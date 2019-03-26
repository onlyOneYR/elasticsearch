# Synchronize data between MaxCompute and Elasticsearch with DataWorks {#concept_m4j_4ns_zgb .concept}

Alibaba Cloud provides you with a wide range of cloud storage and database services. If you want to analyze and search data in these services, use Data Integration to replicate your on-premises data to Alibaba Cloud Elasticsearch, and then search or analyze the data. Data Integration allows you to replicate data at a minimum interval of five minutes.

**Note:** Data replication generates public network traffic and may incur fees.

## Prerequisites {#section_pmq_5ns_zgb .section}

Follow these steps to analyze and search on-premises data:

-   [Create and view a table](../../../../../intl.en-US/Quick Start/Create and view a table.md#), and [import data](../../../../../intl.en-US/Quick Start/Import data.md#). You can [migrate data from Hadoop to MaxCompute](../../../../../intl.en-US/Best Practices/Data migration/Migrate data from Hadoop to MaxCompute.md#), and then synchronize the data. This example uses the following table schemes and data:

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155359192740112_en-US.png)

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155359192740113_en-US.png)

-   Create an Alibaba Cloud Elasticsearch instance to store the data that is successfully replicated by Data Integration.

-   Purchase an Alibaba Cloud ECS instance that shares the same VPC with Alibaba Cloud Elasticsearch. This ECS instance will obtain data and execute Elasticsearch tasks \(these tasks will be sent by Data Integration\).

-   Activate Data Integration, and register the ECS instance with Data Integration as a resource that can execute tasks.

-   Configure a data synchronization script and periodically run the script.


## Procedure {#section_rpb_yns_zgb .section}

1.  Create Alibaba Cloud Elasticsearch and ECS instances
    1.  [Create a VPC](../../../../../intl.en-US/Quick Start/Create a VPC.md#). This example creates a VPC in the **China \(Hangzhou\)** region. The instance name is **es\_test\_vpc**, and the corresponding VSwitch name is **es\_test\_switch**.

    2.  Log on to the [Alibaba Cloud Elasticsearch console](https://elasticsearch-cn-hangzhou.console.aliyun.com/), and create an Alibaba Cloud Elasticsearch instance.

**Note:** Make sure that you select the same **region**, **VPC**, and **VSwitch** with the VPC that you have created in the preceding step.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155359192740114_en-US.png)

    3.  Purchase an ECS instance that is in the same VPC as your Elasticsearch instance, and assign a public IP address or activate EIP. To save costs, we recommend that you use an existing ECS instance that meets the requirements.

        This example creates an ECS instance in **Zone F of China \(Hangzhou\)**. Select **64-bit CentOS 7.4** and **Assign Public IP** to configure network settings, as shown in the following figure:

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155359192840115_en-US.png)

        **Note:** 

        -   We recommend that you use CentOS 6, CentOS 7, or Aliyun Linux.
        -   If the ECS instance that you have created needs to execute MaxCompute tasks or data synchronization tasks, you must verify that the version of Python running on the instance is either Python 2.6 or 2.7. When you install CentOS 5, Python 2.4 is also installed. Other versions of CentOS include Python 2.6 and later.
        -   Make sure that your ECS instance is assigned a public IP address.
2.  Configure data synchronization
    1.  Log on to the [DataWorks console](https://workbench.data.aliyun.com/consolenew#/) to create a project. This example uses a DataWorks project named **bigdata\_DOC**.

        -   If you have already activated Data Integration, the following page is displayed:

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155359192840116_en-US.png)

        -   If you have not activated Data Integration, the following page is displayed: You must follow these steps to activate Data Integration. Activating this service incurs fees, which you can estimate based on the pricing rules.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155359192840117_en-US.png)

    2.  Click **Data Integration** under the DataWorks project.

    3.  Create resource group

        1.  On the Data Integration page, select **Resource Groups** in the left-side navigation bar, and click **Add Resource Group**.

        2.  Follow these steps to add a resource group:

            1.  Create a resource group: Enter a resource group name. This example names the resource group as **es\_test\_resource**.

                ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155359192840118_en-US.png)

            2.  Add a server.

                ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155359192840119_en-US.png)

                -   ECS UUID: [Step 3: Connect to an instance](../../../../../intl.en-US/Quick Start for Entry-Level Users/Step 3: Connect to an instance.md#). Log on to the ECS instance, and run the `dmidecode | grep UUID` command to obtain a returned value\].

                    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155359192940120_en-US.png)

                -   Machine IP/Machine CPUs \(Cores\)/Memory Size \(GB\): Specify the public IP address, CPU cores, and memory size of the ECS instance. Log on to the ECS console, and click the name of the instance to view the relevant information in the **Configuration Information** module.

            3.  Install an agent: Complete the **installation of Agent** following these steps. This example uses a VPC. Therefore, you do not need to open port 8000 for the instance.
            4.  Verify the connectivity: After the connection is successfully established, the status is changed to **Available**. If the status is **Unavailable**, you must log on to the ECS instance, and run the `tail -f /home/admin/alisatasknode/logs/heartbeat.log` command to check whether the heartbeat message between DataWorks and the ECS instance is timed out.
    4.  Add a data source.

        1.  On the Data Integration page, select **Data Source** in the left-side navigation bar, and click **Add Data Source**.

        2.  Select **MaxCompute** as the source type.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155359192940121_en-US.png)

        3.  Enter information about the data source. This example creates a data source named **odps\_es**, as shown in the following figure:

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155359192940122_en-US.png)

            -   **ODPS workspace name**: On the Data Analytics page of DataWorks, the corresponding workspace name of a table is displayed on the right of the icon in the upper left corner, as shown in the following figure:

                ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155359192940123_en-US.png)

            -   **AccessKeyId**/**AccessKeySecrete**: Move the pointer over your username and select **User Info**, as shown in the following figure:

                ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155359192940793_en-US.png)

                On the Personal Account page, move the pointer over your avatar, and click **accesskeys** as shown in the following figure:

    5.  Configure the synchronization task.

        1.  On **Data Analytics** page, click the **Data Analytics** icon in the left-side navigation pane, and click **Business Flow**.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155359192940797_en-US.png)

        2.  Click the target business flow, select **Data Integration**, select **Create Data Integration Node** \> **Data Sync**, and then enter the synchronization node name.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155359193040798_en-US.png)

        3.  After successfully creating the synchronization node, click the **Switch to Script Mode** icon at the top of the new synchronization node page, and select **Confirm**.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155359193040799_en-US.png)

        4.  At the top of on the Script Mode page, click the **Apply Template** icon. Enter the corresponding information for Source Type, Data Source, Destination source type and data source options, and then click **OK** to generate an initial script.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155359193040800_en-US.png)

        5.  [Configure the data synchronization script](../../../../../intl.en-US/User Guide/Data integration/Task configuration/Configure reader plug-in/Script mode configuration.md#). For more information about configuration rules of Elasticsearch, see [Configure writer plug-ins](../../../../../intl.en-US/User Guide/Data integration/Task configuration/Configure writer plug-in/Configure Elasticsearch Writer.md#).

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155359193040126_en-US.png)

**Note:** 

-   The configuration of the synchronization script contains three parts: Reader, Writer, and Setting. Reader is used to configure the source cloud services whose data you want to synchronize. Write is used to configure the config file of Alibaba Cloud Elasticsearch. Setting is used to configure settings for packet loss and maximum concurrent tasks.
-   Endpoint specifies the private or public IP address of the Alibaba Cloud Elasticsearch instance. This example uses a private IP address. Therefore, no whitelist is required. If you use an external IP address, you must configure a whitelist that contains public IP addresses that are allowed to access Elasticsearch on the Network and Snapshots page of Alibaba Cloud Elasticsearch. The whitelist must contain the [IP addresses of your DataWorks server](../../../../../intl.en-US/User Guide/Data integration/Common configuration/Add whitelist.md#) and the resource groups you use.
-   You must configure the username and password that are used to log on to the Alibaba Cloud Elasticsearch instance in accessId and accesskey of Elasticsearch Writer.
-   Enter the index name of the Elasticsearch instance in index. You need to use this index name to access the data on the Alibaba Cloud Elasticsearch instance. This example uses the index named es\_index.
-   If your MaxCompute table is a partitioned table, you must configure the partition information in the partition field. The partition information in this example is **pt=1**.
            Sample configuration code:

            ```
            {
            "configuration": {
            "reader": {
            "plugin": "odps",
            "parameter": {
              "partition": "pt=1",
              "datasource": "odps_es",
              "column": [
                "create_time",
                "category",
                "brand",
                "buyer_id",
                "trans_num",
                "trans_amount",
                "click_cnt"
              ],
              "table": "hive_doc_good_sale"
            }
            },
            "writer": {
            "plugin": "elasticsearch",
            "parameter": {
              "accessId": "elastic",
              "endpoint": "http://es-cn-mpXXXXXXX.elasticsearch.aliyuncs.com:9200",
              "indexType": "elasticsearch",
              "accessKey": "XXXXXX",
              "cleanup": true,
              "discovery": false,
              "column": [
                {
                  "name": "create_time",
                  "type": "string"
                },
                {
                  "name": "category",
                  "type": "string"
                },
                {
                  "name": "brand",
                  "type": "string"
                },
                {
                  "name": "buyer_id",
                  "type": "string"
                },
                {
                  "name": "trans_num",
                  "type": "long"
                },
                {
                  "name": "trans_amount",
                  "type": "double"
                },
                {
                  "name": "click_cnt",
                  "type": "long"
                }
              ],
              "index": "es_index",
              "batchSize": 1000,
              "splitter": ",",
            }
            },
            "setting": {
            "errorLimit": {
              "record": "0"
            },
            "speed": {
              "throttle": false,
              "concurrent": 1,
              "mbps": "1",
              "dmu": 1
            }
            }
            },
            "Type": "job ",
            "version": "1.0"
            }
            ```

        6.  After the script is synchronized, click **Run** to synchronize ODPS data to Alibaba Cloud Elasticsearch.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155359193040802_en-US.png)

3.  Verify the result
    1.  Log on to the Alibaba Cloud Elasticsearch console, click **Kibana console** in the upper-right corner, and select **Dev Tools**.

    2.  Run the following command to verify that data is successfully replicated to Elasticsearch.

```
POST /es_index/_search? pretty
{
"query": { "match_all": {}}
}
```

        es\_index indicates the value of the index field during data synchronization.

        If data is successfully synchronized, the following page is displayed:

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155359193040128_en-US.png)

    3.  Run the following command to sort documents based on the trans\_num field:

        ```
        POST /es_index/_search? pretty
        {
        "query": { "match_all": {} },
        "sort": { "trans_num": { "order": "desc" } }
        }
        ```

    4.  Run the following command to search the category and brand fields in documents:

        ```
        POST /es_index/_search? pretty
        {
        "query": { "match_all": {} },
        "_source": ["category", "brand"]
        }
        ```

    5.  Run the following command to query documents whose category is fresh:

        ```
        POST /es_index/_search? pretty
        {
        "query": { "match": {"category":"fresh"} }
        }
        ```

        For more information, see [Elasticsearch access test](../../../../../intl.en-US/Quick Start/Elasticsearch access test.md#) and [Elastic help center](https://cloud.elastic.co/#help/).


## FAQ {#section_mkf_brs_zgb .section}

**An error occurs when connecting to the Alibaba Cloud Elasticsearch instance**

1.  Before you execute the synchronization script, check whether you have selected the resource group that you have created in the preceding step on the right-side **configuration tasks resources group** menu.

    -   If you have selected the resource group, go to the next step.
    -   If you have not selected the resource group, click the right-side **configuration tasks resources group** menu, select the resource group that you have created, and click **Run**.
2.  Check whether the configuration of the synchronization script is correct, including the endpoint, accessId, and accesskey. The endpoint specifies the private or public IP address of your Elasticsearch instance. Configure a whitelist if you use a public IP address. The accessId specifies the username that is used to access the Elasticsearch instance, which is elastic by default. The accesskey specifies the password that is used to access the Elasticsearch instance.


