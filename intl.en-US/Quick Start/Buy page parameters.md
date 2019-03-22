# Buy page parameters {#concept_nqm_cmk_zgb .concept}

## Billing methods {#section_fcv_l4l_zgb .section}

**Subscription**

**Note:** Promotional offers are currently available for Elasticsearch monthly and annual subscriptions. You can request a refund within five days after you have purchased a Subscription-based Elasticsearch instance. Refunds are not supported after five days from the date of purchase.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134283/155323938639941_en-US.png)

-   If the auto renewal function is disabled, you must log on to the Elasticsearch console and manually renew the overdue instances. For more information, see [Overdue payments](../../../../../intl.en-US/Product Introduction/Overdue payments.md).
-   Elasticsearch instances cannot be manually released in the console.
-   Auto renewal is supported and disabled by default. For more information, see the **Auto renewal** section of this topic.

**Pay-As-You-Go**

**Note:** We recommend that you purchase **Pay-As-You-Go** Elasticsearch instances for testing purposes at the development and testing stages.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134283/155323938639942_en-US.png)

-   Auto renewal is supported. For more information, see [Overdue payments](../../../../../intl.en-US/Product Introduction/Overdue payments.md).
-   You can click **More** and then select Release to manually release an Elasticsearch instance.

## Regions and zones {#section_wsg_m4l_zgb .section}

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134283/155323938639943_en-US.png)

Alibaba Cloud Elasticsearch supports the following [Regions and Zones](../../../../../intl.en-US/General Reference/Regions and Zones.md#).

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

## Instances {#section_rry_m4l_zgb .section}

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134283/155323938739948_en-US.png)

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134283/155323938739950_en-US.png)

**Versions**

Alibaba Cloud Elasticsearch supports the following versions. You can select a version based on your business needs:

-   Elasticsearch 6.3.2 with X-Pack \(recommended\).
-   Elasticsearch 5.5.3 with X-Pack.

**Note:** 

-   [Elasticsearch 6.3.2 release note](https://www.elastic.co/guide/en/elasticsearch/reference/6.3/release-notes-6.3.2.html).
-   [Elasticsearch 5.5.3 release note](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/release-notes-5.5.3.html).

**Network types**

Elasticsearch currently only supports the **VPC** network type.

**VPC networks**

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
-   The default number of data nodes is 3. Valid values: 2-50.

**Dedicated master nodes**

You can select **Dedicated Master Node** on the Alibaba Cloud Elasticsearch buy page and then purchase dedicated master nodes for your Elasticsearch instance. You can also select **Dedicated Master Node** on the Configuration Upgrade page to purchase or upgrade dedicated master nodes. We recommend that you purchase dedicated master nodes to improve the stability of your service.

Alibaba Cloud Elasticsearch dedicated master nodes support the following specifications. For more information about the prices of different specifications, see [Pricing](https://www.alibabacloud.com/product/elasticsearch).

-   1-Core 2 GB \(currently unavailable\)
-   2-Core 8 GB \(default\)
-   4-Core 16 GB
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

For CPU intensive services, we recommend that you purchase client nodes to share the CPU overheads on the data nodes so that you can improve the performance and stability of your services. For example, you can use client nodes to share the overheads if too many aggregation operations are performed. For more information, see [Official Elasticsearch node types](https://www.elastic.co/guide/en/elasticsearch/reference/6.3/modules-node.html). For more information about the prices of different node specifications, see [Pricing](https://www.alibabacloud.com/product/elasticsearch).

Alibaba Cloud Elasticsearch client nodes support the following specifications. For more information about the prices of different node specifications, see [Pricing](https://www.alibabacloud.com/product/elasticsearch).

-   2-Core 8 GB \(default\)
-   4-Core 16 GB
-   8-Core 32 GB
-   16-core 32 GB
-   16-Core 64 GB

**Note:** 

-   To purchase client nodes, select **Client Node** on the Alibaba Cloud Elasticsearch instance buy page. The client nodes will be billed based on the specification that you have specified.
-   You can select **Client Node** on the Configuration Upgrade page to purchase client nodes or upgrade the **client node specification**. The upgraded client nodes will be billed based on the new specification.

**Note:** 

-   The default number of client nodes is 2. Valid values: 2-25.
-   The default client node specification is 2-Core 8 GB and cannot be changed.
-   The default storage type of client nodes is ultra disk and cannot be changed.
-   The default storage space specified for each client node is 20 GB and cannot be changed.
-   You cannot release your purchased client nodes.
-   You cannot downgrade the client nodes that you have purchased.

**Warm nodes**

If you business uses the following indexes, we recommend that you purchase warm nodes to handle infrequently queried indexes. This improves the data processing performance and service stability. For more information, see [Hot-warm architecture in Elasticsearch 5.x](https://www.elastic.co/cn/blog/hot-warm-architecture-in-elasticsearch-5-x).

-   Frequently queried or written indexes
-   Infrequently queried or written indexes, typically indexes of records.

**Hot-warm architecture**

If you select Warm Node on the Alibaba Cloud Elasticsearch instance buy page or Configuration Upgrade page, `-Enode.attr.box_type` is then added to the node startup parameters.

|Node type|Startup parameter|
|---------|-----------------|
|Data nodes|-Enode.attr.box\_type=hot|
|Warm node|-Enode.attr.box\_type=warm|

Alibaba Cloud Elasticsearch warm nodes support the following specifications. For more information about the prices of different specifications, see [Pricing](https://www.alibabacloud.com/product/elasticsearch).

-   2-Core 8 GB \(default\)
-   4-Core 16 GB
-   8-Core 32 GB

**Note:** 

-   To purchase warm nodes, select **Client Node** on the Alibaba Cloud Elasticsearch instance buy page. The warm nodes will be billed based on the specification that you have specified.
-   You can select **Warm Node** on the Configuration Upgrade page to purchase warm nodes or upgrade the **warm node specification**. The upgraded warm nodes will be billed based on the new specification.
-   Each ultra disk can provide up to 5 TB of storage space. Ultra disks are cost-effective and can be used in scenarios such as logging and analyzing large amounts of data.

    Ultra disks larger than 2.5 TB cannot be expanded because they are ran in disk arrays or RAID 0.

-   An ultra disk provides up to 5,120 GB \(5 TB\) of storage space. When the storage space that you specify exceeds 2,048 GB, only 2,560 GB, 3,072 GB, 3,584 GB, 4,096 GB, 4,608 GB, and 5,120 GB are available.

**Note:** 

-   The default number of warm nodes is 2. Valid values: 2-50.
-   The default warm node specification is 2-Core 8 GB. You can change the specification based on your business needs.
-   The default storage type of warm nodes is ultra disk and cannot be changed.
-   The default storage space specified for each warm node is 500 GB and cannot be changed.
-   You cannot release your purchased warm nodes.
-   You cannot downgrade the warm nodes that you have purchased.

## Storage {#section_bbk_n4l_zgb .section}

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134283/155323938739957_en-US.png)

**Storage types**

**SSD \(default\)**

Each SSD can provide up to 2 TB of storage space. An SSD can be used in online analysis and search scenarios that require high input/output operations per second \(IOPS\) and fast response.

**Ultra disk**

Each ultra disk can provide up to 5 TB of storage space. Ultra disks are cost-effective and can be used in scenarios such as logging and analyzing large amounts of data.

**Ultra disks larger than 2.5 TB cannot be expanded because they are running in disk arrays or RAID 0.**

**Storage space per node**

**SSD:**

-   An SSD can provide up to 2,048 GB \(2 TB\) of storage space.

**Ultra disk:**

-   An ultra disk can provide up to 5,120 GB \(5 TB\) of storage space. When the storage space that you specify exceeds 2,048 GB, only 2,560 GB, 3,072 GB, 3,584 GB, 4,096 GB, 4,608 GB, and 5,120 GB are available.
-   For Alibaba Cloud Elasticsearch instances that have already been purchased, if the disks are smaller than 2 TB, you can only expand them up to 2 TB. If the disks size are larger than 2 TB, you cannot expand the disk size.

## Password {#section_qhh_44l_zgb .section}

When purchasing an Alibaba Cloud Elasticsearch instance, you must set the password for the \`elastic\` account. The password cannot be empty.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134283/155323938739958_en-US.png)

**Username**

By default, the `elastic` username is used to log on to Alibaba Cloud Elasticsearch instances and the Kibana console.

**Note:** If the elastic account is used to log on to your Alibaba Cloud Elasticsearch instance, it may take a certain period of time for the new password to take effect. During this period of time, you may not be able to access your service. Therefore, we recommend that you do not use the \`elastic\` account to access your service. You can create a user role in the Kibana console to access your service.

**Password**

Set a password for the `elastic`account according to the password rules.

## Purchase plan {#section_ev5_44l_zgb .section}

You can slide to select a subscription duration to meet your business needs. Supported subscription durations: 1-9 months and 1-3 years.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134283/155323938739959_en-US.png)

**Subscription duration**

The **default subscription duration is one year** for Subscription-based Alibaba Cloud Elasticsearch instances.

**Auto renewal**

-   - You can select **Auto Renew** on the buy page to enable this function.
-   You can enable this function for your purchased Subscription-based Elasticesearch instances on the [Renew](https://renew.console.aliyun.com/center#/renew/elasticsearchpre) page.

**Note:** 

-   Monthly subscription: the auto renewal cycle is one month.
-   Annual subscription: the auto renewal cycle is one year.

## Node types {#section_hxl_p4l_zgb .section}

|Node type|Description|
|---------|-----------|
|Data nodes|Act as data nodes if you have purchased dedicated master nodes. Act as data nodes and dedicated master nodes if no dedicated master node has been purchased.|
|Dedicated master nodes|Act as dedicated master nodes.|
|Client nodes|Act as client nodes.|
|Warm nodes|Act as data nodes and dedicated master nodes if no dedicated master node has been purchased. Act as data nodes if you have purchased dedicated master nodes.|

## Node types and specification families {#section_s4x_p4l_zgb .section}

The following table shows the available node types and the instance types that support the corresponding node type.

|Node type|Instance type|
|---------|-------------|
|Data nodes|Cloud disk type, local SSD type, and local SATA type.|
|Dedicated master nodes|Cloud disk type with a minimum configuration of 2-Core and 8 GB.|
|Client nodes|Cloud disk type with a minimum configuration of 2-Core and 8 GB.|
|Warm nodes|Cloud disk type with a minimum configuration of 2-Core and 8 GB.|

