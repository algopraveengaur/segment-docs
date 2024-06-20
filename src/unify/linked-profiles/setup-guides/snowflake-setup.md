---
title: Snowflake Setup
plan: unify
beta: true
hidden: true
---

> info "Linked Profiles is in public beta"
> Linked Profiles (Data Graph, Linked Events, and Linked Audiences) is in public beta, and Segment is actively working on this feature. Some functionality may change before it becomes generally available.

On this page, you'll learn how to connect your Snowflake data warehouse to Segment.

Log in to Snowflake with admin privileges to provide Segment Linked Profiles with the necessary permissions below.

> info ""
> Both Linked Events and Linked Profiles support Snowflake. 
 
## Required connection settings within Segment

Segment requires the following settings to connect to your Snowflake warehouse.

<img src="/docs/unify/images/snowflake-setup.png" alt="Connect Snowflake to the Data Graph" width="5888"/>

- **Account ID**: The Snowflake account ID that uniquely identifies your organization account.
- **Database Name**: The only database that Segment requires write access to in order to create tables for internal bookkeeping. This database is referred to as `segment_connection_db` in the script below.
- **Warehouse**: The [warehouse](https://docs.snowflake.com/en/user-guide/warehouses){:target="_blank”} in your Snowflake account that you want to use for Segment to run the SQL queries. This warehouse is referred to as `segment_connection_warehouse` in the script below.
- **Username**: The Snowflake user that Segment uses to run SQL in your warehouse. This user is referred to as `segment_connection_username` in the script below.
- **Password**: The password of the user above. This password is referred to as `segment_connection_password` in the script below.

## Set up Snowflake credentials

Segment recommends setting up a new Snowflake user and only giving this user permissions to access the required databases and schemas for Segment Linked Profiles.

### Create Segment user and internal database 

Use the following steps to set up your Snowflake credentials:

- Create a new role and user for Segment Linked Profiles. 
- Grant the Segment user access to the warehouse of your choice. If you'd like to create a new warehouse, uncomment the SQL below.
- Create a new database for Segment Linked Profiles. Segment only requires write access to this one database to create a schema for internal bookkeeping, and to store checkpoint tables for the queries that are executed. Segment recommends creating an empty database for this purpose using the script below. This is also the database you'll be required to specify for the "Database Name" when connecting Snowflake with the Segment app.

```
-- ********** SET UP THE FOLLOWING WAREHOUSE PERMISSIONS **********
-- Edit the following variables
SET segment_connection_username='SEGMENT_LINKED_USER';
SET segment_connection_password='my-safe-password';
SET segment_connection_warehouse='SEGMENT_LINKED_WH';
SET segment_connection_role='SEGMENT_LINKED_ROLE';

-- The DB  used for Segment's internal bookkeeping. Note: Use this DB in the connection settings on the Segment app. This is the only DB that Segment requires write access to.
SET segment_connection_db = 'SEGMENT_LINKED_PROFILES_DB';

-- ********** [OPTIONAL] UNCOMMENT THE CODE BELOW IF YOU NEED TO CREATE A NEW WAREHOUSE **********
-- CREATE WAREHOUSE IF NOT EXISTS identifier($segment_connection_warehouse)
-- WITH WAREHOUSE_SIZE = 'XSMALL'
--   WAREHOUSE_TYPE = 'STANDARD'
--   AUTO_SUSPEND = 600 -- 5 minutes
--   AUTO_RESUME = TRUE;


-- ********** RUN THE COMMANDS BELOW TO FINISH SETTING UP THE WAREHOUSE PERMISSIONS **********

-- Use admin role for setting grants
USE ROLE ACCOUNTADMIN;

-- Create a role for Segment Linked Profiles
CREATE ROLE IF NOT EXISTS identifier($segment_connection_role)
COMMENT = 'Used for Segment Linked Profiles';

-- Create a user for Segment Linked Profiles
CREATE USER IF NOT EXISTS identifier($segment_connection_username)
MUST_CHANGE_PASSWORD = FALSE
DEFAULT_ROLE = $segment_connection_role
PASSWORD=$segment_connection_password
COMMENT='Segment Linked Profiles User'
TIMEZONE='UTC';

-- Grant permission to the role to use the warehouse
GRANT USAGE ON WAREHOUSE identifier($segment_connection_warehouse) TO ROLE identifier($segment_connection_role);

-- Grant role to the user
GRANT ROLE identifier($segment_connection_role) TO USER identifier($segment_connection_username);

-- Create and Grant access to a Segment internal DB used for bookkeeping. This is the only DB that Segment requires write access to. This is also the DB you will use in the "Database Name" config while setting up the connection in the Segment app. 
CREATE DATABASE IF NOT EXISTS identifier($segment_connection_db);
GRANT USAGE ON DATABASE identifier($segment_connection_db) TO ROLE identifier($segment_connection_role);
GRANT USAGE ON ALL SCHEMAS IN DATABASE identifier($segment_connection_db) TO ROLE identifier($segment_connection_role);
GRANT CREATE SCHEMA ON DATABASE  identifier($segment_connection_db) TO ROLE identifier($segment_connection_role);

```

### Grant access to other databases 

Next, give the Segment user **read-only** access to all the other databases you want to use for Linked Profiles. You must grant access to the Profiles Sync database.

Run the SQL query below for **each** database you want to use for Linked Profiles:

```

SET segment_connection_role='SEGMENT_LINKED_ROLE';

-- Change this for each DB you want to access and re-run the SQL below.
SET linked_read_only_database='MARKETING_DB';

GRANT USAGE ON DATABASE identifier($linked_read_only_database) TO ROLE identifier($segment_connection_role);
GRANT USAGE ON ALL SCHEMAS IN DATABASE identifier($linked_read_only_database) TO ROLE identifier($segment_connection_role);
GRANT SELECT ON ALL TABLES IN DATABASE identifier($linked_read_only_database) TO ROLE identifier($segment_connection_role);
GRANT SELECT ON FUTURE TABLES IN DATABASE identifier($linked_read_only_database) TO ROLE identifier($segment_connection_role);
GRANT SELECT ON ALL VIEWS IN DATABASE identifier($linked_read_only_database) TO ROLE identifier($segment_connection_role);
GRANT SELECT ON FUTURE VIEWS IN DATABASE identifier($linked_read_only_database) TO ROLE identifier($segment_connection_role);
GRANT SELECT ON ALL EXTERNAL TABLES IN DATABASE identifier($linked_read_only_database) TO ROLE identifier($segment_connection_role);
GRANT SELECT ON FUTURE EXTERNAL TABLES IN DATABASE identifier($linked_read_only_database) TO ROLE identifier($segment_connection_role);
GRANT SELECT ON ALL MATERIALIZED VIEWS IN DATABASE identifier($linked_read_only_database) TO ROLE identifier($segment_connection_role);
GRANT SELECT ON FUTURE MATERIALIZED VIEWS IN DATABASE identifier($linked_read_only_database) TO ROLE identifier($segment_connection_role);

```

### (Optional) Restrict Snowflake schema access

If you want to restrict access to specific [Snowflake schemas and tables](https://docs.snowflake.com/en/user-guide/security-access-control-privileges#table-privileges){:target="_blank”}, run the following commands: 

```
-- [Optional] Further restrict access to only specific schemas and tables 
SET db='MY_DB';
SET schema='MY_DB.MY_SCHEMA_NAME';
SET segment_connection_role='SEGMENT_LINKED_ROLE';


-- View specific schemas in database
GRANT USAGE ON DATABASE identifier($db) TO ROLE identifier($segment_connection_role);
GRANT USAGE ON SCHEMA identifier($schema) TO ROLE identifier($segment_connection_role);
GRANT SELECT ON ALL TABLES IN SCHEMA identifier($schema) TO ROLE identifier($segment_connection_role);
GRANT SELECT ON FUTURE TABLES IN SCHEMA identifier($schema) TO ROLE identifier($segment_connection_role);
GRANT SELECT ON ALL VIEWS IN SCHEMA identifier($linked_read_only_database) TO ROLE identifier($segment_connection_role);
GRANT SELECT ON FUTURE VIEWS IN SCHEMA identifier($linked_read_only_database) TO ROLE identifier($segment_connection_role);
GRANT SELECT ON ALL EXTERNAL TABLES IN SCHEMA identifier($linked_read_only_database) TO ROLE identifier($segment_connection_role);
GRANT SELECT ON FUTURE EXTERNAL TABLES IN SCHEMA identifier($linked_read_only_database) TO ROLE identifier($segment_connection_role);
GRANT SELECT ON ALL MATERIALIZED VIEWS IN SCHEMA identifier($linked_read_only_database) TO ROLE identifier($segment_connection_role);
GRANT SELECT ON FUTURE MATERIALIZED VIEWS IN SCHEMA identifier($linked_read_only_database) TO ROLE identifier($segment_connection_role);


```

### (If applicable) Update user acccess for Segment Reverse ETL schema 

> warning ""
> This is only applicable if you choose to use an existing database as the Segment connection database that has also been used for Segment Reverse ETL.

Run the following SQL if you run into an error on the Segment app indicating that the user doesn't have sufficient privileges on an existing `_segment_reverse_etl` schema.
 
If Segment Reverse ETL has ever run in the database you are configuring as the Segment connection database, a Segment-managed schema is already created and you need to provide the new Segment user access to the existing schema. 

Add the Snowflake table permissions by running the following commands:

```
-- If you want to use an existing database that already has Segment Reverse ETL schemas, you’ll need to run some additional steps below to grant the role access to the existing schemas.

SET retl_schema = concat($segment_internal_database,'.__segment_reverse_etl');

GRANT USAGE ON SCHEMA identifier($retl_schema) TO ROLE identifier($segment_connection_role);

GRANT CREATE TABLE ON SCHEMA identifier($retl_schema) TO ROLE identifier($segment_connection_role);

GRANT SELECT,INSERT,UPDATE,DELETE ON ALL TABLES IN SCHEMA identifier($retl_schema) TO ROLE identifier($segment_connection_role);

```

### Confirm permissions 

To verify you have set up the right permissions for a specific table, log in with the username and password you created for `SEGMENT_CONNECTION_USERNAME` and run the following command to verify the role you created has the correct permissions. If this command succeeds, you should be able to view the respective table.

```
set segment_connection_role='SEGMENT_LINKED_ROLE';
set linked_read_only_database='YOUR_DB';
set table_name = 'YOUR_DB.SCHEMA.TABLE';

USE ROLE identifier($segment_connection_role);
USE DATABASE identifier($linked_read_only_database) ;
SHOW SCHEMAS;
SELECT * FROM identifier($table) LIMIT 10;

```
