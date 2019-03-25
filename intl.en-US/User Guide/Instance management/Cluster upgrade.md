# Cluster upgrade {#concept_kks_qzl_zgb .concept}

Cluster upgrade includes upgrading of the **instance specifications**, **number of nodes**, **dedicated master node specifications**, **number of client nodes**, **client node specifications**, **number of warm nodes**, **warm node specifications**, **warm node storage space**, and **storage space per node**.

**Note:** You may not be able to upgrade some of the cluster properties due to certain restrictions. For more information, see the following sections.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134290/155350340240023_en-US.png)

## Current configuration {#section_mkc_mgm_zgb .section}

Click **Cluster Extension** to show the configuration of the current Elasticsearch instance.

## Change configuration {#section_h5j_mgm_zgb .section}

You can follow the tips on the Configuration Upgrade page to upgrade the configuration of a cluster based on your business needs. For more information about the parameters, see [Buy page parameters](../../../../../intl.en-US/Quick Start/Buy page parameters.md).

**Note:** 

-   For each upgrade, you can change only one of the cluster properties mentioned at the beginning of this topic.
-   You cannot change the storage type on the Configuration Upgrade page. You can only change the storage space.
-   The cluster upgrade operation restarts the corresponding Alibaba Cloud Elasticsearch instance.
-   Cluster downgrade, such as downgrading of the node count, storage space, and node specifications, is currently not supported.

**Note:** 

-   If you have already purchased a dedicated master, changing the number of nodes does not restart the corresponding Alibaba Cloud Elasticsearch instance.
-   To upgrade an Elasticsearch instance when the health status of the instance is not green, you must select **Force Update**. However, this may affect your business running on the Elasticsearch instance.
-   If your business requires a cluster upgrade, we recommend that you make an upgrade assessment before upgrading the cluster.
-   You can view the total cost of your cluster upgrade order on the Configuration Upgrade page in real time when changing the number of nodes.
-   After you have submitted a cluster upgrade order, the upgrade Elasticsearch instance is billed based on the new configuration.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134290/155350340240025_en-US.png)

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134290/155350340240026_en-US.png)

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134290/155350340240027_en-US.png)

![]()

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134290/155350340240029_en-US.png)

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134290/155350340240031_en-US.png)

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134290/155350340240032_en-US.png)

**Instance types and specifications**

You can follow the tips on the Configuration Upgrade page to change the specification of an Elasticsearch instance. For more information, see [Buy page parameters](../../../../../intl.en-US/Quick Start/Buy page parameters.md).

**Note:** 

-   Data nodes that belong to the local disk specification family cannot be upgraded.
-   You cannot modify the specification families.

**Number of nodes**

You can follow the tips on the Configuration Upgrade page to change the number of data nodes. For more information, see [Buy page parameters](../../../../../intl.en-US/Quick Start/Buy page parameters.md).

**Dedicated master nodes**

You can select **Dedicated Master Node** on the Configuration Upgrade page to purchase dedicated master nodes or upgrade the specification of your purchased dedicated master nodes. The upgraded dedicated master nodes will be billed based on the new specification. For more information, see [Buy page parameters](../../../../../intl.en-US/Quick Start/Buy page parameters.md).

**Note:** 

-   If you have already purchased dedicated master nodes of 1-Core 2 GB, you can select Dedicated Master Node on the Configuration Upgrade page to repurchase dedicated master nodes with a higher specification. The dedicated master nodes will be billed based on the new specification. If you are using free dedicated master nodes, they will be billed after you upgrade their configuration.
-   You can select **Dedicated Master Node** on the Configuration Upgrade page to upgrade the dedicated master node specification. The upgraded dedicated master nodes will be billed based on the new specification.
-   You can select Dedicated Master Node on the Configuration Upgrade page to purchase dedicated master nodes or upgrade the specification of your purchased dedicated master nodes. By default, three dedicated master nodes of 2-Core 8 GB are used. The storage type of the dedicated master nodes is cloud disk. Each dedicated master node is assigned 20 GB of storage space.

**Client nodes**

You can select **Client Node** on the Configuration Upgrade page to purchase client nodes or upgrade the specification of your purchased client nodes. The upgraded client nodes will be billed based on the new specification. For more information, see [Buy page parameters](../../../../../intl.en-US/Quick Start/Buy page parameters.md).

**Note:** Select Client Node on the Configuration Upgrade page to purchase client nodes or upgrade the specification of your purchased client nodes. By default, two client nodes of 2-Core 8 GB are used. The storage type of the client nodes is cloud disk. Each client node is assigned 20 GB of storage space.

**Warm nodes**

You can select **Warm Node** on the Configuration Upgrade page to purchase warm nodes or upgrade the specification of your purchased warm nodes. The upgraded warm nodes will be billed based on the new specification. For more information, see [Buy page parameters](../../../../../intl.en-US/Quick Start/Buy page parameters.md).

**Note:** Select Warm Node on the Configuration Upgrade page to purchase client nodes or upgrade the specification of your purchased client nodes. By default, two warm nodes of 2-Core 8 GB are used. The storage type of the warm nodes is cloud disk. Each warm node is assigned 500 GB of storage space.

## Restart {#section_scp_mgm_zgb .section}

If the health status of your Elasticsearch instance is **green**, the Elasticsearch instance can provide services continuously during the upgrade restart process in most cases. You must make sure that your Elasticsearch instance has a minimum of one replica. The restart process may be time-consuming. **Exceptions may occur during the restart process and the health status of your Elasticsearch instance may temporarily change to red**.

**Note:** 

-   The nodes of an Elasticsearch instance may have a CPU and memory usage spike during the restart process. Your queries or pushing services may become unstable or fail. Typically, these services will recover after a short period of time. **Exceptions may occur during the restart process and the health status of your Elasticsearch instance may temporarily change to red**.
-   You must make sure that the health status of your Elasticsearch instance is **green**.

## Force update {#section_h1w_mgm_zgb .section}

If the health status of your Elasticsearch instance is **red** or **yellow**, this indicates that your services running on the instance have been severely affected. To resolve this issue, you must immediately upgrade your instance. You can select **Force Update** to ignore the **status of the Elasticsearch instance and forcibly upgrade the instance**. The upgrade process takes only a short period of time.

**Note:** 

-   The **Force Update** operation will **restart** the Alibaba Cloud Elasticsearch instance.
-   If you do not select **Force Update**, Elasticsearch uses the **restart** method to upgrade the instance.
-   If the health status of your Elasticsearch instance is **red** or **yellow**, the **Force Update** option is automatically selected. You cannot use the **restart** method to upgrade the instance.
-   The force update operation will make your services running on the Elasticsearch instance become unstable during the restart process.

**Storage**

You can follow the tips on the Configuration Upgrade page to change the storage space of a node. For more information, see [Buy page parameters](../../../../../intl.en-US/Quick Start/Buy page parameters.md).

**Note:** You cannot change the storage space for a data node that is configured with an ultra disk larger than 2,048 GB.

