---
id: jira-server
title: Webhook Connection for Jira Server
sidebar_label: Jira Server
---

You can send JSON payloads from Sumo Logic alerts as an HTTP POST request to create Jira issues in Jira Server. Learn more about the Jira REST API in their [Jira Cloud platform Developer Reference](https://developer.atlassian.com/cloud/jira/platform/rest/v2/).

[Webhook connections](/docs/manage/connections-and-integrations/webhook-connections/set-up-webhook-connections) rely on HTTP endpoints that tell Sumo Logic where to send data. You can set up any number of connections.

## Prerequisite

To send webhook alerts to Jira Server you need to include a Basic Authentication Header with requests.

1. Get the username and password of a user that has permissions to create issues in your Jira Server.
1. Using the username and password you can generate the Basic Authentication Header. In the following cURL command replace `<me@example.com>` with your username and `<password>` with your password and run it:

    ```bash
    curl -v https://mysite.atlassian.net --user <me@example.com>:<password>
    ```
       
1. Your response should look like the following image. You'll need the **Authorization** value when configuring the connection in Sumo Logic.

    ![Atlassian Basic Authentication.png](/img/connection-and-integration/Atlassian-Basic-Authentication.png)

## Configuration in Sumo Logic

In Sumo Logic, Scheduled Searches and Monitors send alerts to other tools via webhook connections. To send alerts from Sumo Logic to Jira Server:

1. [Create a Webhook Connection](#create-a-webhook-connection).
1. Use the Webhook Connection as the Alert Type in a [Scheduled Search](/docs/manage/connections-and-integrations/webhook-connections/schedule-searches-webhook-connections.md) or the Connection Type in a [Monitor](/docs/alerts/monitors).

## Create a Webhook Connection

You need the **Manage connections** [role capability](/docs/manage/users-and-roles/roles/role-capabilities.md) to create webhook connections.

1. Go to **Manage Data \> Monitoring \> Connections**.
1. On the **Connections** page click the **+** icon at the top-right of the table.
1. Select the **Jira** option. In the **Create Jira Connection** dialog, fill out connection information.

    ![Jira webhook button.png](/img/connection-and-integration/Jira-webhook-button.png)

1. Enter a **Name** for the Connection.
1. (Optional) Enter a **Description** for the Connection.
1. Enter a **URL** from the Jira REST API to create issues. For example, to create an issue:

    ```
    https://<jira_instance>/rest/api/2/issue
    ```

    ```note
    See the [Jira Cloud platform Developer Reference](https://developer.atlassian.com/cloud/jira/platform/rest/v2/#api-group-Issues) for details on acceptable request URLs.
    ```

1. Enter your **Authorization Header** from the prerequisite. It should be in the format: `Basic <random string>`
1. (Optional) **Custom Headers**, enter up to five comma separated key-value pairs.
1. The following input fields are automatically updated in the JSON **Payload** and vice versa. Update them to meet your requirements. At a minimum, you'll need to enter a valid Project Key.

   * **Issue Type**
   * **Project Key**
   * **Issue Summary**
   * **Priority** (optional)
   * **Issue Description**

1. The following JSON is an example of the default **Payload**, you can customize it as needed. For details on the variables you can use as parameters within your JSON object, see [Webhook Payload Variables](docs/manage/connections-and-integrations/webhook-connections/set-up-webhook-connections).

    ```
    {
    "fields": {
        "issuetype": {
        "name": "Bug"
        },
        "project": {
        "key": "Sumo_Logic
        },
        "summary": "{{Name}}",
        "priority": {
        "id": "3"
        },
        "description": "{{QueryUrl}}"
    }
    }
    ```

    Details on how to format your payload are available in the [Jira Cloud platform Developer Reference](https://developer.atlassian.com/cloud/jira/platform/rest/v2/#api-group-Issues).

1. Click **Save**.

## Create a Scheduled Search

Scheduled searches are saved searches that run automatically at specified intervals. When a scheduled search is configured to send an alert, it can be sent to another tool using a webhook connection.

To set up a scheduled search for a webhook connection follow the steps in the [Schedule Searches for Webhook Connections](/docs/manage/connections-and-integrations/webhook-connections/schedule-searches-webhook-connections.md) document.