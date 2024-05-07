---
title: Listrak (Actions) Destination
id: 64b6a221baf168a989be641a
---

{% include content/plan-grid.md name="actions" %}

[Listrak](https://www.listrak.com/?utm_source=segmentio&utm_medium=docs&utm_campaign=partners){:target="_blank”} is the retail industry’s leading customer engagement platform. Listrak delivers results for more than 1,000 retailers by providing best-in-class email, text message marketing, identity resolution marketing and push notifications through seamless cross-channel orchestration. Listrak’s data-first approach delivers 1:1 personalization at scale so you can send messages at precisely the right time across the right combination of channels and devices to maximize customer engagement, revenue, and lifetime value.

Listrak maintains this destination. For any issues with the destination, [contact the Listrak Support team](mailto:support@listrak.com).

## Getting started

To add the Listrak Actions destination: 

1. Set up the [Listrak Source](/docs/connections/sources/catalog/cloud-apps/listrak/) first before connecting to the Listrak Actions Destination. Note the API client ID and client secret after creating the integration in Listrak.
2. From your Segment workspace, go to **Connections > Catalog** and select the **Destinations** tab.
3. Search for **Listrak (Actions)** in the Catalog and select the destination.
4. Click **Add destination**.
5. On the **Select data source** step, select your desired source. The source should not be a Listrak source. If you want to sync an Engage Audience, select the Engage space as the source. Click **Confirm Source**.
6. On the **Settings** tab, name your destination. For example, `Listrak`.
7. In the same section of the **Settings** tab, enter your Listrak API client ID and client secret.
8. Click **Save Changes**.
9. Follow the steps in the Destinations Actions documentation to [customize mappings](/docs/connections/destinations/actions/#customize-mappings) or follow the steps below to Sync an Engage Audience.
10. Enable the destination and click **Save Changes**.

### Sync an Engage Audience

To sync an Engage audience with your Listrak (Actions) destination:

1. Each Engage audience to be synced to Listrak must only include email addresses subscribed to the list. To do this, add a condition to the Engage audience that ensures the custom trait for the list exists (eg. have a Custom Trait listrak_list_12345 exists, where 12345 is the list ID).
2. In Listrak, go to **Contacts > Profile Fields** and click **Create Field Group**. 
3. Enter a name for the Profile Field Group (eg. `Engage Audiences`) and Click **Save**.
4. Enter a name for the audience for the **Field Name**.
5. Select **Check Box** for the **Data Type**.
6. Click the **Update** button.
7. Go to **Help & Support > API ID Information** and note the list ID and profile field ID.
8. In Segment, open the previously created Listrak destination.
9. In the **Mappings** tab, click **New Mapping** and select **Update Email Contact Profile Fields**.
10. Under **Select events to map and send**, select **Track** for the **Event Type**.  
11. Click **Add Condition** and add this condition: **Event Name** is `Audience Entered`.
12. Click **Add Condition** and add this condition: **Event Property** `audience_key` is `my_audience` (where `my_audience` is the Audience Key found on the Audience settings page).
13. Under **Select mappings**, enter the list ID and map the email address if `context.traits.email` is not desired.
14. Under **Select mappings**, in the section for mapping the `Profile Field Values`, enter the profile field ID for the `Enter Key Name` textbox on the right and `on` in the textbox for its value to the left. Click **Save**.
15. Repeat steps 9 through 14 using `Audience Exited` instead of `Audience Entered` in step 11 and `off` instead of `on` in step 14.
16. **Enable** both mappings.
17. Go to the **Settings** tab for the destination and **Enable** the destination. Click **Save Changes**.
18. Select the Engage space and navigate to **Engage > Audiences**. Select the source audience to send to the Listrak destination.
19. Click **Add Destination** and select the Listrak Audience destination. 
20. In the settings that appear on the right-hand side, toggle the **Send Track** option on and disable **Send Identify**.
21. Click **Save**.
22. To filter email sends in Listrak using the new audience profile field, see the [help article](https://help.listrak.com/en/articles/3951597-introduction-to-building-filter-2-0-segments){:target="_blank”}.
23. Repeat steps 1 through 21, if you want to sync another audience.

{% include components/actions-fields.html %}

---
