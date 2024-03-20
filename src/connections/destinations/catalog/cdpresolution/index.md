---
title: CDP Resolution (Browser) Destination
id: 650c69e7f47d84b86c120b4c
beta: true
---


{% include content/plan-grid.md name="actions" %}

[CDP Resolution](https://cdpresolution.com?utm_source=segmentio&utm_medium=docs&utm_campaign=partners){:target="_blank"} helps customers instantly match visitor website traffic to full profiles. It turns your anonymous web traffic into full company and buyer profiles — complete with PII and firmographics data, and much more. You can find a [list of the different attributes](https://cdpresolution.com/theattributes?utm_source=segmentio&utm_medium=docs&utm_campaign=partners){:target="_blank"} you can collect with CDP Resolution.

This destination is maintained by CDP Resolution. For any issues with the destination, [contact the CDP Resolution support team](mailto:support@cdpresolution.com).

How this works: A visitor lands on a digital property that has the segment.io analytics.js script connected to the CDP Resolution (Browser) Destination enabled.  For each session, the anonymous ID is sent to CDP Resolution to check if our cookie is present on the browser.  This allows CDP Resolution to resolve the cookie against our graph. If found, the profile and firmographics data are sent to segment.io against a source that is configured within CDP Resolution platform.

## Getting started

To set up the CDP Resolution destination:
1.	Navigate to **Connections > Catalog** in the Segment app and select the **Destinations** tab of the catalog. 
2.	Search for *CDP Resolution* and select it.
3. Choose which of your sources to connect the destination to.
4.	In the Settings, enter your CDP Resolution API key. You can find this in the CDP Connector Setting section of your [CDP Resolution Dashboard Connection Settings](https://app.cdpresolution.com/administration/cdp-connections/segment-io-f4241?utm_source=segmentio&utm_medium=docs&utm_campaign=partners){:target="_blank"}.
5. Go to the CDP Resolution UI. 
5. Go to the [CDP Resolution Connectors](https://app.cdpresolution.com/administration/cdp-connections?utm_source=segmentio&utm_medium=docs&utm_campaign=partners){:target="_blank"} page and select the Segment IO connector.
2.	Paste your CDP Resolution API key in Segment to generate your Write Key.
3.	Paste your Write Key into CDP Resolution's connection configuration.
4.	Click **Upload Key**.

Further documentation can be found on the [CDP documentation site](https://docs.cdpresolution.com?utm_source=segmentio&utm_medium=docs&utm_campaign=partners){:target="_blank"}.

If you have configured your CDP Resolution Destination correctly, and if you've also configured CDP Resolution to send user profile data to a Segment Source, you should start to see user profile data shown in the Segment Source debugger as identify() and group() calls.

{% include components/actions-fields.html %}

