# Synchronize Hadoop and ES data with DataWorks {#concept_fzr_y2r_zgb .concept}

This topic describes how to use the data synchronization feature of DataWorks to migrate data from Hadoop to Alibaba Cloud Elasticsearch \(ES\), and analyze the data. You can also use Java codes to synchronize data. For more information, see [Data interconnection between ES-Hadoop and Elasticsearch](intl.en-US/Best Practices/Data interconnection between ES-Hadoop and Elasticsearch.md#) and [Use ES-Hadoop on E-MapReduce](../../../../../intl.en-US/Best Practices/Use ES-Hadoop on E-MapReduce.md#).

## Prerequisites {#section_r33_hfr_zgb .section}

1.  Create a Hadoop cluster

    You must create a Hadoop cluster to perform data migration. This topic uses the Alibaba Cloud E-MapReduce service \(EMR\) to create a Hadoop cluster. For more information, see [Create a cluster](../../../../../intl.en-US/Quick Start/Step 2 : Create a cluster.md#).

    Specifically, the following EMR Hadoop version information is used:

    -   EMR version: EMR-3.11.0
    -   Cluster type: HADOOP
    -   Services: HDFS2.7.2 / YARN2.7.2 / Hive2.3.3 / Ganglia3.7.2 / Spark2.3.1 / HUE4.1.0 / Zeppelin0.8.0 / Tez0.9.1 / Sqoop1.4.7 / Pig0.14.0 / ApacheDS2.0.0 / Knox0.13.0
    Additionally, this topic uses a VPC network for the Hadoop cluster, sets the region to China East 1 \(Hangzhou\), sets public and private IPs for the ECS master nodes, and selects non-high availability \(non-HA\) mode。

2.  Elasticsearch

    Log on to the [Elasticsearch console](https://elasticsearch-cn-hangzhou.console.aliyun.com/#/instances) and select the same region and VPC network as the EMR cluster. For information about purchasing an ES instance, see [Purchase and configuration](../../../../../intl.en-US/Quick Start/Purchase and configuration.md#).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155358967740067_en-US.png)

3.  DataWorks

    [Create Workspace](../../../../../intl.en-US/Preparation/Administrator Operations/Create Workspace.md#) and set the region to **China East 1** \(Hangzhou\). The following example uses the project bigdata\_DOC.


## Prepare data {#section_hcm_jfr_zgb .section}

To create test data in the Hadoop cluster, follow these steps:

1.  Log on to the [EMR](https://emr.console.aliyun.com/) console, go to Old EMR Scheduling, and in the left-side navigation pane, click **Notebook**.

2.  Click **File** \> **New notebook**. In this example, a notebook named **es\_test\_hive** is created. Set the default type to **Hive**. The attached cluster is the EMR Hadoop cluster created.

3.  Enter the syntax for creating a Hive table：

    ```
    CREATE TABLE IF NOT
     
    EXISTS hive_esdoc_good_sale(
     create_time timestamp,
     category STRING,
     brand STRING,
     buyer_id STRING,
     trans_num BIGINT,
     trans_amount DOUBLE,
     click_cnt BIGINT
     )
     PARTITIONED BY (pt string) ROW FORMAT
    DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    ```

4.  Click **Run**. If the message **Query executed successfully** displays, then the table **hive\_esdoc\_good\_sale** was created successfully in the EMR Hadoop cluster, as shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155358967740070_en-US.png)

5.  Insert test data. You can import data from OSS, or other data sources, or insert data manually. This example inserts data manually. The script for inserting data is as follows:

    ```
    insert into
    hive_esdoc_good_sale PARTITION(pt =1 ) values('2018-08-21','Jacket ','Brand A','lilei',3,500.6,7),('2018-08-22','Fresh food','Brand B','lilei',1,303,8),('2018-08-22','Jacket ','Brand C','hanmeimei',2,510,2),(2018-08-22,'Bathroom accessory','Brand A','hanmeimei',1,442.5,1),('2018-08-22','Fresh food','Brand D','hanmeimei',2,234,3),('2018-08-23','Jacket ','Brand B','jimmy',9,2000,7),('2018-08-23','Fresh food','Brand A','jimmy',5,45.1,5),('2018-08-23','Jacket ','Brand E','jimmy',5,100.2,4),('2018-08-24','Fresh food','Brand G','peiqi',10,5560,7),('2018-08-24','Bathroom accessory','BrandF','peiqi',1,445.6,2),('2018-08-24','Jacket ','Brand A','ray',3,777,3),('2018-08-24','Bathroom accessory','Brand G','ray',3,122,3),('2018-08-24','Jacket ','Brand C','ray',1,62,7) ;
    ```

6.  After data is inserted successfully, run the `select * from hive_esdoc_good_sale where pt =1;` statement, and then check that the data is already in the EMR Hadoop cluster table.


## Synchronize data {#section_kbc_yfr_zgb .section}

**Note:** Because the network environment of the DataWorks project is generally not connected to that of the Hadoop cluster core nodes, you can customize your resource groups to run the synchronization task of DataWorks on Hadoop cluster master nodes \(this is because Hadoop cluster master and core nodes are often interconnected.

1.  View core nodes of the EMR Hadoop cluster.

    1.  In the EMR console, at the top of the menu bar, click **Cluster Management**.

    2.  Locate your target cluster and click **Manage** at its right side.

    3.  In the left-side navigation pane, click **Hosts** to view thes master nodes and core nodes, as shown in the following figure.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155358967740072_en-US.png)

        **Note:** The master node name of a Non-HA EMR Hadoop cluster is generally **emr-header-1**, and the core node name is generally **emr-worker-X**.

    4.  Click the ECS ID of the master node in the preceding figure to go to its Instance Details page. Click **Connect** to connect to the ECS instance. You can also run the `hadoop dfsadmin -report` command to view core node information.

**Note:** The ECS master node logon password is the password you set when you created your EMR Hadoop cluster.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155358967740073_en-US.png)

         

2.  Create a custom resource group

    1.  In the DataWorks console, go to the Data Integration page, select **Resource Group** \> **Add resources Group**. For more information about custom resource group, see [Add task resources](../../../../../intl.en-US/User Guide/Data integration/Common configuration/Add task resources.md#).

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155358967740074_en-US.png)

    2.  Enter the name of the resource group and the server information. The server is the master node of your EMR cluster.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155358967740075_en-US.png)

        -   **Network type** is a **proprietary network \(VPC\)**.

**Note:** For a VPC network, you must enter the UUID of your ECS instance. For a Classic network, you must enter the instance name. Currently, only DataWorks 2.0 in the China East 2 \(Shanghai\) region supports adding a Classic network scheduling resource. For other regions, regardless of whether you are using a Classic network or VPC network, the network type must be selected as VPC network when you add a scheduling resource group.

        -   ECS UUID: Log on to the EMR cluster master node and run `dmidecode | grep UUID` to obtain the returned value.

        -   Machine IP: the public IP of the master node-Machine CPU: the CPU of the master node-Memory size: memory of the master node You can obtain the preceding information from the configuration information section by clicking the master node ID in the ECS console.

    3.  After completing the **Add server** step, you must ensure that the networks of master node and DataWorks are interconnected. If you are using an ECS server, you need to set a server security group. If you are using a private IP, see [Add security group](../../../../../intl.en-US/User Guide/Data integration/Common configuration/Add security group.md#). If you are using a public IP address, you can directly set the Internet ingress and egress under Security Group Rules. This example uses an EMR cluster in a VPC network that is in the same region as DataWorks, which means no security group needs to be set.

    4.  Install the agent as prompted. When the **available** status appears, it indicates that you successfully added a resource group.

**Note:** This example uses a VPC network, which means you do not need to open port 8000.

        If the status is unavailable, log on to the master node and run the `tail –f/home/admin/alisatasknode/logs/heartbeat.log` command to check whether the heartbeat message between DataWorks and the master node has timed out.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155358967740076_en-US.png)

3.  Create a data source

    1.  In the Data Integration page of DataWorks, click **Data Sources** \> **New source**, and select **HDFS**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155358967840077_en-US.png)

    2.  In the New HDFS Data Sources panel, set the **Name** and **defaultFS** parameters.

**Note:** For a EMR Hadoop cluster, if it is a non-HA cluster, the address is set to `hdfs://emr-header-1的IP:9000`. If it is an HA cluster, the address is set to `hdfs://emr-header-1的IP:8020`. In this example, emr-header-1 and DataWorks are connected through a VPC network, so an intranet IP is set, and the test connectivity is unavailable.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155358967840078_en-US.png)

4.  Configure a data synchronization task

    1.  In the left-side navigation pane of the Data Integration page, click **Sync Tasks**, select **New** \> **Script Mode**.

    2.  In the Import template panel, select the following data source type:

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155358967840079_en-US.png)

    3.  After the template is imported, the synchronization task is converted to the script mode. The following figure shows the configuration script used in this topic. For more information, see [Script mode configuration](../../../../../intl.en-US/User Guide/Data integration/Task configuration/Configure reader plug-in/Script mode configuration.md#). For information about Elasticsearch configuration rules, see [Configure Elasticsearch Writer](../../../../../intl.en-US/User Guide/Data integration/Task configuration/Configure writer plug-in/Configure Elasticsearch Writer.md#).

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155358967840080_en-US.png)

    -   The synchronization script configuration includes the following three parts: Reader, which is the configuration of the upstream data source \(that is, the target cloud product for data synchronization\); Writer, which is the configuration of your ES instance; and setting, which refers to synchronization configurations such as packet loss rate and maximum concurrency.
    -   The path parameter indicates the place where the data is stored in the Hadoop cluster. You can log on to the master node and run the `hdfs dfs –ls /user/hive/warehouse/hive_doc_good_sale` command to confirm the place. For a partition table, you do not need to specify the partitions. The data synchronization feature of DataWorks can automatically recurse to the partition path, as shown in the following figure.
    -   Because Elasticsearch does not support the timestamp type, the example used in this topic sets the type of the creat\_time field to string.
    -   endpoint is the intranet or Internet IP address of your Elasticsearch instance. If you are using an intranet address, you need to add the IP into the Elasticsearch whitelist in the Elasticsearch cluster configuration page. If you are using an Internet IP, you need to configure the Elasticsearch publick network access whitelist \(including the server IP addresss of DataWorks and the IP of the resource group you use\).
    -   accessId and accessKey in Elasticsearch Writer are your Elasticsearch access user name \(it is elastic by default\) and password, respectively.
    -   index is the index of your Elasticsearch instance through which you need to access Elasticsearch data.
    -   When creating a synchronization task, in the default configuration script of DataWorks, the record field value of errorLimit is 0. You need to change the value to a larger number, such as 1,000.
5.  After the preceding configurations are complete, in the upper right corner click **configuration tasks resources group**, and then click **Run**.

    If the prompt **Task run successfully is displayed**, it indicates that the task is synchronized successfully. If the task fails to run, copy the error logs for troubleshooting.


## Verify the synchronization result {#section_zcg_1jr_zgb .section}

1.  Go to the Elasticsearch console, click **Kibana console** in the upper right corner and then select **Dev Tools**.

2.  Run the following command to view the synchronized data.

    ```
    POST /hive_doc_esgood_sale/_search? pretty
    {
    "query": { "match_all": {}}
    }
    ```

    `hive_doc_esgood_sale` is the value of the index field when the data is synchronized.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155358967840081_en-US.png)


## Data query and analysis {#section_kbc_3jr_zgb .section}

1.  The following example returns all the documents of Brand A.

    ```
    POST /hive_doc_esgood_sale/_search? pretty
    {
      "query": { "match_phrase": { "brand":"Brand A" } }
    }
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155358967840082_en-US.png)

2.  The following example sorts various documents by **Clicks**, in order to view the popularity of all brands.

    ```
    POST /hive_doc_esgood_sale/_search? pretty
    {
    "query": { "match_all": {} },
    "sort": { "click_cnt": { "order": "desc" } },
    "_source": ["category", "brand","click_cnt"]
    }
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155358967840083_en-US.png)

    For more information about commands and access methods, see [Alibaba Cloud Elasticsearch documents](https://www.alibabacloud.com/help/zh/product/57736.htm) and [Elastic.co help center](https://cloud.elastic.co/?spm=a2c4g.11186623.2.33.4e81422aVNpLYG#help/).


