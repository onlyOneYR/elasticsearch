# Prerequisites {#concept_wv4_1mk_zgb .concept}

**Access Elasticsearch from the Internet**

You can use the **public network address** of an Alibaba Cloud Elasticsearch instance to access the instance from the Internet. For more information, see [Basic Information](../../../../reseller.en-US/User Guide/Instance management/Basic Information.md).

**Note:** 

-   To use public network addresses, you must configure the [Public IP address whitelist](../../../../reseller.en-US/User Guide/Instance management/Security settings.md#section_ux5_yct_zgb). By default, Elasticsearch forbids all public network addresses.
-   You cannot use the transport client to access Alibaba Cloud Elasticsearch 6.7.0 with Commercial Feature through port 9300.
-   You cannot use the transport client to access Alibaba Cloud Elasticsearch 6.3.2 with Commercial Feature through port 9300.

**Access Elasticsearch from a VPC network**

In a VPC network, you can use an ECS instance to access the **internal network address** of an Elasticsearch instance . For more information, see [Basic Information](../../../../reseller.en-US/User Guide/Instance management/Basic Information.md).

**Note:** 

-   You cannot use the transport client to access Alibaba Cloud Elasticsearch 6.7.0 with Commercial Feature through port 9300.
-   You cannot use the transport client to access Alibaba Cloud Elasticsearch 6.3.2 with Commercial Feature through port 9300.
-   **Data security is not guaranteed** when you access an Alibaba Cloud Elasticsearch instance from the Internet. For data security, we recommend that you purchase an ECS instance that meets the requirements described in the following **Purchase instructions**. You can then use the ECS instance to access the **internal network address** of the Elasticsearch instance from a VPC network.

## Purchase instructions {#section_x52_lmk_zgb .section}

-   Before you purchase an Alibaba Cloud Elasticsearch instance, make sure that you have created a VPC network and VSwitch in the same region as the Elasticsearch instance.
-   If you choose to use an Alibaba Cloud ECS instance to access an Alibaba Cloud Elasticsearch instance, make sure that the Elasticsearch instance and ECS instance are in the same region.
    -   If your Alibaba Cloud ECS instance is connected to a VPC network, make sure that the ECS instance and your Alibaba Cloud Elasticsearch instance are connected to the same VPC network.
    -   If your Alibaba Cloud ECS instance is connected to the classic network, you must make sure that the ECS instance meets the requirements described in the **Supported VPC CIDR blocks** topic in [Classic network errors](../../../../reseller.en-US/FAQ/Classic network errors.md).
-   You can only select VSwitches in the same zone, region, and VPC network as the Elasticsearch instance.
    -   If no VSwitch is available in the supported zones, you must manually create a VSwitch in one of the supported zones.
    -   Make sure that the number of VSwitch IP addresses is no less than 200. Otherwise, the system displays a message indicating insufficient private IP addresses.

**Note:** 

-   If your Alibaba Cloud ECS instance and Elasticsearch instance are deployed in the same VPC network, same region, but different zones, you must create a VSwitch in the zone where the ECS instance is deployed to ensure that the ECS instance can access your Elasticsearch instance.
-   New Elasticsearch users can choose to purchase free trial Elasticsearch instances for testing.

## Guidelines for purchasing Subscription-based Alibaba Cloud Elasticsearch instances {#section_tvt_lmk_zgb .section}

Discounts are offered for Subscription-based Alibaba Cloud Elasticsearch instances based on the subscription duration.

## Guidelines for Elasticsearch instance specifications {#section_crb_mmk_zgb .section}

Alibaba Cloud has provided multiple types of Elasticsearch instances. The 1-Core 2 GB instances are only for testing purposes. These instances are not suitable for production. A 1-Core 2 GB instance is configured with the minimum specification, which consumes a large amount of resources and may cause service instability in production. In addition, the 1-Core 2 GB instance is not covered by the Elasticsearch Service Level Agreement \(SLA\). For more information, see [Pricing](https://www.alibabacloud.com/product/elasticsearch).

-   If you are using a 1-Core 2 GB Elasticsearch instance in production, we recommend that you upgrade the instance to ensure the stability and availability of your service.
-   The minimum specification for Elasticsearch instances used in production is 2-Core 4 GB.

## Guidelines for purchasing Elasticsearch instance disks {#section_pyg_mmk_zgb .section}

Before determining the disk size of an Alibaba Cloud Elasticsearch node, learn about what type of data is saved in the disk. Determine the disk size based on your business requirements.

We recommend that you preserve disk space for storing the system logs when selecting a disk. System logs include the operation log and monitoring log.

## Storage space usage {#section_tsm_mmk_zgb .section}

**Note:** The storage space of a disk determines the amount of Elasticsearch cluster logs and X-Pack monitoring indexes that you can store.

-   Stores user data that has been pushed to Elasticsearch.
-   Stores Elasticsearch replicas. The number of replicas is user-configurable. However, Elasticsearch will retain a minimum of one replica.
-   Stores Elasticsearch cluster logs. The amount of storage space that Elasticsearch consumes increases with the number of queries and pushes that Elasticsearch has received. By default, Elasticsearch only keeps cluster logs for seven days after creation. Elasticsearch cluster logging is not currently available.
    -   Stores the operation Log.
    -   Stores the access log.
    -   Stores slow logs.
-   Stores X-Pack monitoring indexes for troubleshooting exceptions. X-Pack is an Elasticsearch component. Monitoring indexes include the following:
    -   .monitoring-es-6-2018.01.08. The indexes consume a large amount of storage space. Elasticsearch creates only one index each day and keeps indexes for seven days after creation.
    -   .monitoring-kibana-6-2018.01.08. The amount of storage space that the indexes consume increases with the number of the indexes. Elasticsearch creates only one index each day and keeps indexes for seven days after creation.
    -   .watcher-history-3-2018.01.08. The indexes consume only a small amount of storage space. Elasticsearch creates only one index each day and keeps all indexes that have been created. You must manually delete the indexes that you no longer need.

**Note:** When an Elasticsearch instance has a high disk usage, such as a disk usage higher than 80%, the Elasticsearch cluster health status changes to yellow or red, and the Elasticsearch instance cannot be restarted. Therefore, before you restart an Elasticsearch instance, make sure that the health status of the instance is green. For more information, see [Instance management](../../../../reseller.en-US/User Guide/Instance management/Instance management.md).

