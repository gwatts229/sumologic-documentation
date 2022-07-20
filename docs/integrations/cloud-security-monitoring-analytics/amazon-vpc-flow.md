---
id: amazon-vpc-flow
title: Amazon VPC Flow
---

The Amazon VPC (Virtual Private Cloud) Flow - Cloud Security Monitoring and Analytics app thoroughly assess Amazon VPC Flow logs to gain a better understanding of your environment and associated traffic patterns. Dig deep into the data, broken down by access levels, group creation, etc.

The Amazon VPC Flow Logs show the IP network traffic of your VPC, allowing you to troubleshoot traffic and security issues. The Amazon VPC Flow Logs App leverages this data to provide real-time visibility and analysis of your environment. It consists of predefined searches and Dashboards.

For more information on Amazon VPC Flow Logs, see http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/flow-logs.html

The VPC Flow Logs can be published to Amazon CloudWatch Logs and Amazon S3. You can use either of the below methods to collect Amazon VPC Flow Logs.

Each method has advantages. Using an AWS S3 source is more reliable, while using a CloudWatch Logs source with the CloudFormation template allows you to optimize your logs. With the CloudWatch Logs source  and CloudFormation template, you can customize logs by adding more information and filtering out unwanted data. The Security Groups dashboard utilizes customized logs that are generated from the Lambda function and created with the CloudFormation template from logs sent to CloudWatch Logs.

## Collect Amazon VPC Flow Logs from CloudWatch using CloudFormation

This page has instructions for collecting VPC Flow Logs using a CloudFormation template.  Alternatively, you can [Collect Amazon VPC Flow Logs using AWS S3 Source](https://help.sumologic.com/07Sumo-Logic-Apps/Cloud_Security_Monitoring_and_Analytics/Amazon_VPC_Flow_-_Cloud_Security_Monitoring_and_Analytics/Collect_Logs_for_the_Amazon_VPC_Flow_-_Cloud_Security_Monitoring_and_Analytics/02Collect_Amazon_VPC_Flow_Logs_using_AWS_S3_Source).

This page has instructions for collecting logs for the Amazon VPC Flow Logs app.


### Collection process

The diagram below illustrates the collection process for Amazon VPC Flow Logs. VPC is enabled to send logs to Amazon CloudWatch. A Lambda function subscribes to a CloudWatch Log Group to obtain the flow logs, and then sends the data on to a Sumo Logic HTTP Source on a hosted collector. The AWS resources are created by a Sumo-provided CloudFormation template.


#### Step 1: Enable Amazon VPC Flow Logs

You can enable Amazon Virtual Private Cloud (VPC) Flow Logs from the Amazon Web Services (AWS) Management Console, the AWS Command Line Interface (CLI), or by making calls to the Elastic Compute Cloud (EC2) API.

**To enable Amazon Virtual Private Cloud (VPC) Flow Logs from the AWS console**


1. Go to **VPC management**, and go to the VPC list.
2. Select the VPC.
3. Click **Actions** > **Create Flow Log**.
4. On the **Create Flow Log** page, select a **Role** to use Flow logs.
    1. If you haven't set up IAM permissions, click **Set Up Permissions**.

    2. From the new tab, **VPC Flow Logs is requesting permissions to use resources in your account**:
    3. From the IAM Role, select **Create a new IAM Role.**
    4. Add a Role Name that describes your logs, for example, VPC-Flow-Logs.
    5. Click **Allow**.
5. Back in **Create Flow Log**, enter the new role you created in **Role.**
6. In **Destination Log Group** enter a descriptive name such as **VPCFlowLogs**.
7. Click **Create Flow Log**. It can take up to an hour for the log group to show up in CloudWatch Logs.


#### Step 2: Configure hosted collector and HTTP source

1. [Create a Hosted Collector ](https://help.sumologic.com/03Send-Data/Hosted-Collectors#Create_a_Hosted_Collector)in Sumo Logic.
2. Configure an [HTTP Source](https://help.sumologic.com/03Send-Data/Sources/02Sources-for-Hosted-Collectors/HTTP-Source) in Sumo Logic. When configuring the source:
3. Under **Advanced Options for Logs**, for **Timestamp Format**, click **Specify a format**.
4. **Format**. Enter:  \
`epoch`
5. **Timestamp locator**. Enter: \
` \s(\d{10,13})\s\d{10,13}`  \

6. Click **Save**.


#### Step 3: Create AWS functions and resources  

Follow the steps on [Amazon CloudWatch Logs](https://help.sumologic.com/03Send-Data/Collect-from-Other-Data-Sources/Amazon-CloudWatch-Logs), starting with the [Download the CloudFormation template](https://help.sumologic.com/03Send-Data/Collect-from-Other-Data-Sources/Amazon-CloudWatch-Logs#Download_the_CloudFormation_template) step and ending with the [Dealing with alarms](https://help.sumologic.com/03Send-Data/Collect-from-Other-Data-Sources/Amazon-CloudWatch-Logs#Dealing_with_alarms) step. As you perform the procedure note the additional instructions below, regarding log format and optional environment variables.


#### Configure LogFormat correctly (Required)  

When you [Create a stack on the AWS CloudFormation console](https://help.sumologic.com/03Send-Data/Collect-from-Other-Data-Sources/Amazon-CloudWatch-Logs#Create_a_stack_on_the_AWS_CloudFormation_console), in Step 5, make sure you select either VPC-JSON or VPC-RAW in the LogFormat field in the Specify Details window.


#### Environment variables for VPC flow log collection (Optional)

When you [Configure environment variables for Lambda functions](https://help.sumologic.com/03Send-Data/Collect-from-Other-Data-Sources/Amazon-CloudWatch-Logs#Configure_environment_variables_for_Lambda_functions), in addition to the variables listed, you can optionally also define the following environment variables.

If you define the environment variables below, do it for both of the Lambda functions created by the CloudFormation template.


**table**



#### Grant Lambda permissions (Optional)

This step is supported only if `INCLUDE_SECURITY_GROUP_INFO` is set to true.

The Lambda function fetches list of Elastic Network Interfaces using the `describeNetworkInterfaces` API. You need to grant permission to Lambda by adding the following inline policy in the  `SumoCWLambdaExecutionRole` role. See the instructions on [Creating Policies on the JSON Tab](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html#access_policies_create-json-editor) in AWS help.

Paste the JSON below, after adding the ARN of the Lambda functions.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DescribeENILambdaPerms",
            "Effect": "Allow",
            "Action": "ec2:DescribeNetworkInterfaces",
            "Resource": "*"
        }
    ]
}
```


#### Step 4: Subscribe the Lambda function to the VPC Flow Log group

1. Select the VPC Flow Log group in the CloudWatch Logs management panel. \
This is the Log Group created in the first part (VPCFlowLogs was used).
2. Click **Actions** and select **Stream to Lambda Function**.
3. Select the Lambda function created by the CloudFormation template. Its name starts with "SumoCWLogsLambda".
4. Click **Next**.
5. Select **JSON** for **Log Format**.
6. Click **Next**.
7. Click **Start Streaming**. Wait a few minutes, and check to make sure your logs are flowing into Sumo.


## Collect Amazon VPC Flow Logs using an AWS S3 Source

This page has instructions for collecting Amazon VPC Flow Logs using an AWS S3 source. If you prefer to collect VPC logs using a CloudFormation template, see [Collect Amazon VPC Flow Logs using a CloudFormation Template](https://help.sumologic.com/07Sumo-Logic-Apps/Cloud_Security_Monitoring_and_Analytics/Amazon_VPC_Flow_-_Cloud_Security_Monitoring_and_Analytics/Collect_Logs_for_the_Amazon_VPC_Flow_-_Cloud_Security_Monitoring_and_Analytics/01Collect-Amazon-VPC-Flow-Logs-from-CloudWatch-Using-CloudFormation).


### Step 1: Enable Amazon VPC Flow Logs  

1. You can use an existing S3 bucket, or create a new one, as described in [Create a S3 bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html) in AWS help.
2. Create flow logs for your VPCs, subnets, or network interfaces. For instructions, see [Creating a Flow Log that Publishes to Amazon S3](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs-s3.html#flow-logs-s3-create-flow-log) in AWS help.
3. Confirm that logs are being delivered to the S3 bucket. Log files are saved to the bucket using following folder structure: \
`bucket_ARN/optional_folder/AWSLogs/aws_account_id/vpcflowlogs/region/year/month/day/log_file_name.log.gz`


### Step 2: Configure AWS S3 source  

1. [Grant Access to an AWS S3 Bucket](https://help.sumologic.com/03Send-Data/Sources/02Sources-for-Hosted-Collectors/Amazon-Web-Services/Grant-Access-to-an-AWS-Product).
2. [Enable logging using the AWS Management Console](http://docs.aws.amazon.com/AmazonS3/latest/dev/enable-logging-console.html).
3. When you create an AWS Source, you associate it with a Hosted Collector. Before creating the Source, identify the Hosted Collector you want to use, or create a new Hosted Collector. For instructions, see [Create a Hosted Collector](https://help.sumologic.com/03Send-Data/Hosted-Collectors#Create_a_Hosted_Collector).
4. Add an [AWS Source](https://help.sumologic.com/03Send-Data/Sources/02Sources-for-Hosted-Collectors/Amazon-Web-Services/AWS-S3-Source#AWS_Sources) for the S3 Source to Sumo Logic. When you configure the S3 source:
    1. In the **Advanced Options for Logs** section, uncheck the **Detect messages spanning multiple lines** option.
    2. In the **Processing Rules for Logs** section, add an **Exclude messages that match** processing rule to ignore the following file header lines: \
`version account-id interface-id srcaddr dstaddr srcport dstport protocol packets bytes start end action log-status`


## Install the Sumo Logic App

Now that you have set up collection, install the Sumo Logic App for PCI Compliance For Amazon VPC Flow App to use the preconfigured searches and [Dashboards](https://help.sumologic.com/07Sumo-Logic-Apps/01Amazon_and_AWS/PCI_Compliance_for_Amazon_VPC_Flow_Logs/03AmazonVPCFlowLogs---Application-Dashboards#Dashboards) that provide insight into your data.

**To install the app:**

Locate and install the app you need from the **App Catalog**. If you want to see a preview of the dashboards included with the app before installing, click **Preview Dashboards**.

1. From the **App Catalog**, search for and select the app**.**
2. Select the version of the service you're using and click **Add to Library**.

Version selection is applicable only to a few apps currently. For more information, see the [Install the Apps from the Library](https://help.sumologic.com/01Start-Here/Library/Apps-in-Sumo-Logic/Install-Apps-from-the-Library).


1. To install the app, complete the following fields.
    1. **App Name.** You can retain the existing name, or enter a name of your choice for the app. 
    2. **Data Source.** Select either of these options for the data source. 
        * Choose **Source Category**, and select a source category from the list. 
        * Choose **Enter a Custom Data Filter**, and enter a custom source category beginning with an underscore. Example: (_sourceCategory=MyCategory). 
    3. **Advanced**. Select the **Location in Library** (the default is the Personal folder in the library), or click **New Folder** to add a new folder.
2. Click **Add to Library**.

Once an app is installed, it will appear in your **Personal** folder, or other folder that you specified. From here, you can share it with your organization.

Panels will start to fill automatically. It's important to note that each panel slowly fills with data matching the time range query and received since the panel was created. Results won't immediately be available, but with a bit of time, you'll see full graphs and maps.


### Dashboards

Analytics and Monitoring dashboards to provide operational security for AWS VPC flow data sources.


#### VPC Flow Logs - Security Monitoring - Overview

**Description: **See the details of security group activities and all AWS activities divided by read only and non read only.

**Use Case:** Provides analysis of group activity events including revoking and authorizing access, creating and deleting groups, and other events.


#### VPC Flow Logs - Security Analytics - Accepts & Rejects

**Description: **See the details of security group activities and all AWS activities divided by read only and non read only.

**Use Case:** Provides analysis of group activity events including revoking and authorizing access, creating and deleting groups, and other events.



#### VPC Flow Logs - Security Analytics - Traffic Direction Monitoring

**Description: **See the details of security group activities and all AWS activities divided by read only and non read only.

**Use Case:** Provides analysis of group activity events including revoking and authorizing access, creating and deleting groups, and other events.

**Use Case:** Provides analysis of group activity events including revoking and authorizing access, creating and deleting groups, and other events.