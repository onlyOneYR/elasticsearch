# Basic information {#concept_dsg_qzl_zgb .concept}

## Elasticsearch subscription instances {#section_umm_ndm_zgb .section}

The following figure shows the information of an Alibaba Cloud Elasticsearch instance that uses the subscription billing method. For parameter descriptions, see the following sections and [Buy page](../../../../reseller.en-US/Quick Start/Buy page.md).

-   **Name**: By default, the name of an Alibaba Cloud Elasticsearch instance is the same as its ID. You can edit the name of the instance. You can also search instances by name.
-   **Internal Network Address**: You can use the IP address of a VPC-connected ECS instance to access an Alibaba Cloud Elasticsearch instance.

    **Note:** If you access an Alibaba Cloud Elasticsearch instance through the Internet, data security is not guaranteed. To protect your data, we recommend that you purchase an ECS instance that is connected to the same VPC network as your Elasticsearch instance. You can then use an **internal network address** to access the Elasticsearch instance.

-   **Internal Network Port**: The following ports are supported:
    -   Port `9200` for HTTP and HTTPS.
    -   Port `9300` for TCP. Only Alibaba Cloud Elasticsearch 5.5.3 with Commercial Feature supports this port.

        **Note:** You cannot use the transport client to access Alibaba Cloud Elasticsearch 6.3.2 with Commercial Feature and Alibaba Cloud Elasticsearch 6.7.0 with Commercial Feature through port `9300`.

-   **Public Network Access**: You can use **public network addresses** to access Alibaba Cloud Elasticsearch instances.
-   **Public Network Port**: The following ports are supported:
    -   Port `9200` for HTTP and HTTPS.
    -   Port `9300` for TCP. Only Alibaba Cloud Elasticsearch 5.5.3 with Commercial Feature supports this port.

        **Note:** 

        -   You cannot use the transport client to access Alibaba Cloud Elasticsearch 6.3.2 with Commercial Feature and Alibaba Cloud Elasticsearch 6.7.0 with Commercial Feature through port `9300`.
        -   To access an Elasticsearch instance through the Internet, you must configure the [Public IP address whitelist](reseller.en-US/User Guide/Instance management/Security settings.md#section_ux5_yct_zgb). By default, the public network access feature forbids all IP addresses.
-   **Protocol**: By default, HTTP is selected. You can click **Edit** to change the protocol. Currently, you can choose HTTP or HTTPS. For more information, see [EN-US\_TP\_134295.md\#section\_i7x\_sqt\_enx](reseller.en-US/User Guide/Instance management/Security settings.md#section_i7x_sqt_enx).
-   **Renew**: You can click **Renew** on the right side of **Basic Information** to renew the instance. You can renew your subscription one or more months. The minimum renewal period is one month.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134289/156231455340005_en-US.jpg)


## Elasticsearch pay-as-you-go instances {#section_gcg_4dm_zgb .section}

The following figure shows the basic information of an Alibaba Cloud Elasticsearch instance that uses the pay-as-you-go billing method. For parameter descriptions, see [Elasticsearch subscription instances](#section_umm_ndm_zgb) and [Buy page](../../../../reseller.en-US/Quick Start/Buy page.md).

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134289/156231455340006_en-US.png)

You can switch an Alibaba Cloud Elasticsearch instance from **pay-as-you-go** to **subscription**. To perform this task, click **Switch to Subscription** on the right side of **Basic Information**, and follow the instructions to switch the billing method.

## Configuration information {#section_05p_e2j_1m2 .section}

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134289/156231455350251_en-US.png)

For more information about parameter descriptions, see [Buy page](../../../../reseller.en-US/Quick Start/Buy page.md#).

## Node visualization {#section_19y_11v_uoo .section}

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134289/156231455350256_en-US.png)

## Remove data nodes {#section_h7r_8p7_gdy .section}

Currently, you can downgrade data nodes for Elasticsearch pay-as-you-go instances and Elasticsearch instances deployed in one zone. Elasticsearch subscription instances and instances deployed across zones are not supported. This function only allows you to remove data nodes from an Alibaba Cloud Elasticsearch instance. You cannot downgrade the specification or disk space of dedicated master nodes, client nodes, and Kibaba nodes. For more information, see [Downgrade data nodes](reseller.en-US/User Guide/Instance management/Downgrade data nodes.md#).

## Upgrade {#section_x35_4dm_zgb .section}

You can upgrade the **instance specification**, **number of nodes**, **dedicated master node specification**, and **storage space per data node** for an Elasticsearch instance. For more information, see [Cluster upgrade](reseller.en-US/User Guide/Instance management/Cluster upgrade.md).

