# Buy page {#concept_nqm_cmk_zgb .concept}

## Billing methods {#section_fcv_l4l_zgb .section}

**Subscription** 

**Note:** Discounts are offered for Subscription-based Alibaba Cloud Elasticsearch instances based on the subscription duration. Elasticsearch instances purchased on the Alibaba Cloud International site do not support conditional refund or unconditional refund within five days. If you no longer need a Subscription-based Alibaba Cloud Elasticsearch instance, back up the data stored on the instance, use your Alibaba Cloud account to sign in, select **Console** \> **Billing Management** \> **Renew**, and click the switch to manually disable auto renewal. Before the end of the current billing cycle, you can still use this Elasticsearch instance. However, the subscription fees will not be refunded. Alibaba Cloud will stop renewing your instance after the current billing cycle ends.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134283/155834397739941_en-US.png)

-   If the auto renewal function is disabled, you must log on to the Elasticsearch console and manually renew the overdue instances. For more information, see [Overdue payments](../../../../intl.en-US/Product Introduction/Overdue payments.md).
-   Elasticsearch instances cannot be manually released in the console.
-   Auto renewal is supported and disabled by default. For more information, see the **Auto renewal** section in this topic.

 **Pay-As-You-Go** 

**Note:** We recommend that you purchase **Pay-As-You-Go** Elasticsearch instances for testing purposes at the development and testing stages.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134283/155834397739942_en-US.png)

-   Auto renewal is supported. For more information, see [Overdue payments](../../../../intl.en-US/Product Introduction/Overdue payments.md).
-   You can click **More** and then select Release to manually release an Elasticsearch instance.

## Regions and zones {#section_wsg_m4l_zgb .section}

Alibaba Cloud Elasticsearch supports the following regions:[Regions and zones](../../../../intl.en-US/General Reference/Regions and zones.md#)

-   China \(Hangzhou\): Zone B, Zone F, Zone G, Zone H, and Zone I.
-   China \(Beijing\): Zone E.
-   China \(Shanghai\): Zone B and Zone D.
-   China \(Shenzhen\): Zone C.
-   India \(Mumbai\): Zone A.
-   Singapore: Zone A and Zone B.
-   Hong Kong: Zone B and Zone C.
-   US \(Silicon Valley\): Zone A and Zone B.
-   Malaysia \(Kuala Lumpur\): Zone A and Zone B.
-   Germany \(Frankfurt\): Zone A and Zone B.
-   Japan \(Tokyo\): Zone A.
-   Australia \(Sydney\): Zone A.
-   Indonesia \(Jakarta\): Zone A.
-   China \(Qingdao\): Zone B and Zone C.

## Instances {#section_rry_m4l_zgb .section}

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134283/155834397739948_en-US.png)

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134283/155834397739950_en-US.png)

 **Version** 

Alibaba Cloud Elasticsearch supports the following versions. You can select a version based on your business scenarios:

-   Elasticsearch 6.7.0 with Commercial Feature.
-   Elasticsearch 6.3.2 with Commercial Feature.
-   Elasticsearch 5.5.3 with Commercial Feature.

**Note:** 

-   [Elasticsearch 6.3.2 Release Notes](https://www.elastic.co/guide/en/elasticsearch/reference/6.3/release-notes-6.3.2.html).
-   [Elasticsearch 6.3.2 Release Notes](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/release-notes-5.5.3.html).

 **Network Type** 

Elasticsearch currently only supports **VPC** networks.

 **Number of Zones** 

When you purchase an Elasticsearch instance, you can specify the number of zones for the instance. If you specify more than one zone, then the instance is deployed across the zones. Currently, you can deploy an Elasticsearch instance across zones in the China \(Hangzhou\), China \(Beijing\), China \(Shanghai\), or China \(Shenzhen\) region. You can deploy an Elasticsearch instance across zones as follows:

-   Across three zones: a deployment for implementing high availability. We recommend that you use this deployment for production where high availability is required.
-   Across two zones: a disaster recovery deployment for production.
-   One zone: the default deployment for handling common tasks.

You do not need to manually deploy an Elasticsearch instance across zones. The system will complete the task for you. When you purchase and use an Elasticsearch instance that is deployed across zones, follow these guidelines:

-   **Nodes** 
    -   You must purchase dedicated master nodes.
    -   The number of data nodes, warm nodes, or client nodes must be a multiple of the number of the zones. For more information about zones, see [Regions and zones](https://www.alibabacloud.com/help/zh/doc-detail/40654.htm)
-   **Index replicas** 
    -   When an Elasticsearch instance is deployed across two zones and one zone becomes unavailable, the other zone is used to provide services. Therefore, the number of index replicas must be greater than or equal to 1.

        **Note:** By default, an Elasticsearch instance deployed across two zones has five primary shards and one replica shard. If you do not have requirements on the reading and writing performance of the instance, use the default setting.

    -   When an Elasticsearch instance is deployed across three zones and one or two of the zones become unavailable, the remaining zones are used to provide services. Therefore, the number of index replicas must be greater than or equal to 2.

        **Note:** By default, an Elasticsearch instance deployed across three zones has five primary shards and one replica shard. You must modify the index template to change the number of index replicas. For more information, see [Index templates](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/indices-templates.html).

        The following example shows how to modify an index template to set the number of index replicas to 2:

        ``` {#codeblock_4py_z1k_zx6}
        PUT _template/template_1
        {
          "template": "*",
          "settings": {
        	"number_of_replicas": 2
          }
        }								
        ```

-   **Cluster settings** 

    The system will automatically configure cluster settings for shard allocation awareness for Alibaba Cloud Elasticsearch instances deployed across zones. For more information, see [Shard allocation awareness](https://www.elastic.co/guide/en/elasticsearch/reference/master/allocation-awareness.html).

    -   For example, if an Alibaba Cloud Elasticsearch instance is deployed across zones cn-hangzhou-f and cn-hangzhou-g, then the cluster settings are as follows:

        |Item|Description|Value|
        |----|-----------|-----|
        |cluster.routing.allocation.awareness.attributes|Indicates the node attributes that are specified as shard allocation awareness attributes for Alibaba Cloud Elasticsearch. The Alibaba Cloud Elasticsearch instance identifies the zone of a node by adding Enode.attr.zone\_id to the startup parameter of the node. Therefore, this parameter is set to zone\_id. **Do not call the Elasticsearch API to change the value of this parameter. Otherwise, exceptions may occur.**|"zone\_id"|
        |cluster.routing.allocation.awareness.force.zone\_id.values|Indicates whether forced awareness is enabled or disabled for replica allocation. Forced awareness prevents a zone from being overloaded when the other zones fail. For example, an index has one primary shard and three replica shards. The Elasticsearch instance is deployed across zones cn-hangzhou-f and cn-hangzhou-g. According to the shard allocation awareness policy, two shards are allocated to zone cn-hangzhou-f and two shards are allocated to zone cn-hangzhou-g. When zone cn-hangzhou-f fails, forced awareness can prevent the system from reallocating the shards of zone cn-hangzhou-f to zone cn-hangzhou-g. Otherwise, zone cn-hangzhou-g has to host all the shards, which may be overloaded. **By default, forced awareness is disabled. You can change the setting as needed.**|\["cn-hangzhou-f", "cn-hangzhou-g"\]|

    -   An Alibaba Cloud Elasticsearch instance deployed across zones adds `-Enode.attr.zone_id` to the startup parameter of a node. For example, if the node is deployed in zone cn-hangzhou-g, then `-Enode.attr.zone_id=cn-hangzhou-g` is added to the startup parameter of the node.

 **VPC** 

Select a VPC network in the corresponding region.

**Note:** If you want to use an Alibaba Cloud ECS instance to access your Alibaba Cloud Elasticsearch instance in a VPC network, make sure that the ECS instance and Elasticsearch instance are connected to the same VPC network.

 **VSwitches** 

After you have specified a VPC network, only VSwitches in the zones supported by the region of your Elasticsearch instance are displayed.

-   If no VSwitch is available in the supported zones, you must manually create a VSwitch in one of the supported zones.
-   Make sure that the number of VSwitch IP addresses is no less than 256. Otherwise, the system displays a message indicating insufficient private IP addresses.

 **Instance types and specifications** 

The following table shows the supported instance types and the relevant specifications. For more information about prices of different instance types, see [Pricing](https://www.alibabacloud.com/product/elasticsearch).

|Instance type|Specification|
|-------------|-------------|
|Cloud disk type|1-Core 2 GB \(testing only\), 2-Core 4 GB, 2-Core 8 GB, 4-Core 16 GB, 8-Core 32 GB, 16-Core 32 GB, and 16-Core 64 GB.|
|Local SSD type|8-Core 32 GB \(894 GB of disk space\) and 16-Core 64 GB \(1,788 GB of disk space\).|
|Local SATA type|8-Core 32 GB \(22,000 GB of disk space\) and 16-Core 64 GB \(44,000 GB of disk space\).|

**Note:** The 1-Core 2 GB instances are only for testing purposes. Do not use them for production purposes. The SLA does not cover these instances.

 **Nodes** 

The number of data nodes that you need to purchase.

**Note:** 

-   You must purchase a minimum of two data nodes. Use caution because a cluster that contains only two data nodes may have the split-brain syndrome.
-   The default number of data nodes is 3. Valid values: from 2 to 50.

 **Dedicated master nodes** 

You can select **Dedicated Master Node** on the Alibaba Cloud Elasticsearch buy page and then purchase dedicated master nodes for your Elasticsearch instance. You can also select **Dedicated Master Node** on the Configuration Upgrade page to purchase or upgrade dedicated master nodes. We recommend that you purchase dedicated master nodes to improve the stability of your service.

Alibaba Cloud Elasticsearch dedicated master nodes support the following specifications. For more information about the prices of different specifications, see [Pricing](https://www.alibabacloud.com/product/elasticsearch).

-   1-Core 2 GB \(currently unavailable\)
-   2-Core 8 GB \(default\)
-   - 4-Core 16 GB
-   8-Core 32 GB
-   16-core 32 GB
-   16-Core 64 GB

**Note:** 

-   The minimum dedicated master node specification that you can purchase on the Alibaba Cloud Elasticsearch instance buy page or Configuration Upgrade page is 2-Core 8 GB. If you have already purchased dedicated master nodes with a specification higher than 1-Core 2 GB, the Dedicated Master Node option on the Configuration Upgrade page will be automatically selected.
-   You can select **Dedicated Master Node** on the Alibaba Cloud Elasticsearch instance buy page to purchase dedicated master nodes. The dedicated master nodes will be billed based the specification that you have selected.
-   You can select **Dedicated Master Node** on the Configuration Upgrade page to purchase dedicated master nodes. You can also upgrade the **dedicated master node specification**. The upgraded dedicated master nodes will be billed based on the new specification. If you are using free dedicated master nodes, they will be billed after you upgrade their specification.
-   If you have purchased dedicated master nodes when purchasing an Alibaba Cloud Elasticsearch instance but the Dedicated Master Node option on the Configuration Upgrade page is not selected, this means that dedicated master nodes that you have purchased are 1-Core 2 GB.

**Note:** 

-   If you have purchased 10 or more data nodes, the dedicated master node feature is automatically disabled. You must manually purchase dedicated master nodes.
-   The default number of dedicated master nodes is 3 and cannot be changed.
-   The default dedicated master node specification is 2-Core 8 GB. You can change the specification based on your business needs.
-   The default storage type of dedicate master nodes is SSD and cannot be changed.
-   The default storage space specified for each dedicated master node is 20 GB and cannot be changed.
-   You cannot release your purchased dedicated master nodes.
-   You cannot downgrade the dedicated master nodes that you have purchased.

 **Client nodes** 

For CPU intensive services, we recommend that you purchase client nodes to share the CPU overheads on the data nodes so that you can improve the performance and stability of your services. For example, you can use client nodes to share the overheads if too many aggregation operations are performed. For more information about client nodes, see [Node types](https://www.elastic.co/guide/en/elasticsearch/reference/6.3/modules-node.html) on the official Elasticsearch website. For more information about the prices of different node specifications, see [Pricing](https://www.alibabacloud.com/product/elasticsearch).

Alibaba Cloud Elasticsearch client nodes support the following specifications. For more information about the prices of different node specifications, see [Pricing](https://www.alibabacloud.com/product/elasticsearch).

-   - 2-Core 8 GB \(default\)
-   - 4-Core 16 GB
-   8-Core 32 GB
-   16-core 32 GB
-   16-Core 64 GB

**Note:** 

-   To purchase client nodes, select **Client Node** on the Alibaba Cloud Elasticsearch instance buy page. The client nodes will be billed based on the specification that you have specified.
-   You can select **Client Node** on the Configuration Upgrade page to purchase client nodes or upgrade the **client node specification**. The upgraded client nodes will be billed based on the new specification.

**Note:** 

-   The default number of client nodes is 2. Valid values: from 2 to 25.
-   The default client node specification is 2-Core 8 GB and cannot be changed.
-   The default storage type of client nodes is ultra disk and cannot be changed.
-   The default storage space specified for each client node is 20 GB and cannot be changed.
-   You cannot release your purchased client nodes.
-   You cannot downgrade the client nodes that you have purchased.

 **Warm node** 

If your business uses the following indexes, we recommend that you purchase warm nodes to handle infrequently queried indexes. This improves the data processing performance and service stability. For more information, see [Hot-warm architecture in Elasticsearch 5.x](https://www.elastic.co/cn/blog/hot-warm-architecture-in-elasticsearch-5-x).

-   Frequently queried or written indexes
-   Infrequently queried or written indexes, typically indexes of records.

 **Hot-warm architecture** 

If you select Warm Node on the Alibaba Cloud Elasticsearch instance buy page or Configuration Upgrade page, `-Enode.attr.box_type` is then added to the node startup parameter.

|Node type|Startup parameter|
|---------|-----------------|
|Data nodes|-Enode.attr.box\_type=hot|
|Warm node|-Enode.attr.box\_type=warm|

Alibaba Cloud Elasticsearch warm nodes support the following specifications. For more information about the prices of different specifications, see [Pricing](https://www.alibabacloud.com/product/elasticsearch).

-   - 2-Core 8 GB \(default\)
-   - 4-Core 16 GB
-   - 8-Core 32 GB

**Note:** 

-   To purchase warm nodes, select **Client Node** on the Alibaba Cloud Elasticsearch instance buy page. The warm nodes will be billed based on the specification that you have specified.
-   You can select **Warm Node** on the Configuration Upgrade page to purchase warm nodes or upgrade the **warm node specification**. The upgraded warm nodes will be billed based on the new specification.
-   Each ultra disk can provide up to 5 TB of storage space. Ultra disks are cost-effective and can be used in scenarios such as logging and analyzing large amounts of data.

    Ultra disks larger than 2 TB cannot be expanded because they are ran in disk arrays or RAID 0.

-   An ultra disk provides up to 5,120 GB \(5 TB\) of storage space. When the storage space that you specify exceeds 2,048 GB, only 2,560 GB, 3,072 GB, 3,584 GB, 4,096 GB, 4,608 GB, and 5,120 GB are available.

**Note:** 

-   The default number of warm nodes is 2. Valid values: from 2 to 50.
-   The default warm node specification is 2-Core 8 GB. You can change the specification based on your business needs.
-   The default storage type of warm nodes is ultra disk and cannot be changed.
-   The default storage space specified for each warm node is 500 GB and cannot be changed.
-   You cannot release your purchased warm nodes.
-   You cannot downgrade the warm nodes that you have purchased.

## Storage {#section_bbk_n4l_zgb .section}

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134283/155834397739957_en-US.png)

 **Storage types** 

**SSD \(default\)**

Each SSD can provide up to 2 TB of storage space. An SSD can be used in online analysis and search scenarios that require high input/output operations per second \(IOPS\) and fast response.

 **Ultra disk** 

Each ultra disk can provide up to 5 TB of storage space. Ultra disks are cost-effective and can be used in scenarios such as logging and analyzing large amounts of data.

**Ultra disks larger than 2 TB cannot be expanded because they are ran in disk arrays or RAID 0.**

 **Storage space per node** 

**SSD:**

-   An SSD can provide up to 2,048 GB \(2 TB\) of storage space.

**Ultra disk:**

-   An ultra disk can provide up to 5,120 GB \(5 TB\) of storage space. When the storage space that you specify exceeds 2,048 GB, only 2,560 GB, 3,072 GB, 3,584 GB, 4,096 GB, 4,608 GB, and 5,120 GB are available.
-   For Alibaba Cloud Elasticsearch instances that have already been purchased, if the disks are smaller than 2 TB, you can only expand them up to 2 TB. If the disks size are larger than 2 TB, you cannot expand the disk size.

## Password {#section_qhh_44l_zgb .section}

When purchasing an Alibaba Cloud Elasticsearch instance, you must set the password for the \`elastic\` account. The password cannot be empty.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134283/155834397739958_en-US.png)

 **Username** 

By default, the `elastic` username is used to log on to Alibaba Cloud Elasticsearch instances and the Kibana console.

**Note:** If the \`elastic\` account is used to log on to your Alibaba Cloud Elasticsearch instance, it may take a certain period of time for the new password to take effect. During this period of time, you may not be able to access your service. Therefore, we recommend that you do not use the \`elastic\` account to access your service. You can create a user role in the Kibana console to access your service.

 **Password** 

Set a password for the `elastic` account according to the password rules.

## Purchase plan {#section_ev5_44l_zgb .section}

You can slide to select a subscription duration to meet your business needs. Supported subscription duration: from 1 to 9 months and from 1 to 3 years.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134283/155834397739959_en-US.png)

 **Subscription duration** 

The **default subscription duration is one year** for **Subscription-based** Alibaba Cloud Elasticsearch instances.

 **Auto renewal** 

-   You can select **Auto Renew** on the buy page to enable this function.
-   You can enable this function for your purchased Subscription-based Elasticesearch instances on the [Renew](https://renew.console.aliyun.com/center#/renew/elasticsearchpre) page.

**Note:** 

-   Monthly subscription: the auto renewal cycle is one month.
-   Annual subscription: the auto renewal cycle is one year.

## Node types {#section_hxl_p4l_zgb .section}

|Node types|Description|
|----------|-----------|
|Data nodes|Act as data nodes if you have purchased dedicated master nodes. Act as data nodes and dedicated master nodes if no dedicated master node has been purchased.|
|Dedicated master nodes|Act as dedicated master nodes.|
|Client nodes|Act as client nodes.|
|Warm node|Act as data nodes and dedicated master nodes if no dedicated master node has been purchased. Act as data nodes if you have purchased dedicated master nodes.|

## Node types and specification families {#section_s4x_p4l_zgb .section}

The following table shows the available node types and the instance types that support the corresponding node type.

|Node types|Instance type|
|----------|-------------|
|Data nodes|Cloud disk type, local SSD type, and local SATA type.|
|Dedicated master nodes|Cloud disk type with a minimum configuration of 2-Core and 8 GB.|
|Client nodes|Cloud disk type with a minimum configuration of 2-Core and 8 GB.|
|Warm node|Cloud disk type with a minimum configuration of 2-Core and 8 GB.|

