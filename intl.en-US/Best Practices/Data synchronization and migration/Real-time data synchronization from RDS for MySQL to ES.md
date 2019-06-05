# Real-time data synchronization from RDS for MySQL to ES {#concept_a5h_jsr_zgb .concept}

This section explains how to use [Data Transmission Service \(DTS\)](https://www.alibabacloud.com/product/data-transmission-service) to quickly create a real-time data synchronization task from an RDS for MySQL instance to an Alibaba Cloud Elasticsearch \(ES\) instance. DTS uses this synchronization feature to synchronize RDS for MySQL data to ES instances and query data in real time.

## Real-time synchronization type {#section_gcv_lks_zgb .section}

DTS instances under the same Alibaba Cloud account from RDS for MySQL to ES.

## SQL operation types {#section_kn1_nks_zgb .section}

The main SQL operation types supported are as follows:

-   Insert
-   Delete
-   Update

**Note:** DTS does not support using DDL statements to synchronize data. DDL operations are ignored when data is synchronized.

If a table using DDL is encountered in an RDS for MySQL instance, the DML operations for the corresponding table may fail. To resolve this problem, complete the following steps:

1.  Delete the object from the synchronization list. For more information, see [Delete synchronization objects](https://partners-intl.aliyun.com/help/doc-detail/26635.htm).
2.  Delete the index corresponding to this table in the ES instance.
3.  Re-add the table to the synchronization list and re-initialize it. For more information, see [Add a synchronization object](https://partners-intl.aliyun.com/help/doc-detail/26634.htm).

If the DDL is used to add a column or modify a table, the order of DDL operations is as follows:

1.  Manually modify the corresponding mapping and new column in your ES instance.
2.  Modify the table schema and add a new schema in the source RDS for MySQL instance.
3.  Stop synchronizing instances in DTS, and restart DTS synchronization instances to reload the mapping relationship that was modified in ES.

## Configure data synchronization {#section_df1_qks_zgb .section}

To synchronize data from an RDS for MySQL instance to an ES instance, complete these steps:

1.  Purchase a DTS synchronization instance

    Log on to the [Data Transmission Service console](https://dts.console.aliyun.com/) and go to the **Data Synchronization** pane. In the upper-right corner, click **Create Synchronization Task** to purchase a synchronization instance. You can then configure the synchronization instance.

    **Note:** You must purchase a synchronization instance before you can configure it. Two billing modes are supported: **Subscription** and **Pay-As-You-Go**.

     **Purchase page parameters** 

    -   Function

        Select Data Synchronization.

    -   Source Instance

        Select MySQL.

    -   Source Region
        -   Because this example uses the RDS for MySQL instance, you need to select the region where the RDS for MySQL instance is located.
    -   Target Instance

        Select Elasticsearch.

    -   Target Region

        Select the region where your Elasticsearch instance is located. Note that after the synchronization instance has been purchased, you cannot change its region.Target Instance

    -   Specification

        Each instance specification corresponds to the performance of a synchronization instance. For more information, see [Data Synchronization Specification](https://partners-intl.aliyun.com/help/doc-detail/26605.htm).

    -   Order Time
        -   If the synchronization instance is prepaid, the order time is one month by default.
    -   Quantity

        By default, the quantity is 1.

        **Note:** The region of your DTS synchronization instance is the target region that you selected. For example, if the synchronization instance is from the Hangzhou-region RDS for MySQL to the Hangzhou-region Elasticsearch, the region of the DTS synchronization instance is Hangzhou. To configure your synchronization instance, go to the instance list in that region in DTS, search for the synchronization instance you just purchased, and click **Configure Synchronization Instance** in the upper-right corner.

2.  Configure your synchronization instance

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134314/155969981240099_en-US.png)

    **Synchronization task name**

    There are no requirements for the name of a synchronization instance.

     **Source instance** 

    This example uses RDS for MySQL as the data source. You need to set the instance type, region and ID, and database account and password.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134314/155969981240100_en-US.png)

     **Target instance** 

    You need to configure the ID, account, and password for the ES instance.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134314/155969981240101_en-US.png)

    Once you complete these configurations, click **Authorize Whitelist and Enter Next Step** to add IPs to RDS for MySQL and ES instance whitelists.

3.  Authorize instance whitelists

    **Note:** If the source instance is RDS for MySQL, DTS automatically adds IPs to a whitelist or adds a security group.

    If the source instance is RDS for MySQL, DTS adds the instance IP to the security group of an RDS instance’s whitelist. This means that, when creating synchronization tasks, you can avoid failures caused by a disconnection between the DTS instance and the RDS database. To ensure the stability of the synchronization task, do not delete the instance IP from the RDS instance.

    After the whitelist is authorized, click **Next** to create a synchronization account.

4.  Select the synchronization object

    To configure synchronization objects and naming rules for indexes, complete these steps:

    1.  Select a naming rule for indexes: table name or database name\_table name.

        -   If you select a table name, the name of the index is the name of the table.
        -   If you select a database name\_table name, the naming rule for the index is database name\_table name. For example, if a database is named dbtest and a table is named sbtest1, after the table is synchronized to your ES instance, the index name would be dbtest\_sbtest1.
        -   If two tables in different databases have the same name, we recommend that the index name be set to database name\_table name.
    2.  Select a specific database, table, and column. The selectable granularity of the synchronization objects supports table-level operations. This means that you can synchronize several databases and tables.

        The selectable granularity of the synchronization objects supports table-level operations. This means that you can synchronize several databases and tables.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134314/155969981240102_en-US.png)

    3.  By default, the docid of all tables is the primary key. If some tables do not have the primary key, configure their docid corresponding to the columns in the source tables. In the box of selected objects on the right, move the pointer over the corresponding table, and click **Edit** to enter the advanced settings pane.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134314/155969981240103_en-US.png)

    4.  In advanced settings, you can configure the index name, type name, partition column and quantity, and \_id value column. If the value of \_id is set to the business primary key, you need to select the corresponding business primary key column.

    5.  After synchronization objects are configured, proceed to the advanced setup.

5.  Advanced setup

    **Main configurations** 

    1.  **Synchronization Initialization**: We recommend that you select **Structure Initialization** and **Data Initialization**, which allows DTS to automatically create indexes and initialize data. If you do not select Schema Initialization, you need to define the mapping for indexes in ES manually before synchronizing. If you do not select Full Data Initialization, the starting time for incremental DTS data synchronization is the time at which synchronization starts.

    2.  **Shard Configuration**: There are 5 partitions and 1 replica by default. Once the configuration is adjusted, all indexes define partitions according to this configuration.

    3.  **String Index** is an analyzer that can select strings. By default, it is **Standard Analyzer**. Other values include: **Simple Analyzer**, **Whitespace Analyzer**, **Stop Analyzer**, **Keyword Analyzer**, **English Analyzer**, and **Fingerprint Analyzer**. The string fields of all indexes define Analyzer according to this configuration.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134314/155969981240104_en-US.png)

    4.  **Time Zone** is where time fields synchronized to your ES instance are stored. The default time zone in China is UTC \(UTC +8\).

6.  Pre-check

    After synchronization task configurations are complete, DTS performs a pre-check. If the pre-check is verified, click **Start** to start the synchronization task.

    After the synchronization task starts, go to the synchronization job list and verify whether the task’s status is **Sync initialization**. The time it takes to initialize depends on the amount of data that the synchronization object has in the source instance. After completing the initialization, the synchronization instance’s status is **Synchronizing**. The synchronization link between the source and target instances is established.

7.  Validate data

    After completing all of the preceding steps, log on to the ES console to check the corresponding indexes created in your ES instances and the synchronized data.


