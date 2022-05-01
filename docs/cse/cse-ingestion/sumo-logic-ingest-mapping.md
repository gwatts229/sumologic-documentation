---
id: sumo-logic-ingest-mapping
---

# Configure a Sumo Logic Ingest Mapping

This topic has instructions for creating a CSE ingest mapping for a data source. An ingest mapping gives CSE the information it needs in order to map message fields to Record attributes. These are referred to as mapping hints, and include: Format, Vendor, Product, and Event ID Pattern.

:::note
The use of ingest mappings is recommended only if there is no Sumo Logic parser or Cloud-to-Cloud connector for the target data source. For more information, see [CSE Ingestion Best Practices](cse-ingestion-best-practices.md).
:::

## Before you start

Before you create ingest mapping for your messages, you need to determine the information described in the following subsections.

### How are your log messages formatted?

You need to know how your messages are formatted. CSE supports messages in the following formats:

* Unstructured messages with a syslog header
* Unstructured messages without a syslog header
* JSON messages without a syslog header
* JSON messages with a syslog header
* CEF or LEEF messages with a syslog header
* CEF or LEEF messages without a syslog header
* Structured syslog data (key-value pairs) with a syslog header
* Microsoft Windows event logs in XML format
* Winlogbeats
* Messages that have been processed by Sumo Logic [Field Extraction
    Rules](/docs/manage/field-extractions).

### Determining Product, Vendor, and Event ID pattern

When you fill out the **Sumo Logic Ingest Mapping** page, for most of the supported message formats, all you need to select a value for **Format**. However, for the following formats, you also need to tell CSE the **Product**, **Vendor**, and **Event ID template** for the messages:

* JSON messages without a syslog header
* JSON messages with a syslog header
* Structured syslog data (key-value pairs) with a syslog header
* Messages that have been processed by Sumo Logic Field Extraction Rules.

For these formats, CSE uses the values you configure for **Product**, **Vendor**, and **Event ID** (in addition to **Format**) to select the appropriate CSE mapper to process the messages. To verify the correct values, you can go to the **Log Mapping Details** page for the mapper in the CSE UI. To do so:

1. In the CSE UI, click the gear icon, then the **Log Mappings** link.   
    ![log-mappings-link.png](/img/cloud-siem-enterprise/log-mappings-link.png)
1. The **Log Mappings** page displays a list of mappers.  
    ![log-mappings-page.png](/img/cloud-siem-enterprise/log-mappings-page.png)
1. In the **Filters** area, you can filter the list of log mappings by
    typing in a keyword, or by selecting a field to filter by.  
    ![log-mapping-filters.png](/img/cloud-siem-enterprise/log-mapping-filters.png)
1. When you find the mapper you’re looking for, you can find the **Product**, **Vendor**, and **Event ID pattern** for a mapper on the **If Input Matches** side of the **Input/Output** side of the page.
    * **Format**. This is the value labeled **c** in the screenshot below.
    * **Product**. This is the value labeled **b** in the screenshot below.
    * **Vendor**. This is the value labeled **a** in the screenshot below.
    * **Event ID pattern**. This is the value labeled **d** in the screenshot below.  
        ![log-mapping-details.png](/img/cloud-siem-enterprise/mapping.png)

### Quick reference to configuring ingest mappings

This table in this section is a quick reference to supplying values for each supported message format on the **Create Sumo Logic Mapping** page in CSE. This reference summarizes the step-by-step instructions provided below. 

| If your messages are... | Select this option for Format | Are Vendor, Product, and 
Event ID pattern required? | How CSE picks a mapper |
|--|--|--|--|
| Unstructured logs lines with a syslog header | Process Syslog with Valid Header | No | CSE will send the messages to the mapper whose name is the same as the name of the grok pattern the message matches.<br/>This option is NOT recommended because legacy parsers (groks) are being phased out and replaced by Sumo Logic system parsers. Check for a system parser on the **Manage Data > Logs > Parsers** page in Sumo Logic. |
| Unstructured log lines without a syslog header | Do not Process Syslog Header | No | CSE will send the messages to the mapper whose name is the same as the name of the grok pattern the message matches.<br/>This option is NOT recommended because legacy parsers (groks) are being phased out and replaced by Sumo Logic system parsers. Check for a system parser on the **Manage Data > Logs > Parsers** page in Sumo Logic. |
| JSON without a syslog header | JSON | Yes | CSE will send the messages to the log mapper with the **Format**, **Vendor**, **Product**, and **Event ID** pattern you enter in the **Sumo Ingest Mapping**. |
| JSON with a syslog header	Process Syslog with Valid Header | You’ll be prompted to select whether messages are JSON or key-value pairs. Choose “JSON”. | Yes | CSE will send the messages to the log mapper with the **Format**, **Vendor**, **Product**, and **Event ID** pattern you enter in the **Sumo Ingest Mapping**. |
| CEF / LEEF with a syslog header | Process Syslog with Valid Header | No | CSE will send the messages to the log mapper with the **Format**, **Vendor**, **Product**, and **Event ID** from the CEF/LEEF message. |
| CEF / LEEF without a syslog header | Do not Process Syslog Header | No | CSE will send the messages to the log mapper with the **Format**, **Vendor**, **Product**, and **Event ID** from the CEF/LEEF message. |
| Structured syslog data (KV pairs) with syslog header | Process Syslog with Valid Header<br/>You’ll be prompted to select whether messages are JSON or key-value pairs. Choose “Key-Value”. Then supply delimiters. | Yes | CSE will send the messages to the log mapper with the **Format**, **Vendor**, **Product**, and **Event ID** pattern you enter in the Sumo Ingest Mapping. |
| Microsoft Windows event logs in XML | Windows | No | CSE will send a message to the log mapper whose:<br/>**Format** is “Windows”<br/>**Vendor** is “Microsoft”<br/>**Product** is “Windows”<br/>**Event** ID pattern is the value of `{channel}-{eventid}` from the Windows event, for example, “Security-1234”. |
| Winlogbeats | Winlogbeats | No | CSE will send a message to the log mapper whose:<br/>**Format** is “Windows”<br/>**Vendor** is “Microsoft” <br/>**Product** is “Windows” <br/>**Event ID** pattern is the value of `{channel}-{eventid}` from the Windows event, for example, “Security-1234”. |
| Fields extracted from Sumo Logic-ingested messages | Extracted Fields JSON | Yes | CSE will send the messages to the log mapper with the **Format**, **Vendor**, **Product**, and **Event ID** pattern you enter in the Sumo **Ingest Mapping.** |

## Configure Sumo Logic Ingest Mapping in CSE

In this step, you configure a Sumo Logic Ingest Mapping in CSE for the source category assigned to your source or collector you configured. The mapping tells CSE the information it needs to select the right mapper to process messages that have been tagged with that source category. 

1. Click the gear icon, and select **Sumo Logic** under **Integrations**.  
    ![integrations-sumologic.png](/img/cloud-siem-enterprise/integrations-sumologic.png)
1. On the **Sumo Logic Ingest Mappings** page, click **Create**.  
    ![ingest-mappings.png](/img/cloud-siem-enterprise/ingest-mappings.png)
1. On the **Create Sumo Logic Mapping** popup:
    1. **Source Category**. Enter the category you assigned to the HTTP Source or Hosted Collector. 
    1. **Format**. Follow the instructions for the type of messages your source collects:
        * [Unstructured messages with a syslog header](#unstructured-messages-with-a-syslog-header)
        * [Unstructured messages without a syslog header](#unstructured-messages-without-a-syslog-header)
        * [JSON messages without a syslog header](#json-messages-without-a-syslog-header)
        * [JSON messages with a syslog header](#json-messages-with-a-syslog-header)
        * [CEF or LEEF messages with a syslog header](#cef-or-leef-messages-with-a-syslog-header)
        * [CEF or LEEF messages without a syslog header](#cef-or-leef-messages-without-a-syslog-header)
        * [Structured syslog data (key-value pairs) with a syslog header](#structured-syslog-data-key-value-pairs-with-a-syslog-header)
        * [Microsoft Windows event logs in XML format](#microsoft-windows-event-logs-in-xml-format)
        * [Winlogbeats](#winlogbeats)
        * [Fields extracted from Sumo Logic-ingested messages](#fields-extracted-from-sumo-logic-ingested-messages)

### Unstructured messages with a syslog header

If your messages are unstructured with a syslog header, all you need to do is select “Process Syslog with Valid Header” for **Format**. 

CSE applies GROK patterns to unstructured messages to determine which mapper to use, so you don’t need to supply any other configuration options.

![create-mapping-1.png](/img/cloud-siem-enterprise/create-mapping-1.png)

### Unstructured messages without a syslog header

If your messages are unstructured without a syslog header, all you need to do is select “Do not Process Syslog Header” for **Format**. 

CSE applies GROK patterns to unstructured messages to determine which mapper to use, so you don’t need to supply any other configuration options.

![create-mapping-3.png](/img/cloud-siem-enterprise/create-mapping-3.png)

### JSON messages without a syslog header

If your messages are JSON format without a syslog header, there are required and optional configuration settings.

#### Required settings: Format, Vendor, Product, and Event ID

1. For **Format**, select “JSON”. 
1. You must specify values for **Vendor**, **Product**, and **Event ID**, which CSE will use to determine what mapper to use for your messages. If you don’t know these values, see [Determining Product, Vendor, and Event ID pattern](#determining-product-vendor-and-event-id-pattern), above.  

    ![create-mapping-2.png](/img/cloud-siem-enterprise/create-mapping-2.png)

#### Optional settings: Advanced JSON Parsing

If you would like to manipulate the JSON data before it’s flattened and parsed, expand the **Advanced JSON Parsing** section of the popup.

![advanced-json-parsing.png](/img/cloud-siem-enterprise/advanced-json-parsing.png)

1. **JSON Explode**. This option takes a JSON array value (flattened value) and creates multiple copies of the log line, one for each value of the array. You can only apply JSON Explode to one attribute within the JSON. For example, given the following example JSON log:  
      
    `{ “animals” : { “pets” : [“cat”, “dog”], “owned”: “true”}, “kids”: “none”}`  
      
      
    Setting the JSON Explode to `animals.pets` results in the creation of two separate raw log lines:  
      
    `{ “animals” : { “pets” : “dog”, “owned”: “true”}, “kids”: “none”}{ “animals” : { “pets” : “cat”, “owned”: “true”}, “kids”: “none”}`  
      
     
1. **JSON Zip Operations**. Collapses JSON arrays in which key-value
    pairs are repeated with a common key identifier and value
    identifier. For example, given the following JSON array:  
      
    `{ “pets” : [ {“name” : “fluffy”}, {“type”: “cat”}, {“name”: “fido”, “type” : “dog”}, {“name”: “sammy”, “type” : “snake}]}`  
      
    The JSON Zip operation will turn the array into:   
      
    `{ “pets” : { “fluffy” : “cat” , “fido” : “dog”, “sammy” : “snake”}}`  
      
    The JSON Zip parameters are:

* **Key Name**. The name of the attribute whose value is the array to zip.
* **Match Key**. The name of the attribute that represents the key in the output. In the example above, it’s `name`.
* **Match Value**. The attribute in the array object that represents the value in the final output. In the example above it’s `type`.

### JSON messages with a syslog header

If your messages are JSON format with a syslog header:

1. **Format**. Select “Process Syslog with Valid Header”. 
1. **Syslog Format**. Choose “JSON”.
1. You must specify values for **Vendor**, **Product,** and **Event ID**, which CSE will use to determine what mapper to use for your messages. If you don’t know these values, see [Determining Product, Vendor, and Event ID pattern](#determining-product-vendor-and-event-id-pattern).  
      
    ![create-mapping-4.png](/img/cloud-siem-enterprise/create-mapping-4.png)

### CEF or LEEF messages with a syslog header

If your messages are CEF or LEEF messages with a syslog header, all you need to do is select “Process Syslog with Valid Header” for **Format**.

Don’t specify **Syslog Format**. 

Don’t specify **Vendor**, **Product,** or **Event ID**. CSE can determine those values from the CEF or LEEF message itself.

![create-mapping-1.png](/img/cloud-siem-enterprise/create-mapping-1.png)

### CEF or LEEF messages without a syslog header

If your messages are CEF or LEEF messages without a syslog header, all you need to do is select “Do Not Process Syslog Header” for **Format**.

Don’t specify **Syslog Format**. 

Don’t specify **Vendor**, **Product**, or **Event ID**. CSE can determine those values from the CEF or LEEF message itself.

![create-mapping-3.png](/img/cloud-siem-enterprise/create-mapping-3.png)

### Structured syslog data (key-value pairs) with a syslog header

If your messages are structured syslog data (key-value pairs) with a syslog header:

1. **Format**. Select “Process Syslog with Valid Header”. 
1. **Syslog Format**. Choose “Key-Value”.
1. The popup refreshes, with options for syslog delimiters.
1. **Syslog Delimiter**. This is the delimiter between the key-value pairs.
1. **Syslog kv Delimiter**. This is the delimiter between a key and a value.
1. You must specify values for **Vendor**, **Product**, and **Event ID**, which CSE will use to determine what mapper to use for your messages. If you don’t know these values, see [Determining Product, Vendor, and Event ID pattern](#determining-product-vendor-and-event-id-pattern).  
      
    ![syslog-delimiters.png](/img/cloud-siem-enterprise/syslog-delimiters.png)

### Microsoft Windows event logs in XML format

If your messages are Windows event logs in XML format, all you need to do is select “Windows” for **Format**.

CSE will determine the appropriate mapper to use from individual events. It will select the mapper whose:

* **Format** is “Windows”.
* **Vendor** is “Microsoft”.
* **Product** is “Windows”.
* **Event ID** is the value of `{channel}-{eventid}`, for example, “Security-1234”.

![windows.png](/img/cloud-siem-enterprise/windows.png)

### Winlogbeats

If your messages are from Winlogbeats, all you need to do is select “Winlogbeats” for **Format**.

CSE will determine the appropriate mapper to use from individual events. It will select the mapper whose:

* **Format** is “Windows”.
* **Vendor** is “Microsoft”.
* **Product** is “Windows”.
* **Event ID** is the value of `{channel}-{eventid}`, for example, “Security-1234”.

![winlogbeats.png](/img/cloud-siem-enterprise/winlogbeats.png)

### Fields extracted from Sumo Logic-ingested messages

If the messages with the source category you’ve specified in the mapping have had Sumo Logic Field Extraction Rules applied to them:

1. **Format**. Select “Extracted Fields JSON”
1. You must specify values for **Vendor**, **Product**, and **Event ID**, which CSE will use to determine what mapper to use for your messages. If you don’t know these values, see [Determining Product, Vendor, and Event ID pattern](#determining-product-vendor-and-event-id-pattern).  
      
    ![extracted-fields-json.png](/img/cloud-siem-enterprise/extracted-fields-json.png)

## Enable mapping

For CSE to be able to select a mapper for messages from Sumo Logic, a valid ingest mapping must be configured and enabled for the source category associated with incoming messages. 

To enable the mapping you have created, move the **Enabled** slider to “On”.