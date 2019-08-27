# Source instance overview {#concept_rww_bnh_hhb .concept}

ElasticFlow imports data from source instances, processes the data, and sends the data to downstream nodes. The source instance and data processing task configurations can be configured separately. You can first configure a source instance, and then select the source instance when configuring a task.

## Source instance types {#section_tsr_bph_hhb .section}

ElasticFlow supports the following Alibaba Cloud source instances. Some source instances require authorization. You can follow the instructions in the console to complete the authorization when creating a source instance.

-   **ApsaraDB RDS for MySQL**

    To create an ApsaraDB RDS for MySQL source instance, add the prepared Alibaba Cloud RDS instance ID, database name, username, and password to ElasticFlow. The account must have the read permission. You can then select a table from the source instance when creating a data import task. After the task is started, ElasticFlow will import data from the table on the source instance.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152525/156291432043268_en-US.png)

-   **MaxCompute**

    To create a MaxCompute source instance, add the prepared Alibaba Cloud MaxCompute project name, AccessKey ID, and AccessKey secret to ElasticFlow. The account must have the read permission. You can then select a table from the source instance when creating a data import task. After the task is started, ElasticFlow will import data from the table on the source instance.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152525/156291432043262_en-US.png)

-   **Log Service**

    To create a Log Service source instance, add the prepared Log Service project name to ElasticFlow. You can then select a table from the source instance when creating a data import task. After the task is started, ElasticFlow will import data from the table on the source instance.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152525/156291432043263_en-US.png)

-   **Elasticsearch**

    To create an Elasticsearch source instance, add the prepared Alibaba Cloud Elasticsearch instance ID, AccessKey ID, and AccessKey Secret to ElasticFlow. The account must have the read permission. You can then select a table from the source instance when creating a data import task. After the task is started, ElasticFlow will import data from the table on the source instance.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152525/156291432043264_en-US.png)


## Source instance management {#section_g4g_fqh_hhb .section}

You can create, search, edit, and delete source instances on the source instances page. For more information about creating source instances, see [Create a source instance based on service authorization](reseller.en-US/User Guide/ElasticFlow/Quick start/Create a source instance based on service authorization.md).

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152525/156291432042271_en-US.png)

