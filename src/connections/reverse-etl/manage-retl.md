---
title: Manage Reverse ETL Syncs
beta: false
---

View your sync history, gain insights into sync statuses, and restart or replay failed or partially successful syncs.

## Sync history
Check the status of your data extractions and see details of your syncs. Click into failed records to view additional details on the error, sample payloads to help you debug the issue, and recommended actions.

To check the status of your extractions:
1. Navigate to **Connections > Destinations** and select the **Reverse ETL** tab.
2. Select the destination you want to view.
3. Select the mapping you want to view.  
4. Click the sync you want to view to get details of the sync. You can view:
    * The status of the sync.
    * Details of how long it took for the sync to complete.
    * How many total records were extracted, as well as a breakdown of the number of records added, updated, and deleted.
    * The load results - how many successful records were synced as well as how many records were updated, deleted, or are new.
5. If your sync failed, click the failed reason to get more details on the error and view sample payloads to help troubleshoot the issue.

> info "Segment automatically retries events that were extracted but failed to load"
> Segment retries events for 14 days following a total or partial sync failure. Before loading the failed records on a subsequent sync, Segment checks for the latest changes in your data to ensure the data loaded into your warehouse isn't stale. If the error causing the load failure is coming from an upstream tool, you can fix the error in the upstream tool to ensure the record loads on the next sync. 

## Reset syncs
You can reset your syncs so that your data is synced from the beginning. This means that Segment resyncs your entire dataset for the model.

To reset a sync:
1. Select the three dots next to **Sync now**.
2. Select **Reset sync**. 
3. Select the checkbox that you understand what happens when a sync is reset.
4. Click **Reset sync**.

## Replays
You can choose to replay syncs. To replay a specific sync, contact [friends@segment.com](mailto:friends@segment.com). Keep in mind that triggering a replay resyncs all records for a given sync.

## Alerting
You can opt in to receive email, Slack, and in-app alerts about Reverse ETL sync failures and partial successes. 

To subscribe to alerts: 
1. Navigate to **Settings > User Preferences**. 
2. Select **Reverse ETL** in the **Activity Notifications** section.
3. Click the Reverse ETL sync status that you'd like to receive notifications for. You can select one or more of the following sync statuses:
    - **Reverse ETL sync failed**: Receive a notification when your Reverse ETL sync fails.
    - **Reverse ETL sync partial success**: Receive a notification when your Reverse ETL sync is partially successful.
4. Select one or more of the following alert options: 
    - **Enable email notifications**: Enter an email address or alias that should receive alerts.
    - **Enable Slack notifications**: Enter a Webhook URL and Slack channel name.
    - **Enable in-app notifications**: Select this option to see an in-app notification.
5. Click **Create alert**.

> success ""
> If you opted to receive notifications by email, you can click **View active email addresses** to see the email addresses that are currently signed up to receive notifications. 

## Supported object and arrays 

When you set up destination actions in Reverse ETL, depending on the destination, some [mapping fields](/docs/connections/reverse-etl/setup/#step-4-create-mappings) may require data to be in the form of an [object](/docs/connections/reverse-etl/manage-retl/#object-mapping) or [array](/docs/connections/reverse-etl/manage-retl/#array-mapping).

Some non-required mapping fields also support [Null value management](#null-value-management), or the ability to opt-out of sending null values to your downstream destinations. 

### Object mapping
You can send data to a mapping field that requires object data. An example of object mapping is an `Order completed` model with a `Products` column that’s in object format. 

Example: 
    
    {
        "product": {
            "id": 0001,
            "color": "pink",
            "name": "tshirt",
            "revenue": 20,
            "inventory": 500
        }
    }

To send data to a mapping field that requires object data, you can choose between these two options: 

Option | Details
------ | --------
Customize object | This enables you to manually set up the mapping fields with any data from the model. If the model contains some object data, you can select properties within the object to set up the mappings as well.
Select object | This enables you to send all nested properties within an object. The model needs to provide data in the format of the object. 

> success ""
> Certain object mapping fields have a fixed list of properties they can accept. If the names of the nested properties in your object don't match with the destination properties, the data won't send. Segment recommends you to use **Customize Object** to ensure your mapping is successful.


### Array mapping
To send data to a mapping field that requires array data, the model must provide data in the format of an array of objects. An example is an `Order completed` model with a `Product purchased` column that’s in an array format.

Example: 

    
    [
    {
        "currency": "USD",
        "price": 40,
        "productName": "jacket",
        "purchaseTime": "2021-12-17 23:43:47.102",
        "quantity": 1
    },
    {
        "currency": "USD",
        "price": 5,
        "productName": "socks",
        "quantity": 2
    }
    ]
    

To send data to a mapping field that requires array data, you can choose between these two options: 

Option | Details
------ | --------
Customize array | This enables you to select the specific nested properties to send to the destination. 
Select array | This enables you to send all nested properties within the array.

> success ""
> Certain array mapping fields have a fixed list of properties they can accept. If the names of the nested properties in your array don't match the destination properties, the data won't send. Segment recommends you to use the **Customize array** option to ensure your mapping is successful.

Objects in an array don't need to have the same properties. If a user selects a missing property in the input object for a mapping field, the output object will miss the property.

### Null value management

You can choose to exclude null values from *optional* mapping fields in your syncs to some destinations. Excluding null values helps you maintain data integrity in your downstream destinations, as syncing a null value for an *optional* field may overwrite an existing value in your downstream tool. For example, if you opt to sync null values with your destination and an end user fills out a form but chooses to leave an optional telephone number field blank, the exising telephone number you have on file in your destination could be overwritten with the null value. By opting out of null values for your downstream destination, you would preserve the existing telephone number in your destination. 

By default, Segment syncs null values from mapped fields to your downstream destinations. Some destinations do not allow the syncing of null values, and will reject requests that contain them. Segment disables the option to opt out of syncing null values for these destinations.

To opt out of including null values in your downstream syncs: 
1. Navigate to **Connections > Destinations** and select the **Reverse ETL** tab.
2. Select the destination and the mapping you want to edit.
3. Click **Edit mapping**.
4. Under the "Optional fields" header, select the field you want to edit.
5. In the field dropdown selection, disable the **Sync null values** toggle. 

> success ""
> If the toggle appears dimmed, your downstream destination rejects requests that contain null values, and you are automatically opted out of syncing null values.