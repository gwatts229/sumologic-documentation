---
id: mariadb
title: Sumo Logic App for MariaDB
---

The MariaDB app is a unified logs and metrics app that helps you monitor MariaDB database cluster availability, performance, and resource utilization. Pre-configured dashboards and searches provide insight into the health of your database clusters, performance metrics, resource metrics, schema metrics, replication, error logs, slow queries, Innodb operations, failed logins, and error logs.

This App is tested with the following MariaDB versions:

Non-Kubernetes: MariaDB  - Version 10.7.1
Kubernetes: MariaDB - Version 10.5.11

## Collect Logs and Metrics for the MariaDB App

### Collection Process Overview

Configuring log and metric collection for the MariaDB App includes the following tasks:

* Configure Fields in Sumo Logic.
* Configure Collection for MariaDB
    * [Collect Logs and Metrics for Non-Kubernetes environments.](https://help.sumologic.com/07Sumo-Logic-Apps/12Databases/MariaDB/Collect_Logs_and_Metrics_for_the_MariaDB_App/Collect_MariaDB_Logs_and_Metrics_for_Non-Kubernetes_environments)
    * [Collect Logs and Metrics for Kubernetes environments.](https://help.sumologic.com/07Sumo-Logic-Apps/12Databases/MariaDB/Collect_Logs_and_Metrics_for_the_MariaDB_App/Collect_MariaDB_Logs_and_Metrics_for_Kubernetes_environments)


#### Configure Fields in Sumo Logic

Create the following fields in Sumo Logic before configuring the collection to ensure that your logs and metrics are tagged with relevant metadata, which is required by the app dashboards. For information on setting up fields, see the [Fields](https://help.sumologic.com/Manage/Fields) help page.

If you are using MariaDB in a  non-Kubernetes environment create the fields:

* component
* environment
* db_system
* db_cluster
* pod

If you are using MariaDB in a Kubernetes environment create the fields:
* pod_labels_component
* pod_labels_environment
* pod_labels_db_system
* pod_labels_db_cluster


#### Configure Collection for MariaDB

Sumo Logic supports the collection of logs and metrics data from MariaDB in both Kubernetes and non-Kubernetes environments.

Please click on the appropriate links below based on the environment where your MariaDB clusters are hosted.

* [Collect Logs and Metrics for Non-Kubernetes environments.](https://help.sumologic.com/07Sumo-Logic-Apps/12Databases/MariaDB/Collect_Logs_and_Metrics_for_the_MariaDB_App/Collect_MariaDB_Logs_and_Metrics_for_Non-Kubernetes_environments)
* [Collect Logs and Metrics for Kubernetes environments.](https://help.sumologic.com/07Sumo-Logic-Apps/12Databases/MariaDB/Collect_Logs_and_Metrics_for_the_MariaDB_App/Collect_MariaDB_Logs_and_Metrics_for_Kubernetes_environments)


## Collect MariaDB Logs and Metrics for Kubernetes environments

In a Kubernetes environment, we use the Telegraf Operator, which is packaged with our Kubernetes collection. You can learn more about it[ here](https://help.sumologic.com/03Send-Data/Collect-from-Other-Data-Sources/Collect_Metrics_Using_Telegraf/01_Telegraf_Collection_Architecture). The diagram below illustrates how data is collected from MariaDB  in Kubernetes environments. In the architecture shown below, there are four services that make up the metric collection pipeline:


* Telegraf
* Prometheus
* Fluentd
* FluentBit

The first service in the pipeline is Telegraf. Telegraf collects metrics from MariaDB. Note that we’re running Telegraf in each pod we want to collect metrics from as a sidecar deployment, that is Telegraf runs in the same pod as the containers it monitors. Telegraf uses the [MySQL Input Plugin](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/mysql) to obtain metrics. (For simplicity, the diagram doesn’t show the input plugins.) The injection of the Telegraf sidecar container is done by the Telegraf Operator. We also have Fluentbit that collects logs written to standard out and forwards them to FluentD, which in turn sends all the logs and metrics data to a Sumo Logic HTTP Source.

Follow the below instructions to set up the metric collection:

1. Configure Metrics Collection
    1. Setup Kubernetes Collection with the Telegraf operator
    2. Add annotations on your MariaDB pods
2. Configure Logs Collection
    3. Configure logging in MariaDB.
    4. Add labels on your MariaDB pods to capture logs from standard output.
    5. Collecting MariaDB Logs from a Log file.

**Prerequisites**

These instructions assume that you are using the latest Helm chart version. If not, upgrade using the instructions [here](https://github.com/SumoLogic/sumologic-kubernetes-collection/blob/release-v2.0/deploy/docs/v2_migration_doc.md#how-to-upgrade).


### Configure Metrics Collection

This section explains the steps to collect MariaDB metrics from a Kubernetes environment.

In a Kubernetes environment, we use the Telegraf Operator, which is packaged with our Kubernetes collection. You can learn more on this[ here](https://help.sumologic.com/03Send-Data/Collect-from-Other-Data-Sources/Collect_Metrics_Using_Telegraf/01_Telegraf_Collection_Architecture). Follow the steps listed below to collect metrics from a Kubernetes environment:



1. **[Setup Kubernetes Collection with the Telegraf Operator.](https://help.sumologic.com/03Send-Data/Collect-from-Other-Data-Sources/Collect_Metrics_Using_Telegraf/03_Install_Telegraf#Install_Telegraf_in_a_Kubernetes_environment)**
2. **Add annotations on your MariaDB pods**

On your MariaDB Pods, add the following annotations:


```
annotations:
    telegraf.influxdata.com/class: sumologic-prometheus
    prometheus.io/scrape: "true"
    prometheus.io/port: "9273"
    telegraf.influxdata.com/inputs: |+

[[inputs.mysql]]
  servers = ["user_TO_BE_CHANGED:password_TO_BE_CHANGED@tcp(IP_ADDRESS_MARIADB_TO_BE_CHANGED:PORT_MARIADB_TO_BE_CHANGED)/?tls=false"]
  metric_version = 2
  table_schema_databases = []
  perf_summary_events = []
  gather_table_schema                       = true
  gather_process_list                       = true
  gather_info_schema_auto_inc               = true
  gather_user_statistics = true
  gather_slave_status = true
  gather_table_io_waits = true
  gather_table_lock_waits = true
  gather_index_io_waits = true
  gather_event_waits = true
  gather_file_events_stats = true
  gather_perf_events_statements = true
  interval_slow = "30m"
[inputs.mysql.tags]
    environment="TO_BE_CHANGED"
    component="database"
    db_system="mariadb"
    db_cluster="TO_BE_CHANGED"
```


If you haven’t defined a cluster in MariaDB, then enter ‘**default**’ for `db_cluster`.

Enter in values for the following parameters (marked in bold above):

* telegraf.influxdata.com/inputs - This contains the required configuration for the Telegraf exec Input plugin. Please refer[ to this doc](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/redis) for more information on configuring the MySQL input plugin for Telegraf. Note: As telegraf will be run as a sidecar the host should always be localhost.
    * In the input plugins section, that is:
        * **servers **- The URL of your MariaDB server. For information about additional input plugin configuration options, see the [Readme ](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/mysql)for the MySQL input plugin.
    * In the tags section, that is  `[inputs.mysql.tags]`
        * **environment** - This is the deployment environment where the MariaDB cluster identified by the value of **servers** resides. For example: dev, prod or qa. While this value is optional we highly recommend setting it.
        * **db_cluster** - Enter a name to identify this MariaDB cluster. This cluster name will be shown in the Sumo Logic dashboards.  

Here’s an explanation for additional values set by this configuration that we request you to please **do not modify** as they will cause the Sumo Logic apps to not function correctly.



* **telegraf.influxdata.com/class: sumologic-prometheus** - This instructs the Telegraf operator what output to use. This should not be changed.
* **prometheus.io/scrape: "true"** - This ensures our Prometheus will scrape the metrics.
* **prometheus.io/port: "9273"** - This tells prometheus what ports to scrape on. This should not be changed.
* **telegraf.influxdata.com/inputs**
    * In the tags section i.e.  `[inputs.mysql.tags]`
        * **component**: “database” - This value is used by Sumo Logic apps to identify application components.
        * **db_system**: “mariadb” - This value identifies the database system.

For all other parameters, please see [this doc](https://help.sumologic.com/03Send-Data/Collect-from-Other-Data-Sources/Collect_Metrics_Using_Telegraf/03_Install_Telegraf#Configuring_Telegraf) for more properties that can be configured in the Telegraf agent globally.



1. Sumo Logic Kubernetes collection will automatically start collecting metrics from the pods having the labels and annotations defined in the previous step.
2. Verify metrics in Sumo Logic.


### Configure Logs Collection


This section explains the steps to collect MariaDB logs from a Kubernetes environment.



1. **(Recommended Method) Add labels on your MariaDB pods to capture logs from standard output.**

Make sure that the logs from MariaDB are sent to stdout. Follow the instructions below to capture MariaDBlogs from stdout on Kubernetes.



1. Apply following labels to the MariaDBpod:


```
labels:
    environment: "prod_CHANGEME"
    component: "database"
    db_system: "mariadb"
    db_cluster "Cluster_CHANGEME"
```



    Enter in values for the following parameters (marked in **bold and CHANGE_ME** above):



* **environment.** This is the deployment environment where the MariaDB cluster identified by the value of **servers** resides. For example:- dev, prod, or QA. While this value is optional we highly recommend setting it.
* **db_cluster.** Enter a name to identify this MariaDB cluster. This cluster name will be shown in the Sumo Logic dashboards. If you haven’t defined a cluster in MariaDB, then enter ‘**default**’ for db_cluster.

    Here’s an explanation for additional values set by this configuration, but **do not modify them** as changes will cause the Sumo Logic apps to not function correctly.

* **component.** “database” - This value is used by Sumo Logic apps to identify application components.
* **db_system.** “mariadb” - This value identifies the database system.

    For all other parameters, please see [this doc](https://help.sumologic.com/03Send-Data/Collect-from-Other-Data-Sources/Collect_Metrics_Using_Telegraf/03_Install_Telegraf#Configuring_Telegraf) for more properties that can be configured in the Telegraf agent globally.

1. The Sumologic-Kubernetes-Collection will automatically capture the logs from stdout and will send the logs to Sumologic. For more information on deploying Sumologic-Kubernetes-Collection,[ visit](https://help.sumologic.com/07Sumo-Logic-Apps/10Containers_and_Orchestration/Kubernetes/Collect_Logs_and_Metrics_for_the_Kubernetes_App) here.
2. Verify logs in Sumo Logic.
1. **(Optional) Collecting MariaDB Logs from a Log File \
**Follow the steps below to capture MariaDB logs from a log file on Kubernetes.
1. Determine the location of the MariaDB log file on Kubernetes. This can be determined from the server.conf for your MariaDB cluster along with the mounts on the MariaDB pods.
2. Install the Sumo Logic [tailing sidecar operator](https://github.com/SumoLogic/tailing-sidecar/tree/main/operator#deploy-tailing-sidecar-operator).
3. Add the following annotation in addition to the existing annotations.


```
annotations:
  tailing-sidecar: sidecarconfig;<mount>:<path_of_MariaDB_log_file>/<MariaDB_log_file_name>
```



    Example:


```
annotations:
  tailing-sidecar: sidecarconfig;data:/var/opt/MariaDB/errorlog

```



1. Make sure that the MariaDB pods are running and annotations are applied by using the command: **kubectl describe pod <MariaDB_pod_name>**
2. Sumo Logic Kubernetes collection will automatically start collecting logs from the pods having the annotations defined above.
3. Verify logs in Sumo Logic.
1. **Add an FER to normalize the fields in Kubernetes environments \
**Labels created in Kubernetes environments automatically are prefixed with pod_labels. To normalize these for our app to work, we need to create a Field Extraction Rule if not already created for Proxy Application Components:
1. **Go to Manage Data > Logs > Field Extraction Rules.**
2. **Click the + Add button on the top right of the table.**
3. **The following form appears:**


8


1. Enter the following options:
1. **Rule Name**. Enter the name as **App Observability - database**.
2. **Applied At.** Choose **Ingest Time**
3. **Scope**. Select **Specific Data**
4. **Scope**: Enter the following keyword search expression:


```
pod_labels_environment=* pod_labels_component=database pod_labels_db_cluster=* pod_labels_db_system=*

```



* **Parse Expression**.Enter the following parse expression:

```
if (!isEmpty(pod_labels_environment), pod_labels_environment, "") as environment
| pod_labels_component as component
| pod_labels_db_system as db_system
| pod_labels_db_cluster as db_cluster

```


1. Click **Save** to create the rule.


### Collect MariaDB Logs and Metrics for Non-Kubernetes environments


Sumo Logic uses the Telegraf operator for MariaDB metric collection and the [Installed Collector](https://help.sumologic.com/03Send-Data/Installed-Collectors/01About-Installed-Collectors) for collecting MariaDB logs. The diagram below illustrates the components of the MariaDB collection in a non-Kubernetes environment. Telegraf uses the[ MySQL Input Plugin](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/sqlserver) to obtain MariaDB metrics and the Sumo Logic output plugin to send the metrics to Sumo Logic. Logs from MariaDB are collected by a [Local File Source](https://help.sumologic.com/03Send-Data/Sources/01Sources-for-Installed-Collectors/Local-File-Source).


The process to set up collection for MariaDB data is done through the following steps:



1. Configure Logs Collection
    1. Configure MariaDB to log to a local file
    2. Configure a Collector
    3. Configure a Source
2. Configure Metrics Collection
    4. Configure a Hosted Collector
    5. Configure an HTTP Logs and Metrics Source
    6. Install Telegraf
    7. Configure and start Telegraf


### Configure Logs Collection
10


This section provides instructions for configuring log collection for MariaDB running on a non-Kubernetes environment for the Sumo Logic App for MariaDB.

By default, MariaDB logs are stored in a log file. MariaDB also supports forwarding logs via Syslog Audit Logs.

Sumo Logic supports collecting logs both via Syslog and a local log file. Utilizing Sumo Logic [Cloud Syslog](https://help.sumologic.com/03Send-Data/Sources/02Sources-for-Hosted-Collectors/Cloud-Syslog-Source) will require TCP TLS Port 6514 to be open in your network. Local log files can be collected via [Installed collectors](https://help.sumologic.com/03Send-Data/Installed-Collectors) or [FluentD](https://www.fluentd.org/). Installed collector will require you to allow outbound traffic to [Sumo Logic endpoints](https://help.sumologic.com/APIs/General-API-Information/Sumo-Logic-Endpoints-by-Deployment-and-Firewall-Security) for collection to work. For detailed requirements for Installed collectors, see this [page](https://help.sumologic.com/01Start-Here/03About-Sumo-Logic/System-Requirements/Installed-Collector-Requirements).

Based on your infrastructure and networking setup choose one of these methods to collect MariaDB logs and follow the instructions below to set up log collection:



1. Configure MariaDB to log to a local file
2. Configure a Collector
3. Configure a Source


#### Configure MariaDB to log to a local file.  
11


MariaDB logs written to a log file can be collected via the Local File Source of a Sumo Logic Installed Collector.

To configure the MariaDB log file(s), locate your local server.cnf configuration file in the database directory.



1. Open server.cnf in a text editor.
2. Set the following  parameters in the [mariadb] section:


```
[mariadb]
log_error=/var/log/mariadb/mariadb-error.log
log_output=FILE
slow_query_log=1
slow_query_log_file = /var/log/mariadb/slow_query.log
long_query_time=2
```



12




* [Error Logs](https://mariadb.com/kb/en/error-log/): MariaDB always writes its error log, but the destination is configurable.
* [Slow Query Logs](https://mariadb.com/kb/en/slow-query-log-overview/): The slow query log is disabled by default.
* [General Query Logs](https://mariadb.com/kb/en/general-query-log/). We don't recommend enabling general_log for performance reasons. These logs are not used by the Sumo Logic MariaDB App.





1. Save the server.cnf file.
2. Restart the MariaDB server: \
`systemctl restart mariadb`


#### Configuring a Collector
13


Use one of the following Sumo Logic Collector options:



* To collect logs directly from the MariaDB machine, configure an[ Installed Collector](https://help.sumologic.com/03Send-Data/Installed-Collectors).


#### Configuring a Source
14


This section demonstrates how to configure sources for the following log types:



* Error Logs
* Slow Query Logs


##### Configure Source for MariaDB Error Logs
15


This section demonstrates how to configure a Local File Source for MariaDB Error Logs, for use with an [Installed Collector](https://help.sumologic.com/07Sumo-Logic-Apps/04Microsoft-and-Azure/IIS_10_(Legacy)/Collect_Logs_for_the_IIS_10_(Legacy)_App#Configure_a_Collector). You may configure a [Remote File Source](https://help.sumologic.com/03Send-Data/Sources/01Sources-for-Installed-Collectors/Remote-File-Source), but the configuration is more complex.


16
Sumo Logic recommends using a Local File Source whenever possible.

**To configure a local file source for MariaDB Error Logs, do the following:**



1. On the Collection Management screen, click **Add**, next to the collector, then select **Add Source**.
2. Select **Local File_ _**as the source type.
3. Configure the Local File Source fields as follows:
1. **Name** (Required). Enter a name for the source.
2. **Description** (Optional).
3. **File Path** (Required)**.** Enter the path to your mariadb-error.log. The files are typically located in /var/log/mariadb/mariadb-error.log. If you are using a customized path, check the server.cnf file for this information
4. **The collection should begin. Set this for how far back historically you want to start collecting.**
5. **Source Host** (Optional)**.** Sumo Logic uses the hostname assigned by the OS unless you enter a different hostname
6. **Source Category** (Recommended). DB/MariaDB/ErrorLogs_._
7. **Fields. **Set the following fields**:**
* `component = database`
* `db_system = mariadb`
* `db_cluster = <Your_MariaDB_Cluster_Name>`. Enter **Default** if you do not have one.
* `environment = <Your_Environment_Name`> (for example, Dev, QA, or Prod)


17


1. In the **Advanced** section, select the following options:
1. **Timestamp Parsing Settings**: Make sure the setting matches the timezone on the log files.
2. **Enable Timetamp Parsing**: Select **Extract timestamp information from log file entries**.
3. **Time Zone**: Select the option to **Use time zone from log file. If none is present use**: and set the timezone to **UTC**.
4. **Timestamp Format**: Select the option to **Automatically detect the format**.
5. **Encoding**. UTF-8 is the default, but you can choose another encoding format from the menu if your MariaDB logs are encoded differently.
6. **Enable Multiline Processing**. Uncheck the box to **Detect messages spanning multiple lines**. Since MariaDB Error logs are single line log files, disabling this option will ensure that your messages are collected correctly.
1. Click **Save**.

After a few minutes, your new Source should be propagated down to the Collector and will begin submitting your MariaDB log files to the Sumo Logic service.


#### Configure Source for MariaDB Slow Query Logs
18


This section demonstrates how to configure a Local File Source for MariaDB Slow Query Logs, for use with an [Installed Collector](https://help.sumologic.com/07Sumo-Logic-Apps/04Microsoft-and-Azure/IIS_10_(Legacy)/Collect_Logs_for_the_IIS_10_(Legacy)_App#Configure_a_Collector).

**To configure a local file source for MariaDB Slow Query Logs, do the following:**



1. On the Collection Management screen, click **Add**, next to the collector, then select **Add Source**.
2. Select **Local File_ _**as the source type.
3. Configure the Local File Source fields as follows:
1. **Name** (Required). Enter a name for the source.
2. **Description** (Optional).
3. **File Path** (Required)**.** Enter the path to your slow_query.log. The files are typically located in /var/log/mariadb/slow_query.log. If you are using a customized path, check the server.cnf file for this information
4. **The collection should begin. Set this for how far back historically you want to start collecting.**
5. **Source Host** (Optional)**.** Sumo Logic uses the hostname assigned by the OS unless you enter a different hostname
6. **Source Category** (Recommended)**.** DB/MariaDB/SlowQuery_._
7. **Fields. **Set the following fields**:**
* component = database
* db_system = mariadb
* db_cluster = <Your_mariadb_Cluster_Name>. Enter **Default** if you do not have one.
* environment = <Your_Environment_Name>** **(for example, Dev, QA, or Prod)


19


1. In the **Advanced** section, select the following options:
1. **Timestamp Parsing Settings**: Make sure the setting matches the timezone on the log files.
2. **Enable Timetamp Parsing**: Select **Extract timestamp information from log file entries**.
3. **Time Zone**: Select the option to **Use time zone from log file. If none is present use**: and set the timezone to **UTC**.
4. **Timestamp Format**: Select the option to **Automatically detect the format**.
5. **Encoding**. UTF-8 is the default, but you can choose another encoding format from the menu if your MariaDB logs are encoded differently.
6. **Enable Multiline Processing**
* **Detect Messages Spanning Multiple Lines. **True
* **Infer Boundaries - Detect message boundaries automatically. False**
* **Boundary Regex.  `^#\sTime:\s.`
1. Click **Save**.

After a few minutes, your new Source should be propagated down to the Collector and will begin submitting your MariaDB log files to the Sumo Logic service.


### Configure Metrics Collection
20



#### Setup a Sumo Logic HTTP Source
21



##### Configure a Hosted Collector for Metrics.
22


To create a new Sumo Logic hosted collector, perform the steps in the [Configure a Hosted Collector](https://help.sumologic.com/03Send-Data/Hosted-Collectors/Configure-a-Hosted-Collector) documentation.


##### Configure an HTTP Logs & Metrics source:
23


On the created Hosted Collector on the Collection Management screen, select Add Source.



1. **Select HTTP Logs & Metrics_._**
    1. **Name.** (Required). Enter a name for the source.
    2. **Description.** (Optional).
    3. **Source Category.** (Recommended). Be sure to follow the [Best Practices for Source Categories](https://help.sumologic.com/03Send-Data/01-Design-Your-Deployment/Best-Practices%3A-Good-Source-Category%2C-Bad-Source-Category). A recommended Source Category may be Prod/DB/MariaDB/Metrics.
2. Select **Save**.
3. Note the URL provided once you click _Save_. You can retrieve it again by selecting the Show URL next to the source on the Collection Management screen.


#### Setup Telegraf
24



##### Install Telegraf if you haven’t already.
25


Use the[ following steps](https://help.sumologic.com/03Send-Data/Collect-from-Other-Data-Sources/Collect_Metrics_Using_Telegraf/03_Install_Telegraf) to install Telegraf.


##### Configure and start Telegraf.
26


As part of collecting metrics data from Telegraf, we will use the [MySQL Input Plugin](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/mysql) to get data from Telegraf and the [Sumo Logic output plugin](https://github.com/SumoLogic/fluentd-output-sumologic) to send data to Sumo Logic.

Create or modify `telegraf.conf` and copy and paste the text below:  


```
[[inputs.mysql]]
  servers = ["user_TO_BE_CHANGED:password_TO_BE_CHANGED@tcp(IP_ADDRESS_MARIADB_TO_BE_CHANGED:PORT_MARIADB_TO_BE_CHANGED)/?tls=false"]
  metric_version = 2
  table_schema_databases = []
  perf_summary_events = []
  gather_table_schema                       = true
  gather_process_list                       = true
  gather_info_schema_auto_inc               = true
  gather_user_statistics = true
  gather_slave_status = true
  gather_table_io_waits = true
  gather_table_lock_waits = true
  gather_index_io_waits = true
  gather_event_waits = true
  gather_file_events_stats = true
  gather_perf_events_statements = true
  interval_slow = "30m"
[inputs.mysql.tags]
    environment="dev_TO_BE_CHANGEME"
    component="database"
    db_system="mariadb"
    db_cluster="mariadb_on_premise_TO_BE_CHANGEME"

[[outputs.sumologic]]
  url = "<URL_from_HTTP_Logs_and_Metrics_Source>"
  data_format = "prometheus"
```


Enter values for fields annotated with `<VALUE_TO_BE_CHANGED>` to the appropriate values. Do not include the brackets (`< >`) in your final configuration



* Input plugins section, which is `[[inputs.mysql]]`:
    * **servers.** The the URL of your MariaDB server. For information about additional input plugin configuration options, see the [Readme ](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/mysql)for the MySQL input plugin.
* In the tags section, which is [inputs.mysql.tags]:
    * **environment.** This is the deployment environment where the MariaDB cluster identified by the value of **servers** resides. For example; dev, prod, or QA. While this value is optional we highly recommend setting it.
    * **db_cluster.** Enter a name to identify this MariaDB cluster. This cluster name will be shown in our dashboards.
* In the output plugins section, which is `[[outputs.sumologic]]`:
    * **URL.** This is the HTTP source URL created previously. See this doc for more information on additional parameters for configuring the Sumo Logic Telegraf output plugin.

Here’s an explanation for additional values set by this Telegraf configuration.


27
If you haven’t defined a cluster in MariaDB, then enter ‘**default**’ for db_cluster.
28
There are additional values set by the Telegraf configuration.  We recommend not to modify these values as they might cause the Sumo Logic app to not function correctly.



* **data_format**. “prometheus” - In the output `[[outputs.sumologic]]` plugins section. Metrics are sent in the Prometheus format to Sumo Logic.
* **component.** “database” - In the input `[[inputs.mysql]]` plugins section. This value is used by Sumo Logic apps to identify application components.
* **db_system.** “mariadb” - In the input plugins sections. This value identifies the database system.

See[ this doc](https://github.com/influxdata/telegraf/blob/master/etc/telegraf.conf) for all other parameters that can be configured in the Telegraf agent globally.


29
After you have finalized your telegraf.conf file, you can start or reload the telegraf service using instructions from this[ doc](https://docs.influxdata.com/telegraf/v1.17/introduction/getting-started/#start-telegraf-service).

At this point, Telegraf should start collecting the MariaDB metrics and forward them to the Sumo Logic HTTP Source.


## Install the MariaDB Monitors, App, and view the Dashboards

This page provides instructions for installing the MariaDB Monitors, App, as well as examples of each of the App dashboards. These instructions assume you have already set up the collection as described in the Collect Logs and Metrics for the MariaDB App page.


### Pre-Packaged Alerts
30


Sumo Logic has provided out-of-the-box alerts available through [Sumo Logic monitors](https://help.sumologic.com/Visualizations-and-Alerts/Alerts/Monitors) to help you monitor your MariaDB clusters. These alerts are built based on metrics and logs datasets and include preset thresholds based on industry best practices and recommendations.

For details on the individual alerts, see this [page](https://help.sumologic.com/07Sumo-Logic-Apps/12Databases/MariaDB/MariaDB_Alerts).


#### Installing Monitors

* To install these alerts, you need to have the Manage Monitors role capability.
* Alerts can be installed by either importing a JSON file or a Terraform script.

There are limits to how many alerts can be enabled - see the [Alerts FAQ](https://help.sumologic.com/Visualizations-and-Alerts/Alerts/Monitors/Monitor_FAQ) for details.


##### Install the monitors by importing a JSON file Method:

1. Download the [JSON file](https://github.com/SumoLogic/terraform-sumologic-sumo-logic-monitor/blob/main/monitor_packages/MariaDB/MariaDB.json) that describes the monitors.
2. The [JSON](https://github.com/SumoLogic/terraform-sumologic-sumo-logic-monitor/blob/main/monitor_packages/MariaDB/MariaDB.json) contains the alerts that are based on Sumo Logic searches that do not have any scope filters and therefore will be applicable to all MariaDB clusters, the data for which has been collected via the instructions in the previous sections.  However, if you would like to restrict these alerts to specific clusters or environments, update the JSON file by replacing the text `db_system=mariadb` with `<Your Custom Filter>`.

Custom filter examples:



1. For alerts applicable only to a specific cluster, your custom filter would be,  ‘`db_cluster=mariadb-prod.01`‘.
2. For alerts applicable to all clusters that start with Kafka-prod, your custom filter would be, `db_cluster=mariadb-prod*`.
3. For alerts applicable to a specific cluster within a production environment, your custom filter would be,`db_cluster=mariadb-1` and `environment=prod` (This assumes you have set the optional environment tag while configuring collection).
4. Go to Manage Data > Alerts > Monitors.
5. Click **Add**
34

6. Click Import and then copy-paste the above JSON to import monitors.


35
The monitors are disabled by default. Once you have installed the alerts using this method, navigate to the MariaDB folder under **Monitors** to configure them. See [this](https://help.sumologic.com/Visualizations-and-Alerts/Alerts/Monitors) document to enable monitors to send notifications to teams or connections. See the instructions detailed in Step 4 of this [document](https://help.sumologic.com/Visualizations-and-Alerts/Alerts/Monitors#Add_a_monitor).


##### Install the alerts using a Terraform script Method
36




1. **Generate a Sumo Logic access key and ID. \
**Generate an access key and access ID for a user that has the Manage Monitors role capability in Sumo Logic using these[ instructions](https://help.sumologic.com/Manage/Security/Access-Keys#manage-your-access-keys-on-preferences-page). Identify which deployment your Sumo Logic account is in, using this [link](https://help.sumologic.com/APIs/General-API-Information/Sumo-Logic-Endpoints-by-Deployment-and-Firewall-Security)
2. **[Download and install Terraform 0.13](https://www.terraform.io/downloads.html) or later. **
3. ** Download the Sumo Logic Terraform package for MariaDB alerts. \
**The alerts package is available in the Sumo Logic GitHub [repository](https://github.com/SumoLogic/terraform-sumologic-sumo-logic-monitor/tree/main/monitor_packages/MariaDB). You can either download it through the “git clone” command or as a zip file.
4. **Alert Configuration.** After the package has been extracted, navigate to the package directory `terraform-sumologic-sumo-logic-monitor/monitor_packages/MariaDB/`

Edit the **MariaDB.auto.tfvars** file and add the Sumo Logic Access Key, Access Id, and Deployment from Step 1.

    ```
    access_id   = "<SUMOLOGIC ACCESS ID>"
    access_key  = "<SUMOLOGIC ACCESS KEY>"
    environment = "<SUMOLOGIC DEPLOYMENT>"
    ```


    The Terraform script installs the alerts without any scope filters, if you would like to restrict the alerts to specific clusters or environments, update the variable `mariadb_data_source`. Custom filter examples:

1. A specific cluster `db_cluster=mariadb.prod.01`
2. All clusters in an environment `environment=prod`
3. For alerts applicable to all clusters that start with mariadb-prod, your custom filter would be: `db_cluster=mariadb-prod*`
4. For alerts applicable to a specific cluster within a production environment, your custom filter would be:
`db_cluster=mariadb-1` and `environment=prod` (This assumes you have set the optional environment tag while configuring collection)

All monitors are disabled by default on installation, if you would like to enable all the monitors, set the parameter `monitors_disabled` to false in this file.


By default, the monitors are configured in a monitor **folder** called “**MariaDB**”, if you would like to change the name of the folder, update the monitor folder name in “folder” key at **`MariaDB.auto.tfvars` file.


    If you would like the alerts to send email or connection notifications, configure these in the file **`MariaDB_notifications.auto.tfvars`. For configuration examples, refer to the next section.

1. **Email and Connection Notification Configuration Examples**

    Modify the file **`MariaDB_notifications.auto.tfvars` and populate connection_notifications and email_notifications as per below examples.


#### Pagerduty Connection Example:

```
connection_notifications = [
    {
      connection_type       = "PagerDuty",
      connection_id         = "<CONNECTION_ID>",
      payload_override      = "{\"service_key\": \"your_pagerduty_api_integration_key\",\"event_type\": \"trigger\",\"description\": \"Alert: Triggered {{TriggerType}} for Monitor {{Name}}\",\"client\": \"Sumo Logic\",\"client_url\": \"{{QueryUrl}}\"}",
      run_for_trigger_types = ["Critical", "ResolvedCritical"]
    },
    {
      connection_type       = "Webhook",
      connection_id         = "<CONNECTION_ID>",
      payload_override      = "",
      run_for_trigger_types = ["Critical", "ResolvedCritical"]
    }
  ]
```


    Replace `<CONNECTION_ID>` with the connection id of the webhook connection. The webhook connection id can be retrieved by calling the [Monitors API](https://api.sumologic.com/docs/#operation/listConnections).


    For overriding payload for different connection types, refer to this [document](https://help.sumologic.com/Manage/Connections-and-Integrations/Webhook-Connections/Set_Up_Webhook_Connections).


##### **Email Notifications Example:**

```
email_notifications = [
    {
      connection_type       = "Email",
      recipients            = ["abc@example.com"],
      subject               = "Monitor Alert: {{TriggerType}} on {{Name}}",
      time_zone             = "PST",
      message_body          = "Triggered {{TriggerType}} Alert on {{Name}}: {{QueryURL}}",
      run_for_trigger_types = ["Critical", "ResolvedCritical"]
    }
  ]

```



1. ** Install the Alerts**
    1. Navigate to the package directory terraform-sumologic-sumo-logic-monitor/monitor_packages/**MariaDB**/ and run **terraform init. **This will initialize Terraform and will download the required components.
    2. Run **terraform plan **to view the monitors which will be created/modified by Terraform.
    3. Run **terraform apply**.
2. ** Post Installation**

    If you haven’t enabled alerts and/or configured notifications through the Terraform procedure outlined above, we highly recommend enabling alerts of interest and configuring each enabled alert to send notifications to other users or services. This is detailed in Step 4 of [this document](https://help.sumologic.com/Visualizations-and-Alerts/Alerts/Monitors#Add_a_monitor).



39
There are limits to how many alerts can be enabled - see the [Alerts FAQ](https://help.sumologic.com/Visualizations-and-Alerts/Alerts/Monitors/Monitor_FAQ).


### Install the Sumo Logic App
40


This section demonstrates how to install the MariaDB App.

**To install the app:**

Locate and install the app you need from the **App Catalog**. If you want to see a preview of the dashboards included with the app before installing, click **Preview Dashboards**.



1. From the **App Catalog**, search for and select the app**.**
2. Select the version of the service you're using and click **Add to Library**.


41
Version selection is applicable only to a few apps currently. For more information, see the[ Install the Apps from the Library](https://help.sumologic.com/01Start-Here/Library/Apps-in-Sumo-Logic/Install-Apps-from-the-Library).



1. To install the app, complete the following fields.
    1. **App Name.** You can retain the existing name, or enter a name of your choice for the app. 
    2. **Data Source.**
        * Choose **Enter a Custom Data Filter**, and enter a custom MariaDB cluster filter. Examples;
            1. For all MariaDB clusters, \
`db_cluster=*`.
            2. For a specific cluster, \
`db_cluster=mariadb.dev.01`.
            3. Clusters within a specific environment \
`db_cluster=mariadb.dev.01` and `environment=prod \
`(This assumes you have set the optional environment tag while configuring collection).
    3. **Advanced**. Select the **Location in Library** (the default is the Personal folder in the library), or click **New Folder** to add a new folder.
    4. Click **Add to Library**.

Once an app is installed, it will appear in your **Personal** folder, or another folder that you specified. From here, you can share it with your organization.

Panels will start to fill automatically. It's important to note that each panel slowly fills with data matching the time range query and received since the panel was created. Results won't immediately be available, but with a bit of time, you'll see full graphs and maps.


### Dashboard Filter with Template Variables  
42


Template variables provide dynamic dashboards that rescope data on the fly. As you apply variables to troubleshoot through your dashboard, you can view dynamic changes to the data for a fast resolution to the root cause. For more information, see the[ Filter with template variables](https://help.sumologic.com/Visualizations-and-Alerts/Dashboard_(New)/Filter_with_template_variables) help page.


43
You can use template variables to drill down and examine the data on a granular level.


### Dashboards
44



#### MariaDB - Overview
45


The **MariaDB - Overview** dashboard gives you an at-a-glance view of the state of your database clusters by monitoring key cluster information such as errors, failed logins, errors, queries executed, slow queries, lock waits, uptime, and more.

**Use this dashboard to**:



* Quickly identify the state of a given database cluster


46



#### MariaDB - Error Logs
47


The **MariaDB - Error Logs** dashboard provides insight into database error logs by specifically monitoring database shutdown/start events, errors over time, errors, warnings, and crash recovery attempts.

**Use this dashboard to**:



* Quickly identify errors and patterns in logs for troubleshooting
* Monitor trends in the error log and identify outliers
* Ensure that server start, server stop, and crash recovery events are in line with expectations
* Dashboard filters allow you to narrow a search for the database clusters.


48



#### MariaDB - Failed Logins
49


The **MariaDB - Failed Logins** dashboard provides insights into all failed login attempts by location, users and hosts.

**Use this dashboard to**:



* Monitor all failed login attempts and identify any unusual or suspicious activity


50



#### MariaDB - Replication
51


The **MariaDB - Replication** dashboard provides insights into the state of database replication.

**Use this dashboard to**:



* Quickly determine reasons for replication failures
* Monitor replication status trends


52



#### MariaDB - Slow Queries
53


The **MariaDB - Slow Queries** dashboard provides insights into all slow queries executed on the database.


54
Slow queries are queries that take 10 seconds or more to execute (default value is 10 seconds as per MariaDB configuration which can be altered) and excessive slow queries are those that take 15 seconds or more to execute.

**Use this dashboard to**:



* Identify all slow queries
* Quickly determine which queries have been identified as slow or excessive slow queries
* Monitor users and hosts running slow queries
* Determine which SQL commands are slower than others
* Examine slow query trends to determine if there are periodic performance bottlenecks in your database clusters


55



#### MariaDB - Performance and Resource Metrics
56


The **MariaDB - Performance and Resource Metrics** dashboard allows you to monitor the performance and resource usage of your database clusters.

**Use this dashboard to**:



* Understand the behavior and performance of your database clusters
* Monitor key operational metrics around connections, network traffic, threads running, innodb waits and locks.
* Monitor query execution trends to ensure they match up with expectations
* Dashboard filters allow you to narrow a search for a specific database cluster


57



#### MariaDB - Performance Schema Metrics
58


The **MariaDB - Performance Schema Metrics** Dashboard provides insights into the metrics provided by the MariaDB Performance Schema, which is a feature for monitoring MariaDB Server execution at a low level.

**Use this dashboard to**:



* Monitor errors and warning for SQL statements
* Monitor statements running without use of index columns
* Monitor statistics such as Table and Index waits and read and write lock waits to optimize the performance of your database


59



#### MariaDB - Replication Metrics
60


The **MariaDB - Replication Metrics** dashboard shows replication events, errors, warnings, and nodes.


61



#### MariaDB - InnoDB Metrics
62


The **MariaDB - InnoDB** Metrics dashboard shows replication events, errors, warnings, and nodes.



## MariaDB Alerts

Sumo Logic has provided out-of-the-box alerts available through [Sumo Logic monitors](https://help.sumologic.com/Visualizations-and-Alerts/Alerts/Monitors) to help you quickly determine if the MariaDB Database are available and performing as expected. These alerts are built based on logs and metrics datasets and have preset thresholds based on industry best practices and recommendations.

Sumo Logic provides the following out-of-the-box alerts:

**INSERT TABLE**