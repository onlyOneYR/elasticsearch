# Configure Elasticsearch features {#concept_878575 .concept}

This topic describes how to use ECS to access Elasticsearch, configure the VPC and public network whitelists, and customize monitoring and alerting settings.

## Use ECS to access Elasticsearch {#section_1pd_nbq_34u .section}

Alibaba Cloud Elasticsearch allows you to use the following methods to access Elasticsearch:

-   Log on to the [Kibana console](../../../../reseller.en-US/User Guide/Data visualization/Kibana/Log on to the Kibana console.md#) that has been integrated into Alibaba Cloud Elasticsearch, and then access Elasticsearch from the **Dev Tools** page.
-   Use an application or the cURL tool installed on an ECS instance to access Elasticsearch. The ECS instance and Elasticsearch instance must be created in the same region and VPC network.
-   Use the public network address of the Elasticsearch instance to access Elasticsearch.
-   Use other [Elasticsearch clients](https://www.elastic.co/guide/en/elasticsearch/client/index.html) listed on the official Elasticsearch site to access Elasticsearch.

Use ECS to access Elasticsearch \(optional\)

**Note:** 

-   If your ECS instance is connected to the classic network, then you must create a ClassicLink connection to access Elasticsearch. For more information, see [Classic network errors](https://help.aliyun.com/document_detail/61359.html).
-   The ClassicLink connection is a unidirectional connection originated from the classic network to the VPC network. You cannot use this connection to access the classic network from the VPC network.

If you already have an ECS instance deployed in the same VPC network as your Elasticsearch instance, then you can use this ECS instance as a client to access Elasticsearch. You only need to deploy your application or install the cURL tool on the ECS instance.

If your ECS instance does not meet the preceding requirements, then you must purchase an ECS instance in the same VPC network as your Elasticsearch instance. For more information, see [Create an instance by using the wizard](../../../../reseller.en-US/Instances/Create an instance/Create an instance by using the wizard.md#).

**Note:** 

-   The purchased ECS instance is used as a client. You can deploy applications on the ECS instance and then use it to access Elasticsearch.
-   If you do not have an ECS instance in the same VPC network as your Elasticsearch instance, you can also use the Kibana console to send test queries to Elasticsearch.

Use ECS and cURL to access Elasticsearch \(optional\) 

1.  Log on to the ECS instance that is connected to the same VPC network as your Elasticsearch instance, and then install the cURL tool.
2.  Add the cURL path to the path environment variable of the ECS instance so that you can use cURL to access Elasticsearch.

## Configure the VPC and public network whitelists {#section_g2p_g4b_oa6 .section}

Alibaba Cloud Elasticsearch is integrated with the X-Pack plug-in enterprise edition. The X-Pack plug-in is used to protect data transmission when you use the public or internal network address of your Elasticsearch instance to access Elasticsearch. You can configure the VPC or public network whitelist to implement access control for your Elasticsearch instance. For more information, see [Security configuration](../../../../reseller.en-US/User Guide/Instance management/Security configuration.md#).

## Customize monitoring and alerting settings {#section_jqu_p91_0vz .section}

Alibaba Cloud Elasticsearch supports monitoring Elasticsearch instances and generating alerts by sending SMS messages. The monitoring data includes the following metrics. You can customize the thresholds for triggering alerts. For more information, see [ES CloudMonitor alarm](../../../../reseller.en-US/Monitoring Alarms/ES CloudMonitor alarm.md).

-   Cluster Status
-   Cluster Index Queries \(QPS\)
-   Cluster Write Queries \(QPS\)
-   Node CPU Usage \(%\)
-   Node Disk Space Usage \(%\)
-   Node Heap Memory Usage \(%\)
-   Node Workload Within One Minute

