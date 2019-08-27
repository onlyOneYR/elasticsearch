# Use BSearch-QueryBuilder {#concept_261567 .concept}

BSearch-QueryBuilder is an advanced query plug-in, as well as a UI component. With the BSearch-QueryBuilder plug-in, you no longer need to write complex DSL statements for data query. It allows you to create complex queries in a visualized manner. This document describes how to use the BSearch-QueryBuilder plug-in to create a query.

## Features {#section_v2x_85q_3f3 .section}

BSearch-QueryBuilder has the following features:

-   Easy to learn: the BSearch-QueryBuilder plug-in is a UI component, allowing you to create Elasticsearch DSL queries in a visualized manner. You can customize search conditions without coding. This saves the costs of learning complex DSL statements. It also helps developers write and verify DSL statements.
-   Easy to use: all queries that you have defined are saved in Kibana, which are ready for use at anytime.
-   Compact: BSearch-QueryBuilder only consumes about 14 MB of disk space. BSearch-QueryBuilder does not stay resident in the memory. This means that it will not adversely affect the performance of Kibana and Elasticsearch.
-   Secure and reliable: BSearch-QueryBuilder does not rewrite, store, or forward any user data. The source code of BSearch-QueryBuilder has been verified by Alibaba Cloud security auditing.

## Background {#section_mr3_162_9uo .section}

QueryDSL is an open-source Java framework used to define SQL type-safe queries. It allows you to use API operations to send queries instead of writing statements. Currently, QueryDSL supports JPA, JDO, SQL, Java Collections, RDF, Lucene, and Hibernate Search.

Elasticsearch provides a complete JSON query DSL for you to define queries. QueryDSL provides various query expressions. Some queries can wrap other queries, such as the boolean queries. Some queries can wrap filters, such as the constant score queries. Some queries can wrap other queries and filters at the same time, such as the filtered queries. You can use any query expressions and filters supported by Elasticsearch to create complex queries and filter the returned result. DSL is only mastered by a few programmers. You may make mistakes when writing DSL statements. QueryBuilder can help users that do not have much knowledge in Elasticsearch DSL or those that want to create DSL queries efficiently.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231262749225_en-US.png)

## Preparations {#section_tl7_i7c_s9f .section}

To use the BSearch-QueryBuilder plug-in, you must first [purchase an Elasticsearch instance](../../../../reseller.en-US/Quick Start/Purchase and configuration.md#). The version of the instance must be 6.3 or 6.7. Version 5.5.3 is not supported.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231262749427_en-US.png)

**Note:** You can also use an existing instance. If the instance version does not meet the requirements, upgrade the instance.

## Install the BSearch-QueryBuilder plug-in {#section_09m_zuc_pm4 .section}

**Note:** Before you install the BSearch-QueryBuilder plug-in, make sure that the specification of your Kibana node is **2-core, 4 GB** or higher. Otherwise, [Cluster upgrade](reseller.en-US/User Guide/Instance management/Cluster upgrade.md#).

1.  Log on to the [Alibaba Cloud Elasticsearch console](https://elasticsearch.console.aliyun.com/).
2.  Click the name of the Elasticsearch instance, and then click **Data Visualization** in the left-side navigation pane.
3.  On the **Data Visualization** page, click **Edit Configuration** under **Kibana**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231262749321_en-US.png)

4.  On the **Kibana Configuration** page, click **Install** on the right side of **Bsearch\_querybuilder** in the **Plug-in Configuration** list.

    **Note:** After you confirm the install operation, the system will restart the Kibana node. Therefore, before you confirm the operation, you must make sure that the restart process does not affect your operations on the Kibana console.

5.  Confirm the operation and restart the Kibana node.

    After the Kibana node is restarted, the installation process is then completed. The plug-in will be in the **Installed** state.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231262749322_en-US.png)

    **Note:** The installation process may be time-consuming.


## Use the BSearch-QueryBuilder plug-in {#section_37c_alq_zuo .section}

1.  Go back to the **Data Visualization** page, click **Console** under **Kibana**.
2.  Enter the username and password, and then click **LOG IN** to log on to the Kibana console.

    The default username is elastic. Enter the password that you have set when purchasing the Elasticsearch instance.

3.  In the Kibana console, select **Discover** \> **Query**.

    **Note:** Before querying, make sure that you have created an index pattern. To create an index pattern, in the Kibana console, click **Management**, find the **Kibana**area, and click **Index Patterns** \> **Create index pattern**.

4.  In the query area, select a search condition and filter, and click **Submit**.

    After you submit the query, the system shows the query result.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231262749357_en-US.png)

    In the query area, click the ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231262849385_en-US.png) icon to add a search condition, click the ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231262849386_en-US.png) icon to add a filter for the condition, or click the ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231262849391_en-US.png) icon to delete a search condition or filter.

    For more information, see [Examples](#section_mc9_1mf_vau).


## Examples {#section_mc9_1mf_vau .section}

The BSearch-QueryBuilder plug-in allows you to create a variety of queries, such as regexp queries, boolean queries, and range queries.

-   Regexp queries

    As shown in the following figure, the **email** condition is added for fuzzy match. The **email** condition matches all email addresses that contain the **iga** keyword.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231262849284_en-US.png)

    The following figure shows the returned result:

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231262849285_en-US.png)

-   Boolean queries

    As shown in the following figure, the **index** condition is set to **tryme\_book**. An OR condition containing multiple filters is also added to filter data by **type**. These **type** filters are set to **Undergraduate teaching materials**, **Math**, **Foreign language teaching**, and **Undergraduate textbooks**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231262949286_en-US.png)

    The following figure shows the returned result.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231262949287_en-US.png)

-   Range queries

    Range queries allow you to search data by date. As shown in the following figure, the range condition is used to filter data based on the **utc\_time** field. Only data entries created `in the last 240 days` are returned.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231262949288_en-US.png)

    The following figure shows the returned result.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231262949289_en-US.png)


With all these search conditions and filters, you can define a complex query as follows:

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231263049233_en-US.png)

The actual DSL statement for the query is as follows:

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231263049234_en-US.png)

As shown in the preceding examples, BSearch-QueryBuilder significantly simplifies the complexity of Elasticsearch queries.

