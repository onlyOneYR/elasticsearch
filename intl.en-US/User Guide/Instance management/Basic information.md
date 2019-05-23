# Basic information {#concept_dsg_qzl_zgb .concept}

## Subscription {#section_umm_ndm_zgb .section}

Basic information of **Subscription-based** Alibaba Cloud Elasticsearch instances. For more information about the parameters, see [Buy page](../../../../reseller.en-US/Quick Start/Buy page.md).

## Renew {#section_nqv_ndm_zgb .section}

This function allows you to renew your Alibaba Cloud Elasticsearch instance. You can move the slider to expand the renewal cycle. By default, the renewal cycle is set to one month. You can select a renewal cycle from one to nine months or from one to three years.

**Note:** 

-   Discounts are offered for a one-year, two-year, or three-year renewal.
-   The minimum renewal cycle is one month.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134289/155859845440005_en-US.jpg)

## Pay-As-You-Go {#section_gcg_4dm_zgb .section}

Basic information of **Pay-As-You-Go** Alibaba Cloud Elasticsearch instances. For more information about the parameters, see [Buy page](../../../../reseller.en-US/Quick Start/Buy page.md).

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134289/155859845440006_en-US.png)

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134289/155859845440007_en-US.png)

## Switch to Subscription {#section_c14_4dm_zgb .section}

You can switch the billing method of Alibaba Cloud Elasticsearch instances from **Pay-As-You-Go** to **Subscription**. To perform this task, log on to the Elasticsearch console, click **Switch to Subscription**, and follow the instructions on the page to switch the billing method.

## Upgrade {#section_x35_4dm_zgb .section}

You can upgrade the **instance specification**, **number of nodes**, **dedicated master node specification**, and **storage space per data node** for an Elasticsearch instance. For more information, see [Cluster upgrade](reseller.en-US/User Guide/Instance management/Cluster upgrade.md).

 **Name** 

By default, the name of an Elasticsearch instance is the same as its ID. Elasticsearch allows you to customize instance names and search for instances by specifying their names in the console.

## Downgrade Data Nodes {#section_xhq_xqo_nzd .section}

This function only supports Alibaba Cloud Elasticsearch instances that use the Subscription billing method and are deployed in one zone. Instances that use the Pay-As-You-Go billing method or that are deployed across zones are not supported. You can only use this function to remove data nodes from an Alibaba Cloud Elasticsearch instance. You cannot downgrade the specification or disk space of dedicated master nodes, client nodes, and Kibaba nodes. For more information, see [Downgrade data nodes](reseller.en-US/User Guide/Instance management/Downgrade data nodes.md#).

## Dedicated Master Nodes {#section_ftr_pdm_zgb .section}

You can select **Dedicated Master Node** on the Alibaba Cloud Elasticsearch buy page and then purchase dedicated master nodes for your Elasticsearch instance. You can also select **Dedicated Master Node** on the Configuration Upgrade page to purchase or upgrade dedicated master nodes. We recommend that you purchase dedicated master nodes to improve the stability of your service. For more information, see [Buy page](../../../../reseller.en-US/Quick Start/Buy page.md).

## Internal Network Address {#section_hry_pdm_zgb .section}

In a VPC network, you can use an ECS instance to access the **internal network address** of an Elasticsearch instance.

 **Internal Network Port** 

You can access an Alibaba Cloud Elasticsearch instance through the following internal network ports:

-   Port `9200` for HTTP.
-   Port `9300` for TCP. This port is used to access Alibaba Cloud Elasticsearch 5.5.3 with Commercial Feature.

**Note:** You cannot use the transport client to access Alibaba Cloud Elasticsearch 6.3.2 with Commercial Feature and Alibaba Cloud Elasticsearch 6.7.0 with Commercial Feature through port `9300`.

**Note:** Information security is not guaranteed when you access Elasticsearch from the Internet. For data security, we recommend that you purchase an ECS instance that meets the requirements of Alibaba Cloud Elasticsearch. You can then use the ECS instance to access the **internal network address** of the Elasticsearch instance through a VPC network.

## Public Network Address {#section_sy4_5dm_zgb .section}

You can access the **public network address** of an Alibaba Cloud Elasticsearch instance from the Internet.

 **Public Network Port** 

You can access an Alibaba Cloud Elasticsearch instance through the following public network ports:

-   Port `9200` for HTTP.
-   Port `9300` for TCP. This port is used to access Alibaba Cloud Elasticsearch 5.5.3 with Commercial Feature.

**Note:** 

-   You cannot use the transport client to access Alibaba Cloud Elasticsearch 6.3.2 with Commercial Feature and Alibaba Cloud Elasticsearch 6.7.0 with Commercial Feature through port `9300`.
-   To configure the public network whitelist, see [Public IP address whitelist](reseller.en-US/User Guide/Instance management/Security settings.md#section_ux5_yct_zgb). By default, Elasticsearch forbids all public network addresses.

## Other parameters {#section_kt5_5dm_zgb .section}

For parameters that are not described in this topic, see the parameter descriptions on the basic information page in the Elasticsearch console.

