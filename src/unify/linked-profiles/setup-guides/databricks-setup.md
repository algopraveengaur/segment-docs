---
title: Databricks Setup
beta: true
plan: unify
hidden: true
---

> info "Linked Profiles is in public beta"
> Linked Profiles (Data Graph, Linked Events, and Linked Audiences) is in public beta, and Segment is actively working on this feature. Some functionality may change before it becomes generally available.

On this page, you'll learn how to connect your Databricks data warehouse to the Segment Data Graph. 

## Set up Databricks credentials

Sign in to Databricks with admin permissions to create new resources and provide the Data Graph with the necessary permissions. 

Segment assumes that you already have a workspace that includes the datasets you'd like to use for the Data Graph. Segment recommends setting up a new Service Principal user with only the permissions to access the required catalogs and schemas.

### Step 1: Set up a Service Principal user

Segment recommends that you set up a new Service Principal user. If you already have a Service Principal user you'd like to use, grant it "Can use" permissions for your data warehouse and proceed to [Step 2: Create a catalog for Segment to store checkpoint tables](#step-2-create-a-catalog-for-segment-to-store-checkpoint-tables). 

If you want to create a new Service Principal user, complete the following substeps: 

#### Substep 1: Create a new Service Principal user
1. Log in to the Databricks UI as an Admin.
2. Click **User Management**. 
3. Select the **Service principals** tab. 
4. Click **Add Service Principal**.
5. Enter a Service Principal user name and click **Add**. 
6. Select the Service Principal user you just created and click **Generate secret**.
7. Save the **Secret** and **Client ID** to a safe place. You'll need these values to connect your Databricks warehouse to Segment. 
8. Navigate to Workspaces and select your Workspace. 
9. Select the “Permissions” tab and click **Add Permissions**. 
10. Add the newly created Service Principal user and click **Save**. 

> success ""
> If you already have a warehouse you'd like to use, you can move on to the next substep, [Substep 2: Add your Service Principal user to Warehouse User Lists](#substep-2-add-your-service-principal-user-to-warehouse-user-lists). If you need to create a new warehouse first, see the [Create a new warehouse](#create-a-new-warehouse) before completing the next substep. 

#### Substep 2: Add your Service Principal user to Warehouse User Lists 
1. Log in to the Databricks UI as an Admin.
2. Navigate to SQL Warehouses. 
3. Select your warehouse and click **Permissions**. 
4. Add the Service Principal user and grant them “Can use” access. 
5. Click **Add**. 

##### (Optional) Confirm Service Principal permissions
Confirm that the Service Principal user that you're using to connect to Segment has "Can use" permissions for your warehouse. 

To confirm that your Service Principal user has "Can use" permission: 
1. In the Databricks console, navigate to SQL Warehouses and select your warehouse. 
2. Navigate to Overview and click **Permissions**. 
3. Verify that the Service Principal user has "Can use" permission. 

### Step 2: Create a catalog for Segment to store checkpoint tables

> warning "Segment recommends creating an empty catalog for the Data Graph"
> If you plan to use an existing catalog with Reverse ETL, follow the instructions in the [Update user access for Segment Reverse ETL catalog](#update-user-access-for-segment-reverse-etl-catalog) section.
 
Segment requires write access to a catalog to create a schema for internal bookkeeping, and to store checkpoint tables for the queries that are executed. 

Segment recommends creating an empty catalog for this purpose by running the following SQL. This is also the catalog that you'll be required to specify when setting up your Databricks integration in the Segment app. 

```sql
CREATE CATALOG IF NOT EXISTS `SEGMENT_LINKED_PROFILES_DB`;
-- Copy the Client ID by clicking “Generate secret” for the Service Principal user
GRANT USAGE ON CATALOG `SEGMENT_LINKED_PROFILES_DB` TO `${client_id}`;
GRANT CREATE ON CATALOG `SEGMENT_LINKED_PROFILES_DB` TO `${client_id}`;
GRANT SELECT ON CATALOG `SEGMENT_LINKED_PROFILES_DB` TO `${client_id}`;
```

### Step 3: Grant read-only access to the Profiles Sync catalog

Run the following SQL to grant the Data Graph read-only access to the Profiles Sync catalog:

```sql
GRANT USAGE, SELECT, USE SCHEMA ON CATALOG `${profiles_sync_catalog}` TO `${client_id}`;
```

### Step 4: Grant read-only access to additional catalogs for the Data Graph
Run the following SQL to grant your Service Principal user read-only access to any additional catalogs you want to use for the Data Graph: 

```sql 
-- Run this command for each catalog you want to use for the Segment Data Graph
GRANT USAGE, SELECT, USE SCHEMA ON CATALOG `${catalog}` TO `${client_id}`;
```

### (Optional) Restrict read-only access to schemas

Restrict access to specific schemas by running the following SQL:

```sql
GRANT USAGE ON CATALOG `${catalog}` TO `${client_id}`;
USE CATALOG `${catalog}`;
GRANT USAGE, SELECT ON SCHEMA `${schema_1}` TO `${client_id}`;
GRANT USAGE, SELECT ON SCHEMA `${schema_2}` TO `${client_id}`;
...

```

### (Optional) Restrict read-only access to tables
Restrict access to specific tables by running the following SQL: 

```sql
GRANT USAGE ON CATALOG `${catalog}` TO `${client_id}`;
USE CATALOG `${catalog}`;
GRANT USAGE ON SCHEMA `${schema_1}` TO `${client_id}`;
USE SCHEMA `${schema_1}`;
GRANT SELECT ON TABLE `${table_1}` TO `${client_id}`;
GRANT SELECT ON TABLE `${table_2}` TO `${client_id}`;
...

```

### Step 5: Validate the permissions of your Service Principal user

Sign in to the [Databricks CLI with your Client ID secret](https://docs.databricks.com/en/dev-tools/cli/authentication.html#oauth-machine-to-machine-m2m-authentication){:target="_blank”} and run the following SQL to verify the Service Principal user has the correct permissions for a given table. 

> success ""
> If this command succeeds, you can view the table. 

```sql
USE DATABASE ${linked_read_only_database} ;
SHOW SCHEMAS;
SELECT * FROM ${schema}.${table} LIMIT 10;
```

### Step 6: Connect your warehouse to Segment

Segment requires the following settings to connect to your Databricks warehouse. You can find these details in your Databricks workspace by navigating to **SQL Warehouse > Connection details**.

- **Hostname**: The address of your Databricks server
- **Http Path**: The address of your Databricks compute resources
- **Port**: The port used to connect to your Databricks warehouse. The default port is 443, but your port might be different. 
- **Catalog**: The catalog you designated in [Step 2: Create a catalog for Segment to store checkpoint tables](#step-2-create-a-catalog-for-segment-to-store-checkpoint-tables)
- **Service principal client ID**: The client ID used to access to your Databricks warehouse
- **OAuth secret**: The OAuth secret used to connect to your Databricks warehouse

After identifying the following settings, continue setting up the Data Graph by following the instructions in [Connect your warehouse to the Data Graph](/docs/unify/linked-profiles/data-graph/#step-2-connect-your-warehouse-to-the-data-graph). 

## Additional set up for warehouse permissions

### Update user access for Segment Reverse ETL catalog
Run the following SQL if you run into an error on the Segment app indicating that the user doesn’t have sufficient privileges on an existing `_segment_reverse_etl` schema.

If Segment Reverse ETL has ever run in the catalog you are configuring as the Segment connection catalog, a Segment-managed schema is already created and you need to provide the new Segment user access to the existing schema. Update the Databricks table permissions by running the following SQL:

```sql
GRANT ALL PRIVILEGES ON SCHEMA ${segment_internal_catalog}.__segment_reverse_etl TO `${client_id}`;
```