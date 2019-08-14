# Buy page parameters {#concept_nqm_cmk_zgb .concept}

This topic describes the parameters on the Alibaba Cloud Elasticsearch buy page.

## Billing method {#section_fcv_l4l_zgb .section}

Alibaba Cloud Elasticsearch supports both the **subscription** and **pay-as-you-go** billing methods.

-   **Subscription**: Currently, Alibaba Cloud Elasticsearch offers promotional discount based on the subscription duration: monthly or annual. Alibaba Cloud Elasticsearch subscription instances purchased on the Alibaba Cloud International site do not support conditional refunds or unconditional refunds within five days. If you want to cancel your subscription, make sure that all data on the subscription instance has been backed up. Sign in on the Alibaba Cloud International site, and select **Console** \> **Billing Management** \> **Renew**, and manually disable the **auto-renewal** feature. Before the end of the current billing cycle, you can still use the Elasticsearch instance. However, the subscription fee is not refunded. Alibaba Cloud will stop renewing your instance in the next billing cycle.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134283/156574660139941_en-US.png)

    -   If the auto-renewal feature is disabled, you must log on to the Elasticsearch console and manually renew the overdue instances. For more information, see [Overdue payments](../../../../reseller.en-US/Product Introduction/Overdue payments.md).
    -   Elasticsearch instances cannot be manually released in the console.
    -   Auto-renewal is supported and disabled by default. For more information, see the **Auto-renewal** section in this topic.
-   **Pay-as-you-go**: We recommend that you purchase Alibaba Cloud Elasticsearch **pay-as-you-go** instances for testing purposes at the development and testing stages.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134283/156574660139942_en-US.png)

    -   Auto-renewal is supported. For more information, see [Overdue payments](../../../../reseller.en-US/Product Introduction/Overdue payments.md).
    -   You can log on to the Elasticsearch console, click **More**, and then select Release to manually release a pay-as-you-go instance.

## Parameters {#section_1lw_wnm_zaf .section}

|Parameter|Description|
|---------|-----------|
|**Region**|For more information, see [Regions and zones](#section_wsg_m4l_zgb).|
|**Zone**|For more information, see [Regions and zones](#section_wsg_m4l_zgb).|
|**Resource Group**|We recommend that you select the **Default Resource Group**.|
|**Version**|Elasticsearch 6.7, 6.3, and 5.5.3 are supported. [Elasticsearch 6.3.2 Release Notes](https://www.elastic.co/guide/en/elasticsearch/reference/6.3/release-notes-6.3.2.html).

 [Elasticsearch 5.5.3 Release Notes](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/release-notes-5.5.3.html).

 |
|**Network Type**|Currently, only **VPC** is supported.|
|**Number of Available Zones**|**1-AZ**, **2-AZ**, and **3-AZ** are supported. For more information, see [Number of zones](#section_oit_gux_orc).|
|**VPC**|Select a VPC network in the corresponding region. **Note:** If you want to use an Alibaba Cloud ECS instance to access your Elasticsearch instance, then the ECS instance must be connected to the same VPC network as your Elasticsearch instance.

 |
|**VSwitch**|After you have specified a VPC network, only VSwitches in the zones supported by the region of your Elasticsearch instance are displayed.|
|**Specification Family**|For more information, see [Instance types and specifications](#section_aah_v30_6ua).|
|**Instance Type**|For more information, see [Instance types and specifications](#section_aah_v30_6ua).|
|**Nodes**|The number of data nodes that you want to purchase. **Note:** 

-   You must purchase a minimum of two data nodes. Use caution because a cluster that contains only two data nodes may have the split-brain syndrome.
-   The default number of data nodes is 3. Valid values: from 2 to 50.

 |
|**Dedicated Master Node**|For more information, see [Dedicated master nodes](#section_yyc_rc6_ww7).|
|**Client Node**|For more information, see [Client nodes](#section_1sy_pd8_ri2).|
|**Warm Node**|For more information, see [Warm nodes](#section_i5k_j8z_o3z).|
|**Disk Type**|**SSD Cloud Disk** and **Ultra Disk** are supported. -   **SSD Cloud Disk** \(default\): Supports up to 2 TiB of storage space. SSD cloud disks are suitable for online data analysis and search that require high throughput and fast response.
-   **Ultra Disk**: Supports up to 5 TiB of storage space. Ultra disks are cost-effective and can be used in scenarios such as logging and analyzing large amounts of data.

**Note:** Ultra disks larger than 2.5 TiB cannot be expanded because they run in disk arrays or RAID 0.


 |
|**Node Storage**|The storage space per data node varies depending on the type of the disk. -   **SSD Cloud Disk**: Supports up to 2,048 GiB \(2 TiB\) of storage space. Unit: GiB.
-   **Ultra Disk**:
    -   An ultra disk can provide up to 5,120 GiB \(5 TiB\) of storage space. Ultra disks larger than 2,048 GiB include 2,560 GiB, 3,072 GiB, 3,584 GiB, 4,096 GiB, 4,608 GiB, and 5,120 GiB.
    -   For purchased Alibaba Cloud Elasticsearch instances, if the disks are smaller than 2 TiB, you can only expand them up to 2 TiB. If the disks are larger than 2 TiB, you cannot expand the disk size.

 |
|**Username**|By default, the elastic account is used to log on to Alibaba Cloud Elasticsearch instances and the Kibana console. **Note:** If you use the elastic account to access your Alibaba Cloud Elasticsearch service, after you reset the password of the elastic account, it may take a period of time for the new password to take effect. When Elasticsearch is applying the new password, your services will be affected. We recommend that you do not use the elastic account to access your Elasticsearch service. We recommend that you log on to the Kibana console and create a user account with the required role to access Elasticsearch.

 |
|**Password**|The password of the elastic account. You must enter a password.|
|**Duration**|Only **subscription** instances support this parameter. The default subscription duration is one month. You can slide to select a subscription duration to meet your business needs. Supported subscription duration: from 1 to 9 months or from 1 to 3 years.|
|**Auto Renew**| -   To enable this feature, select **Auto Renew** on the buy page.
-   For purchased Alibaba Cloud Elasticsearch **subscription** instances, you can enable the auto-renewal feature for these instances in [Renew](https://renew.console.aliyun.com/center#/renew/elasticsearchpre).

**Note:** 

    -   Monthly subscription: The auto-renewal cycle is one month.
    -   Annual subscription: The auto-renewal cycle is one year.

 |

## Regions and zones {#section_wsg_m4l_zgb .section}

Alibaba Cloud Elasticsearch supports the following [Regions and zones](../../../../reseller.en-US/General Reference/Regions and zones.md#):

-   China \(Hangzhou\): Zone B, Zone F, Zone G, Zone H, Zone I, and Zone E.
-   China \(Beijing\): Zone E, Zone A, Zone C, and Zone D.
-   China \(Shanghai\): Zone B and Zone D.
-   China \(Shenzhen\): Zone C.
-   India \(Mumbai\): Zone A.
-   Singapore: Zone A and Zone B.
-   Hong Kong \(China\): Zone B and Zone C.
-   US \(Silicon Valley\): Zone A and Zone B.
-   Malaysia \(Kuala Lumpur\): Zone A and Zone B.
-   Germany \(Frankfurt\): Zone A and Zone B.
-   Japan \(Tokyo\): Zone A.
-   Australia \(Sydney\): Zone A.
-   Indonesia \(Jakarta\): Zone A.
-   China \(Qingdao\): Zone B and Zone C.

## Number of zones {#section_oit_gux_orc .section}

You can specify the number of zones when you purchase an Alibaba Cloud Elasticsearch instance. If you choose two or three zones, the Elasticsearch instance is deployed across zones. Currently, you can deploy an Elasticsearch instance across zones in the China \(Hangzhou\), China \(Beijing\), China \(Shanghai\), or China \(Shenzhen\) region. You can use the following methods to deploy an Elasticsearch instance:

-   One zone: the default deployment for handling common tasks.
-   Across two zones: a disaster recovery deployment for production.
-   Across three zones: a deployment for implementing high availability. We recommend that you use this deployment for production where high availability is required.

If you choose to deploy an Elasticsearch instance across zones, you do not need to manually specify the zones. The system will select the zones for you. When you purchase and use an Alibaba Cloud Elasticsearch instance deployed across zones, follow these guidelines:

-   Nodes 
    -   You must purchase dedicated master nodes.
    -   The number of data nodes, warm nodes, or client nodes must be a multiple of the number of the zones. For more information about zones, see [Regions and zones](https://www.alibabacloud.com/help/zh/doc-detail/40654.htm).
-   Index replicas 
    -   When an Elasticsearch instance is deployed across two zones and one zone becomes unavailable, the other zone is used to provide services. Therefore, the number of index replicas must be greater than or equal to 1.

        **Note:** By default, an Elasticsearch instance has five primary shards and one replica. If you do not have specific requirements on the throughput of the instance, you can use the default setting.

    -   When an Elasticsearch instance is deployed across three zones and one or two of the zones become unavailable, the remaining zones are used to provide services. Therefore, the number of index replicas must be greater than or equal to 2.

        **Note:** By default, an Elasticsearch instance has five primary shards and one replica. Therefore, you need to manually modify the index template to adjust the default number of replicas. For more information, see [Index templates](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/indices-templates.html).

        The following sample code shows how to modify the index template to set the number of replicas to 2.

        ``` {#codeblock_egc_jh1_ik5}
        PUT _template/template_1
        {
          "template": "*",
          "settings": {
            "number_of_replicas": 2
          }
        }                                
        ```

-   Cluster settings 

    The system will automatically configure cluster settings for shard allocation awareness for Alibaba Cloud Elasticsearch instances deployed across zones. For more information, see [Shard allocation awareness](https://www.elastic.co/guide/en/elasticsearch/reference/master/allocation-awareness.html).

    -   If an Alibaba Cloud Elasticsearch instance is deployed in the cn-hangzhou-f and cn-hangzhou-g zones, the cluster settings are as follows:

        |Parameter|Description|Value|
        |---------|-----------|-----|
        |cluster.routing.allocation.awareness.attributes| **Note:** Do not modify this parameter by using the Elasticsearch API. Otherwise, an exception may occur.

 This parameter specifies the node attributes that are specified as shard allocation awareness attributes for Alibaba Cloud Elasticsearch. The Alibaba Cloud Elasticsearch instance identifies the zone of a node by adding `Enode.attr.zone_id` to the startup configuration of the node. Therefore, this parameter is set to `zone_id`.|"zone\_id"|
        |cluster.routing.allocation.awareness.force.zone\_id.values|Enables or disables forced awareness for replica allocation. Forced awareness prevents a zone from being overloaded when other zones fail. For example, an index has one primary shard and three replica shards. The Elasticsearch instance is deployed across the cn-hangzhou-f and cn-hangzhou-g zones. According to the shard allocation awareness policy, two shards are allocated to the zone cn-hangzhou-f and two shards are allocated to the zone cn-hangzhou-g. If the cluster.routing.allocation.awareness.force.zone\_id.values parameter is set, when the zone cn-hangzhou-f fails, forced awareness can prevent the system from reallocating the shards of the zone cn-hangzhou-f to the zone cn-hangzhou-g. Otherwise, the zone cn-hangzhou-g has to host all the shards, which may be overloaded. **Note:** By default, forced awareness is disabled. You can change the setting as needed.

 |\["cn-hangzhou-f", "cn-hangzhou-g"\]|

    -   Alibaba Cloud Elasticsearch instances deployed across zones will add `-Enode.attr.zone_id` to the startup configuration of the nodes. For example, if a node is deployed in the zone cn-hangzhou-g, then `-Enode.attr.zone_id=cn-hangzhou-g` is added to the startup configuration of the node.
-   Switch over and back 

    Alibaba Cloud Elasticsearch instances deployed across zones support the switch over and switch back operations.

    -   Switch over

        **Note:** To make sure that the Elasticsearch instance can read and write data normally after the switchover is complete, we recommend that the Elasticsearch instance has at least one replica.

        When one or more nodes of the Elasticsearch instance is a zone fail, you can switch the network traffic to other zones. On the Basic Information page of the Elasticsearch console, click **Node Visualization**, move your mouse pointer to the zone where the nodes fail, and click **Switch Over**. Follow the instructions to complete the switchover operation.

        **Note:** 

        -   The status of the specified zone changes from Enabled to Disabled.
        -   Network traffic sent from clients to the zone is then forwarded to other enabled zones and the nodes in this zone are removed from the cluster.
        -   To ensure that the indexes can be read and written normally and the computing resources are sufficient, Alibaba Cloud Elasticsearch will add new nodes to other enabled zones. These nodes may include dedicated master nodes, client nodes, data nodes, and Kibana node.
        Before you perform the switch over operation, if your index has one replica, then the status of the cluster displays yellow after the swtichover process is complete. After the swtichover process is complete, you can reference the following example to set the cluster parameters so that the shards in the disabled zone can be allocated to other zones. After the shards are allocated, the status of the cluster displays green.

        ``` {#codeblock_hou_rvn_w02}
        PUT /_cluster/settings
        {
            "persistent" : {
                "cluster.routing.allocation.awareness.force.zone_id.values" : {"0": null, "1": null, "2": null}
            }
        }
        ```

    -   Switch back

        On the Basic Information page of the Elastcisearch console, click **Data Visualization**, move your mouse pointer to the zone that you want to recover, and click **Switch Back**. Follow the instructions to complete the switchback operation.

        **Note:** 

        -   The status of the zone changes from Disabled to Enabled.
        -   Network traffic sent from clients is then forwarded to the recovered zone and nodes in this zone are added to the cluster again.
        -   During the switchback process, Alibaba Cloud Elasticsearch will remove the nodes that are added during the switchover process. These nodes may include dedicated master nodes, client nodes, and data nodes. When Elasticsearch removes data nodes, it will migrate the data on the data nodes to other data nodes.

## Instance types and specifications {#section_aah_v30_6ua .section}

The following table shows the supported instance types and relevant specifications. For more information about prices of different instance types, see [Pricing](https://www.alibabacloud.com/product/elasticsearch).

|Instance type|Specification|
|-------------|-------------|
|Cloud disk type|1-core 2 GB \(testing only\), 2-core 4 GB, 2-core 8 GB, 4-core 16 GB, 8-core 32 GB, 16-core 32 GB, and 16-core 64 GB.|
|Local SSD type|8-core 32 GB \(894 GiB of disk space\) and 16-core 64 GB \(1,788 GiB of disk space\).|
|Local SATA type|8-core 32 GB \(22,000 GiB of disk space\) and 16-core 64 GB \(44,000 GiB of disk space\).|

**Note:** The 1-core 2 GB instances are only for testing purposes. Do not use them for production purposes. The SLA does not cover these instances.

## Dedicated master nodes {#section_yyc_rc6_ww7 .section}

**Note:** 

-   If you have purchased 10 or more data nodes, the dedicated master node feature is automatically disabled. You must manually purchase dedicated master nodes.
-   The default number of dedicated master nodes is 3 and cannot be changed.
-   The default dedicated master node specification is 2-core 8 GB. You can change the specification based on your business needs.
-   The default storage type of dedicate master nodes is SSD cloud disk and cannot be changed.
-   The default storage space specified for each dedicated master node is 20 GiB and cannot be changed.
-   You cannot release your purchased dedicated master nodes.
-   You cannot downgrade your purchased dedicated master nodes.

We recommend that you purchase dedicated master nodes to improve the stability of your services. When you purchase an Alibaba Cloud Elasticsearch instance, click **Yes** on the right side of **Dedicated Master Node** to purchase dedicated master nodes. You can also purchase or upgrade dedicated master nodes on the [Cluster upgrade](../../../../reseller.en-US/User Guide/Instance management/Cluster upgrade.md#) page. The dedicated master nodes will be billed based on the new specification.

Alibaba Cloud Elasticsearch supports the following types of dedicated master nodes. For more information about pricing, see [Pricing](https://www.alibabacloud.com/product/elasticsearch).

-   1-core 2 GB \(currently unavailable\)
-   2-core 8 GB \(default\)
-   4-core 16 GB
-   8-core 32 GB
-   16-core 32 GB
-   16-core 64 GB

**Note:** 

-   The minimum specification of dedicated master nodes that you can purchase on the Elasticsearch instance buy page or upgrade page is **2-core 8 GB**. If you have purchased dedicated master nodes with a specification higher than **1-core 2 GB**, then the **Yes** option is selected for **Dedicated Master Node** on the cluster upgrade page.
-   On the Upgrade page, click **Yes** on the right side of **Dedicated Master Node** to purchase or upgrade **dedicated master nodes**. The nodes will be billed based on the new specification. If your dedicated master nodes are free nodes provided by Elasticsearch, then we will start charging these dedicated master nodes after you upgrade them.
-   If you have already purchased **dedicated master nodes** when you purchase an Alibaba Cloud Elasticsearch instance and the **Dedicated Master Node** parameter on the cluster upgrade page displays **No**, this indicates that the specification of the purchased dedicated master nodes is **1-core 2 GB**.

## Client nodes {#section_1sy_pd8_ri2 .section}

**Note:** 

-   The default number of client nodes is 2. Valid values: from 2 to 25.
-   The default client node specification is 2-core 8 GB and cannot be changed.
-   The default storage type of client nodes is ultra disk and cannot be changed.
-   The default storage space specified for each client node is 20 GiB and cannot be changed.
-   You cannot release your purchased client nodes.
-   You cannot downgrade your purchased client nodes.

You can purchase client nodes to share the CPU overheads of data nodes in order to improve the computing performance and service stability. For CPU-intensive services, we recommend that you purchase client nodes. For example, you can use client nodes to share the overheads if too many aggregation operations are performed. For more information, see [Elasticsearch official node types](https://www.elastic.co/guide/en/elasticsearch/reference/6.3/modules-node.html).

When you purchase an Alibaba Cloud Elasticsearch instance, click **Yes** on the right side of **Client Node** to purchase client nodes. You can also purchase or upgrade client nodes on the [Cluster upgrade](../../../../reseller.en-US/User Guide/Instance management/Cluster upgrade.md#) page. The client nodes will be charged based on the new specification.

Alibaba Cloud Elasticsearch supports the following types of client nodes. For more information about pricing, see [Pricing](https://www.alibabacloud.com/product/elasticsearch).

-   2-core 8 GB \(default\)
-   4-core 16 GB
-   8-core 32 GB
-   16-core 32 GB
-   16-core 64 GB

## Warm nodes {#section_i5k_j8z_o3z .section}

**Note:** 

-   The default number of warm nodes is 2. Valid values: from 2 to 50.
-   The default warm node specification is 2-core 8 GB. You can change the specification based on your business needs.
-   The default storage type of warm nodes is ultra disk and cannot be changed.
-   The default storage space specified for each warm node is 500 GiB and cannot be changed.
-   You cannot release your purchased warm nodes.
-   You cannot downgrade your purchased warm nodes.

If your business includes the following types of indexes at the same time, we recommend that you purchase warm nodes to implement the hot-warm architecture. The hot-warm architecture can improve the computing performance and service stability of Elasticsearch. For more information, see [ES 5.x hot-warm architecture](https://www.elastic.co/cn/blog/hot-warm-architecture-in-elasticsearch-5-x).

-   Frequently queried or written indexes
-   Infrequently queried or written indexes, typically indexes of records.

When you purchase an Alibaba Cloud Elasticsearch instance, click **Yes** on the right side of **Warm Node** to purchase warm nodes. You can also purchase or upgrade warm nodes on the [Cluster upgrade](../../../../reseller.en-US/User Guide/Instance management/Cluster upgrade.md#) page. The warm nodes will be billed based on the new specification.

 Hot-warm architecture 

After you purchase warm nodes, the system will add `-Enode.attr.box_type` to the startup configuration of the nodes as follows:

|Node type|Startup parameter|
|---------|-----------------|
|Data node|-Enode.attr.box\_type=hot|
|Warm node|-Enode.attr.box\_type=warm|

Alibaba Cloud Elasticsearch supports the following types of warm nodes. For more information about pricing, see [Pricing](https://www.alibabacloud.com/product/elasticsearch).

-   2-core 8 GB \(default\)
-   4-core 16 GB
-   8-core 32 GB

**Note:** 

-   Each ultra disk can provide up to 5 TiB of storage space. Ultra disks are cost-effective and can be used in scenarios such as logging and analyzing large amounts of data.

    Ultra disks larger than 2 TiB cannot be expanded because they run in disk arrays or RAID 0.

-   An ultra disk provides up to 5,120 GiB \(5 TiB\) of storage space. Ultra disks larger than 2,048 GiB include 2,560 GiB, 3,072 GiB, 3,584 GiB, 4,096 GiB, 4,608 GiB, and 5,120 GiB.

## Notes {#section_hxl_p4l_zgb .section}

-   Node types 

    The following table lists the node types supported by Alibaba Cloud Elasticsearch.

    |Node type|Description|
    |---------|-----------|
    |[Data node](https://www.elastic.co/guide/en/elasticsearch/reference/6.3/modules-node.html#data-node)|Used as data nodes if you have purchased dedicated master nodes. If no dedicated master node has been purchased, data nodes are also used as dedicated master nodes.|
    |[Dedicated master node](https://www.elastic.co/guide/en/elasticsearch/reference/6.3/modules-node.html#master-node)|Used as dedicated master nodes.|
    |[Client node](https://www.elastic.co/guide/en/elasticsearch/reference/6.3/modules-node.html#coordinating-only-node)|Used as client nodes.|
    |[Warm node](https://www.elastic.co/cn/blog/hot-warm-architecture-in-elasticsearch-5-x?spm=a2c4g.11186623.2.35.44ca610bMs12U2)|Used as data nodes and dedicated master nodes if no dedicated master node has been purchased. If you have purchased dedicated master nodes, warm nodes are only used as data nodes.|

-   Node types and specification families 

    The following table lists the node types and corresponding specification families supported by Alibaba Cloud Elasticsearch.

    |Node type|Specification family|
    |---------|--------------------|
    |Data node|Cloud disk type, local SSD type, and local SATA type.|
    |Dedicated master node|Cloud disk type with a minimum configuration of 2-core and 8 GB.|
    |Client node|Cloud disk type with a minimum configuration of 2-core and 8 GB.|
    |Warm node|Cloud disk type with a minimum configuration of 2-core and 8 GB.|


