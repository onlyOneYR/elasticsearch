# Cluster upgrade {#concept_kks_qzl_zgb .concept}

This topic describes the procedure, guidelines, and restrictions of upgrading an Alibaba Cloud Elasticsearch instance.

Alibaba Cloud Elasticsearch allows you to upgrade the **instance specification**, **number of nodes**, **dedicated master node specification**, **number of client nodes**, **client node specification**, **number of warm nodes**, **warm node specification**, **warm node storage space**, and **storage space per data node** of an Elasticsearch instance.

**Note:** You may not be able to upgrade some of the cluster properties due to certain restrictions. For more information, see [Configuration upgrade](#section_h5j_mgm_zgb).

Log on to the [Alibaba Cloud Elasticsearch console](https://elasticsearch.console.aliyun.com/), select **Instance ID** \> **Basic Information**, and then click Upgrade to navigate to the Update page.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134290/156395376640023_en-US.png)

The Update page includes the **Current Config** and **Configuration Upgrade** information. For more information, see [Current configuration](#section_mkc_mgm_zgb) and [Configuration upgrade](#section_h5j_mgm_zgb).

## Current configuration {#section_mkc_mgm_zgb .section}

The **Current Config** section shows the configuration of the current Alibaba Cloud Elasticsearch instance. You can reference the information when you upgrade the instance.

## Precautions {#section_ksh_d6x_563 .section}

Before you upgrade an Elasticsearch instance, pay close attention to the following precautions:

-   If you need to upgrade the instance due to business requirements, make an assessment before you upgrade the cluster.
-   For each upgrade operation, you can only change one of the [upgradable cluster properties](#).
-   Typically, Elasticsearch needs to restart your Elasticsearch instance for the upgrade to take effect. For an Elasticsearch instance with dedicated master nodes, if you change the **number of nodes**, the instance will not be restarted.
-   If the status of your Elasticsearch instance is unhealthy \(showing a yellow or red flag\), then you must select **Force Update** to upgrade the instance. Force update may affect your businesses.
-   You cannot change the disk type of nodes by upgrading the instance. You can only change the storage space per node.
-   Alibaba Cloud Elasticsearch allows you to upgrade the specification of the Kibana node. Fees are charged for upgrading the Kibana node.
-   Alibaba Cloud Elasticsearch subscription instances currently do not support downgrading. For example, you cannot remove nodes from clusters, scale in the disk space, or downgrade the node specifications.
-   You can downgrade Alibaba Cloud Elasticsearch pay-as-you-go instances by scaling in the number of data nodes. The number of data nodes that you can scale in is restricted. Currently, you cannot perform other downgrade operations. For example, you cannot scale in the disk space or downgrade the node specification.
-   After you change the configuration of the instance, you can check the amount of your order on the Update page.
-   After you submit the order, your Elasticsearch instance will be billed based on the new configuration.

## Configuration upgrade {#section_h5j_mgm_zgb .section}

**Note:** Before you upgrade the configuration of an Elasticsearch instance, make sure that you have read the precautions in [Precautions](#section_ksh_d6x_563).

You can follow the instructions on the configuration upgrade page to change the configuration of the instance to meet your business requirements. For more information about the parameters, see [Buy page parameters](../../../../reseller.en-US/Quick Start/Buy page parameters.md#).

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134290/156395376740025_en-US.png)

Some of the parameters are described as follows:

-   **Specification family** and **instance type** 

    The **Specification Family** cannot be changed. If the **Specification Family** is set to a local disk type, then the **Instance Type** cannot be changed.

-   **Dedicated master nodes** 

    On the Update page, click **Yes** on the right side of **Dedicated Master Node** to purchase dedicated master nodes. You can upgrade the specification of the purchased dedicated master nodes. By default, three dedicated master nodes are purchased. Each dedicated master node has 2 cores, 8 GB of memory, and a cloud disk of 20 GiB. After you upgrade the dedicated master nodes, the Elasticsearch instance will be billed based on the new configuration.

    **Note:** If you have purchased **1-core 2 GB** dedicated master nodes, then you can repurchase dedicated master nodes of higher specifications on the Update page. The Elasticsearch instance will be billed based on the new configuration. If your dedicated master nodes are free nodes provided by Elasticsearch, then after you upgrade these nodes, we will start charging these nodes.

-   **Client nodes** 

    On the Update page, click **Yes** on the right side of **Client Node** to purchase client nodes. You can upgrade the specification of the purchased client nodes. By default, two client nodes are purchased. Each client node has 2 cores, 8 GB of memory, and a cloud disk of 20 GiB. After you upgrade the client nodes, the Elasticsearch instance will be billed based on the new configuration.

-   **Warm nodes** 

    On the Update page, click **Yes** on the right side of **Warm Node** to purchase warm nodes. You can upgrade the specification of the purchased warm nodes. By default, two warm nodes are purchased. Each warm node has 2 cores, 8 GB of memory, and a cloud disk of 500 GiB. After you upgrade the warm nodes, the Elasticsearch instance will be billed based on the new configuration.

-   **Kibana node** 

    On the Update page, click **Yes** on the right side of **Kibana Node** to purchase a Kibana node. You can upgrade the specification of the purchased Kibana node. By default, the Kibana node has two cores and 4 GB of memory.

    **Note:** After you purchase an Alibaba Cloud Elasticsearch instance, Elasticsearch provides you a free Kibana node with 1 core and 2 GB of memory. After you upgrade the Kibana node, the Elasticsearch instance will be billed based on the new configuration.

-   **Force update** 

    If the status of your Elasticsearch instance is unhealthy \(showing a **red** or **yellow** flag\), then your businesses have been severely affected. You must upgrade the instance immediately. You can select **Force Update** to ignore the status of the Elasticsearch instance and **forcibly upgrade** the instance. The upgrade process only takes a short period of time.

    **Note:** 

    -   The Elasticsearch instance needs to **restart** to complete the **force update** process.
    -   During the **force update** process, the services running on the Elasticsearch instance may become unstable.
    -   If you do not select **Force Update**, the **restart** method is used to upgrade the instance by default. For more information, see [Restart instances](reseller.en-US/User Guide/Instance management/Instance management.md#section_p5n_ccm_zgb).
    -   If the status of your Alibaba Cloud Elasticsearch instance is not healthy \(a **red** or **yellow** flag\), then the system will automatically select **Force Update** for you. Elasticsearch will not use the **restart** method to upgrade the instance.
-   **Node storage** 

    The storage space of nodes is measured in GiB. A standard SSD disk can provide up to 2,048 GiB \(2 TiB\) of storage space.

    You can scale out an ultra disk to up 2 TiB. When you purchase an ultra disk, you can set the storage space to up to 5,120 GiB \(5 TiB\). Ultra disks larger than 2,048 GiB include 2,560 GiB, 3,072 GiB, 3,584 GiB, 4,096 GiB, 4,608 GiB, and 5,120 GiB.


