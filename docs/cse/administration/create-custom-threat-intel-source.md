---
id: create-custom-threat-intel-source
---

# Create a Custom Threat Intel Source

This topic has information about setting up a *custom threat intel
source* in CSE, which is a threat intel list that you can populate
manually, as opposed to using an automatic feed. 

You can set up and populate custom threat intel sources interactively
from the CSE UI, by uploading a .csv file, or using CSE APIs. You can
populate the sources with IP addresses, hostnames, URLs, and file
hashes.

### How CSE uses indicators

When CSE encounters an indicator from your threat source in an incoming
Record it adds relevant information to the Record. Because threat intel
information is persisted within Records, you can reference it downstream
in both rules and search.The built-in rules that come with CSE
automatically create a Signal for Records that have been enriched in
this way.

Rule authors can also write rules that look for threat intel information
in Records. To leverage the information in a rule, you can extend your
custom rule expression, or add a Rule Turning Expression to a built-in
rule. For a more detailed explanation of how to use threat intel
information in rules, see [Threat Intel](../cse-rules/about-cse-rules.md) in the
*About CSE Rules* topic.

### Create a threat intel source from CSE UI

1. Click the Content menu and select **Threat Intelligence**.
1. Click **Add Source** on the **Threat Intelligence** page.  
    ![threat-intel-page2.png](/img/cloud-siem-enterprise/threat-intel-page2.png)
1. Click **Custom** on the **Add Source** popup.  
    ![custom-button.png](/img/cloud-siem-enterprise/custom-button.png)
1. On the **Add New Source** popup, enter a name, and if desired, a
    description for the source.  
    ![add-custom-source.png](/img/cloud-siem-enterprise/add-custom-source.png)
1. Click **Add Custom Source**.

Your new source should now appear on the **Threat Intelligence** page.

### Enter indicators manually

1. On the **Threat Intelligence** page, click the name of the source
    you want to update.  
    ![click-name.png](/img/cloud-siem-enterprise/click-name.png)
1. The **Details** page lists any indicators that have previously been
    added and have not expired. Click **Add Indicator**.  
    ![threat-details.png](/img/cloud-siem-enterprise/threat-details.png)
1. On the **New Threat Intelligence Indicator** popup.
    1. **Value**. Enter an IP address, hostname, URL, or file hash.
        Your entry must be one of:
        * A valid IPV4 or IPv6 address  
        * A valid, complete URL
        * A hostname (without protocol or path)
        * A hexadecimal string of 32, 40, 64, or 128 characters 
    1. **Description**. (Optional)
    1. **Expiration**. (Optional) If desired, you can specify an
        expiration date and time for the indicator. When that time is
        reached, the indicator will be removed from the source. When you
        click in the field, you’ll be prompted to select a date and
        time.
    1. Click **Add**.  
        ![import-indicators.png](/img/cloud-siem-enterprise/import-indicators.png)

### Upload a file of indicators 

If you have a large number of indicators to add to your source, you can
save time by creating a .csv file and uploading it to CSE.

#### Create a CSV file

The .csv file can contain up to three columns, which are described
below. 

[TABLE]

**Example .csv file**

`value,description,expires mvtexco.com,EMEA guidance 22.333.22.252,Tante Intel,2022-06-01 01:00 PM`

### Upload the file

1. On the **Threat Intelligence** page, click the name of the target
    custom source.
1. Click **Import Indicators**.
1. On the import popup:
    1. Drag your file onto the import popup, or click to navigate to
        the file, and then click Import.
    1. Optionally, you can enter an expiration for the indicators on
        the list. If you do, it will override any expirations that are
        defined in the file. Enter the expiration in any ISO date
        format. For example:  
        `2022-12-31`

### Manage sources and indicators using APIs

You can use CSE threat intel APIs to create and manage indicators and
custom threat sources. For information about CSE APIs and how to access
the API documentation, see [CSE APIs](cse-apis.md).  
 