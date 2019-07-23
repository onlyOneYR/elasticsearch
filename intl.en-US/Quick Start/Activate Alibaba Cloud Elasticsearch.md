# Activate Alibaba Cloud Elasticsearch {#concept_xd1_cmk_zgb .concept}

This topic describes how to activate Alibaba Cloud Elasticsearch.

## Prerequisites {#section_mo9_17s_2kb .section}

Before you purchase Alibaba Cloud Elasticsearch, make sure that you have completed the following tasks:

-   You have created an Alibaba Cloud account. To create an Alibaba Cloud account, click [Create an Alibaba Cloud account](https://account.aliyun.com/register/register.html).
-   You have created a VPC network and VSwitch. For more information, see [Create a VPC and a VSwitch](../../../../reseller.en-US/User Guide/VPC and subnets/Manage a VPC.md#section_ufw_rhv_rdb).

## Procedure {#section_nzc_6y3_pew .section}

1.  Log on to the [Alibaba Cloud International site](https://account.aliyun.com/).
2.  Select **Products** \> **Analytics & Big Data**, and then click **Elasticsearch**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134282/156384585950484_en-US.png)

3.  Click **Buy Now**, set the parameters on the buy page, and then click **Buy Now**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134282/156384586050486_en-US.png)

    For more information about the parameters, see [Buy page](reseller.en-US/Quick Start/Buy page.md#).

4.  On the Confirm Order page, confirm the instance information, select the check box to agree with the terms of service, and then click **Activate**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134282/156384586039933_en-US.png)

5.  After the service is activated, click **Console** to log on to the Alibaba Cloud Elasticsearch console.
6.  You can then view the purchased Elasticsearch instance in the Alibaba Cloud Elasticsearch console. Wait for the instance to be activated.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134282/156384586039935_en-US.png)

    When the **status** of the instance changes to **Active**, the instance is activated. You can then use the Elasticsearch instance to perform [Elasticsearch access testing](reseller.en-US/Quick Start/Elasticsearch access test.md#), [configure Elasticsearch features](reseller.en-US/Quick Start/Configure Elasticsearch features.md#), import data, and query data.

    **Note:** 

    Before you import data to the activated Elasticsearch instance, you must manually create indexes and mappings. For example, if you import data to the instance while the system is automatically creating mappings, the created mappings may not be able to meet your requirements and an error may occur.

    To avoid this issue, Elasticsearch disables the **auto indexing** feature by default. Therefore, you must create indexes and mappings before you import data to the instance.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134282/156384586039936_en-US.png)

    If you must use the auto indexing feature, go to the [Cluster Configuration](../../../../reseller.en-US/User Guide/Instance management/Elasticsearch cluster configuration.md#) page in the Elasticsearch console, enable the auto indexing feature under **YML Configuration**, and then restart the instance to apply the change.


