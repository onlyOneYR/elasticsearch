# Prerequisites {#concept_wv4_1mk_zgb .concept}

**Access Elasticsearch from the Internet**

You can use a **public address** to access an Elasticsearch instance from the Internet. For more information, see [Basic information](../../../../intl.en-US/User Guide/Instance management/Basic information.md).

**Note:** 

-   To access an Elasticsearch instance from the Internet, you must first create a [Public IP address whitelist](../../../../intl.en-US/User Guide/Instance management/Security settings.md#section_ux5_yct_zgb). By default, no public addresses are allowed to access Elasticsearch.
-   Alibaba Cloud Elasticsearch 6.3.2 with X-Pack does not support `9300` port.

**Access Elasticsearch from an intranet**

You can use a **private address** to access an Elasticsearch instance from an ECS instance in a VPC. For more information, see [Basic information](../../../../intl.en-US/User Guide/Instance management/Basic information.md).

**Note:** 

-   Alibaba Cloud Elasticsearch 6.3.2 with X-Pack does not support `9300` port.
-   **Information security is not guaranteed** when you access an Elasticsearch instance from the Internet. To secure access to Elasticsearch, we recommend that you purchase an ECS instance that meets the requirements described in the following **purchase instructions** and use a **private address** to access the Elasticsearch instance from a VPC.

## Purchase instructions {#section_x52_lmk_zgb .section}

-   Before you purchase an Elasticsearch instance, make sure that you have created a VPC and VSwitch in the same region as the Elasticsearch instance.
-   If you choose to access an Elasticsearch instance from an ECS instance, make sure that the Elasticsearch instance and ECS instance are in the same region.
    -   - If your Alibaba Cloud ECS instance is deployed in a VPC, make sure that the ECS instance and your Alibaba Cloud Elasticsearch instance are connected to the same VPC.
    -   If the network type of your ECS instance is Classic, make sure that the instance meets the requirements as described in **VPC network segments** of [Classic network errors](../../../../intl.en-US/FAQ/Classic network errors.md).
-   You can only select VSwitches in the same zone, region, and VPC as the Elasticsearch instance.
    -   You must create a VSwitch if no VSwitch is available in the zone and VPC where the Elasticsearch instance resides.
    -   Make sure that the number of VSwitch IP addresses is no less than 200. Otherwise, the system displays a message indicating insufficient private IP addresses.

**Note:** 

-   If your Alibaba Cloud Elasticsearch instance and ECS instance share the same VPC and region but reside in different zones, you must create a VSwitch in the zone where the ECS instance resides to ensure that the ECS instance can access your Elasticsearch instance.
-   New Elasticsearch users can choose to purchase free trial Elasticsearch instances for testing.

## Elasticsearch Subscription {#section_tvt_lmk_zgb .section}

All discounts for Alibaba Cloud Elasticsearch instances are offered based on the subscription duration that you have specified.

You cannot request a refund with no reason in five days. Subscription products on the international site do not support refunds. If you want to terminate your subscription services, make sure that the data backup is complete, and log on to the Alibaba cloud **console** \> **Billing Management** \> **Renewal** to manually disable the Don't Renew button. You can still use Alibaba Cloud services before the current billing cycle. The Subscription-based fees will not be returned to you. Auto renewal will be terminated in the next billing cycle.

## Elasticsearch instance types {#section_crb_mmk_zgb .section}

Alibaba Cloud has provided multiple types of Elasticsearch instances. The 1-Core 2 GB instances are only for testing purposes. These instances are not suitable for production. An Elasticsearch instance with the minimum specification consumes a large amount of resources and may cause service instability in production. In addition, the 1 Core 2 GB specification is not covered by the Elasticsearch Service Level Agreement \(SLA\). For more information, see [Product Overview](https://www.alibabacloud.com/product/elasticsearch).

-   If you are using a 1-Core 2 GB Elasticsearch instance in production, we recommend that you upgrade the instance to ensure the stability and availability of your service.
-   The minimum specification for Elasticsearch instances used in production is 2-Core 4 GB.

## Purchase Elasticsearch instance disks {#section_pyg_mmk_zgb .section}

Before determining the disk size of an Alibaba Cloud Elasticsearch node you want to purchase, learn about what type of data is saved in the disk. Determine the disk size based on your business requirements.

We recommend that you preserve disk space for storing the system logs when selecting an Elasticsearch instance disk. System logs include the running log and monitoring log.

## Data types {#section_tsm_mmk_zgb .section}

**Note:** Before you purchase an Elasticsearch instance disk, make sure that the disk has enough space to store Elasticsearch cluster logs and X-Pack monitoring indexes.

-   User data that has been pushed to Elasticsearch.
-   Elasticsearch replicas. The number of replicas is user-configurable. However, Elasticsearch will retain a minimum of one replica.
-   Elasticsearch cluster logs. The amount of storage space that Elasticsearch consumes increases with the number of queries and pushes that Elasticsearch has received. By default, Elasticsearch only keeps cluster logs for seven days after creation. Elasticsearch cluster logging is not currently available.
    -   Running log
    -   Access log
    -   Slow query log
-   X-Pack monitoring indexes for troubleshooting exceptions. X-Pack is an Elasticsearch component. Monitoring indexes include the following:
    -   .monitoring-es-6-2018.01.08. The indexes consume a large amount of storage space. Elasticsearch creates only one index each day and keeps indexes for seven days after creation.
    -   .monitoring-kibana-6-2018.01.08. The amount of storage space that the indexes consume increases with the number of the indexes. Elasticsearch creates only one index each day and keeps indexes for seven days after creation.
    -   .watcher-history-3-2018.01.08. The indexes consume only a small amount of storage space. Elasticsearch creates only one index each day and keeps all indexes that have been created. You must manually delete the indexes that you no longer need.

**Note:** When an Elasticsearch instance has a high disk usage, such as a disk usage higher than 80%, the Elasticsearch cluster health status changes to yellow or red, and the Elasticsearch instance cannot be restarted. To restart an Elasticsearch instance, make sure that the health status of the instance is green. For more information, see [Instance management](../../../../intl.en-US/User Guide/Instance management/Instance management.md).

