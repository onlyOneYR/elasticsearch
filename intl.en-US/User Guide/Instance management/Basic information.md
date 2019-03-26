# Basic information {#concept_dsg_qzl_zgb .concept}

## Prepayment \(Subscription\) {#section_umm_ndm_zgb .section}

For basic information and parameters when using the **Subscription billing method** to purchase Alibaba Cloud Elasticsearch instances, see [Buy page parameters](../../../../../intl.en-US/Quick Start/Buy page parameters.md).

## Renewal {#section_nqv_ndm_zgb .section}

Drag the slider to the right to select the renewal duration. By default, the renewal duration is 1 month. Optional durations are 1 to 9 months or 1 to 3 years.

**Note:** 

-   Renewing an instance for 1 to 3 years will result in a discounted price.
-   The shortest renewal duration you can choose is 1 month.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134289/155357003240005_en-US.jpg)

## Post-payment \(Pay-As-You-Go\) {#section_gcg_4dm_zgb .section}

For basic information and parameters when using the **Pay-As-You-Go** billing method to purchase Alibaba Cloud Elasticsearch instances, see [Buy page parameters](../../../../../intl.en-US/Quick Start/Buy page parameters.md).

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134289/155357003240006_en-US.png)

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134289/155357003240007_en-US.png)

## Switch from Pay-As-You-Go to Subscription {#section_c14_4dm_zgb .section}

ES **Pay-As-You-Go** instances can be converted to **Subscription** instances. Click the **Subscription Billing** button. The process of changing billing methods is similar to that of renewal.

## Cluster expansion {#section_x35_4dm_zgb .section}

You can scale out by **instance specification**, **node number**, **dedicated master node specification**, or **node storage space**. For more information, see [Cluster upgrade](intl.en-US/User Guide/Instance management/Cluster upgrade.md).

**Name**

By default, the name of an Elasticsearch instance is the same as its ID. Elasticsearch allows you to customize instance names and search for instances by name in the console.

## Dedicated master node {#section_ftr_pdm_zgb .section}

You can select **Dedicated Master Node** on the Alibaba Cloud Elasticsearch purchase page and then purchase dedicated master nodes for your Elasticsearch instance. You can also purchase or scale out **dedicated master nodes** on the cluster expansion page. We recommend that you purchase dedicated master nodes to improve the stability of your service. For more information, see [Buy page parameters](../../../../../intl.en-US/Quick Start/Buy page parameters.md).

## Private address {#section_hry_pdm_zgb .section}

You can use a **private address** to access an Elasticsearch instance from an ECS instance in a VPC-Connected network.

**Private ports**

Elasticsearch allows you to specify the following ports as private ports:

-   Port `9200` \(for HTTP\).
-   Port `9300` \(for TCP\), which is used to support X-Pack for ES 5.5.3.

**Note:** The ES 6.3.2 with X-Pack version does not support the specification of port `9300`.

**Note:** Information security is not guaranteed when you access an Elasticsearch instance from the Internet. For secure access to Elasticsearch, we recommend that you purchase an ECS instance that meets the requirements of Alibaba Cloud Elasticsearch and use a **private address** to access Elasticsearch through a VPC.

## Public address {#section_sy4_5dm_zgb .section}

You can use a **public address** to access an Elasticsearch instance from the Internet.

**Public ports**

Elasticsearch allows you to specify the following ports as public ports:

-   Port `9200` \(for HTTP\).
-   Port `9300` \(for TCP\), which mainly supports X-Pack for ES 5.5.3.

**Note:** 

-   The ES 6.3.2 with X-Pack version does not support the specification of port `9300`.
-   To access an Elasticsearch instance from the Internet, you must first create a [Public IP address whitelist](intl.en-US/User Guide/Instance management/Security settings.md#section_ux5_yct_zgb). By default, no public addresses are allowed to access Elasticsearch.

## Other parameters {#section_kt5_5dm_zgb .section}

For information about parameters not described in this document, see the parameter descriptions on the basic information page.

