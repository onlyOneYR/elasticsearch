# Synchronize data from an ApsaraDB RDS for MySQL database to an Alibaba Cloud Elasticsearch instance, and query and analyze data {#concept_mvk_jpr_zgb .concept}

Alibaba Cloud provides you with a wide range of cloud storage and database services. If you want to analyze and search data stored in these services, use Data Integration to replicate the data to Alibaba Cloud Elasticsearch, and then query or analyze the data. Data Integration allows you to replicate data at a minimum interval of five minutes.

**Note:** Data replication generates public network traffic and may incur fees.

## Prerequisites {#section_r22_rpr_zgb .section}

Perform the following tasks before you analyze or query the on-premises data:

-   Create a database. You can use an ApsaraDB RDS for MySQL database, or create a database on your local server. This example uses an ApsaraDB RDS for MySQL database. The following figure shows the dataset stored in the database:

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134312/155505013640085_en-US.png)

-   Purchase an Alibaba Cloud Elastic Compute Service \(ECS\) instance that is connected to the same VPC network as your Alibaba Cloud Elasticsearch instance. This ECS instance is used to retrieve data from data sources and run tasks to write the data to the Alibaba Cloud Elasticsearch instance. The tasks are dispatched by Data Integration.

-   Activate Data Integration, and add the ECS instance to Data Integration as a resource to run synchronization tasks.

-   Configure a data synchronization script and run the script periodically.

-   Create an Alibaba Cloud Elasticsearch instance to store the data synchronized by Data Integration.


## Procedure {#section_cgx_spr_zgb .section}

**Synchronize data**

1.  [Create a VPC](../../../../../reseller.en-US/Quick Start/Create a VPC.md#).

2.  Log on to the [Alibaba Cloud Elasticsearch console](https://elasticsearch-cn-hangzhou.console.aliyun.com/) and click **Create** to create an Alibaba Cloud Elasticsearch instance.

**Note:** The **region**, **VPC network**, and the **VSwitch** that you specify for the Alibaba Cloud Elaticsearch instance must be the same as those of the VPC network that you have created in the step 1.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134312/155505013640086_en-US.png)

3.  Purchase an ECS instance that is connected to the same VPC network as the Alibaba Cloud Elasticsearch instance, and assign a public IP address or activate Elastic IP Address \(EIP\) to the ECS instance. To save costs, we recommend that you use an existing ECS instance that meets the requirements.

**Note:** 

-   We recommend that you use CentOS6, CentOS7, or AliyunOS.
-   If the ECS instance needs to run MaxCompute or data synchronization tasks, you must verify that the current Python version of the ECS instance is 2.6 or 2.7. The Python version of CentOS 5 is 2.4 while that of other CentOS versions is 2.6 or later.
-   Make sure that the ECS instance has a public IP address.
4.  Log on to the [DataWorks console](https://workbench.data.aliyun.com/consolenew#/).

    -   The following page is displayed if you have already activated Data Integration:

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134312/155505013640087_en-US.png)

    -   The following page is displayed if you have not activated Data Integration: Perform the following steps to activate Data Integration. Activating Data Integration incurs service fees. You can estimate the costs based on the billing items.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134312/155505013640088_en-US.png)

5.  Click **Data Integration**.

6.  On the Data Integration page, click **Resource Group** in the left-side navigation pane, and then click **Add Resource Group** in the upper-right corner.

7.  Enter the resource group name and server information as required. The server you add on this page refers to the ECS instance that you have purchased. Enter the following information:

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134312/155505013640089_en-US.png)

    -   ECS UUID: enter the UUID of the ECS instance. Log on to the ECS instance and run the `dmidecode | grep UUID` command to obtain the UUID. For more information, see [Step 3: Connect to an instance](../../../../../reseller.en-US/Quick Start for Entry-Level Users/Step 3: Connect to an instance.md#).

    -   Server IP, Machine CPU \(Cores\), and Machine RAM \(GB\): enter the public IP address of the ECS instance, the CPU size, and the memory size. To obtain the information, log on to the ECS console and click the ECS instance name. The information is listed in the **Configuration Information** area.

    -   Follow the instructions on the page to **install an agent**. Step 5 opens port 8000 of the ECS instance. You can use the default settings and skip this step.

8.  Configure the database whitelist. Add the IP address of the resource group and the IP address of the ECS instance to the whitelist. For more information about whitelist configuration, see [Add whitelist](../../../../../reseller.en-US/User Guide/Data integration/Common configuration/Add whitelist.md#).

9.  After you create the resource group, click **Data Source** in the left-side navigation pane, and then click **Add Data Source** in the upper-right corner.

10. Select **MySQL**. On the **Add Data Source MySQL** page, enter the required information, as shown in the following figure:

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134312/155505013640090_en-US.png)

    Data Source Type: this example uses **an ApsaraDB RDS for MySQL database**. You can select **Public IP Address Available** or **Public IP Address Unavailable**. For more information about the parameters, see [Configure MySQL data source](../../../../../reseller.en-US/User Guide/Data integration/Data source configuration/Configure MySQL data source.md#).

11. In the left-side navigation pane, click **Sync Resources** and then click **Create Task**. Select **Script Mode**.

12. In the Apply Template dialog box, choose **Source Type** \> **MySQL**. Enter the name of the data source that you have added in step 10 in the **Data Source** field and select **Elasticsearch** from the **Destination Type** drop-down list. Confirm the information and click **OK**.

    ![](images/40091_en-US.png)

13. Configure a data synchronization script For more information about the configuration, see [Script mode configuration](../../../../../reseller.en-US/User Guide/Data integration/Task configuration/Configure reader plug-in/Script mode configuration.md#). For more information about Alibaba Cloud Elasticsearch instance configuration rules, see [Configure Elasticsearch Writer](../../../../../reseller.en-US/User Guide/Data integration/Task configuration/Configure writer plug-in/Configure Elasticsearch Writer.md#).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134312/155505013640092_en-US.png)

    **Note:** 

    -   A data synchronization script includes three sections: the reader, writer, and settings. The reader sections contain the configuration of the data source \(cloud resource\) that stores the data to be synchronized. The writer section contains the configuration of the Alibaba Cloud Elasticsearch instance. The settings section contains data synchronization settings, such as the packet loss threshold and maximum concurrency.
    -   You can set the endpoint to the internal or public IP address of the Alibaba Cloud Elasticsearch instance. If you use the internal IP address, you must configure a system whitelist for the Alibaba Cloud Elasticsearch instance on the Elasticsearch Cluster Configuration page. If you use the public IP address, you must configure a whitelist on the Security page for the Alibaba Cloud Elasticsearch instance to allow visits from public IP addresses. The whitelist must include the [IP address of the ECS instance added to DataWorks](../../../../../reseller.en-US/User Guide/Data integration/Common configuration/Add whitelist.md#) and the IP address of the resource group that you use.
    -   Set the accessId and accessKey parameters in the writer section to the username and password of the Alibaba Cloud Elasticsearch instance, respectively.
    -   Set the index parameter in the writer section to the index that of the Alibaba Cloud Elasticsearch instance. This index is used to access the data stored on the Alibaba Cloud Elasticsearch instance.
14. After you have configured the synchronization script, click **Configure Resource Group** on the right side of the page and select the resource group that you have created in step 7. Confirm and click **Run** to replicate data from the MySQL database to the Elasticsearch instance.


## Query and analyze data {#section_r2j_jqr_zgb .section}

1.  Log on to the Elasticsearch console, click **Kibana Console** in the upper-right corner, and then click **Dev Tools**.

2.  Run the following command to view the synchronized data:

    ```
    POST /testrds/_search? pretty
    {
    "query": { "match_all": {}}
    }
    ```

    testrds is the value specified in the index parameter in the data synchronization script.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134312/155505013640093_en-US.png)

3.  Run the following command to sort the data based on the trans\_num column:

    ```
    POST /testrds/_search? pretty
    {
    "query": { "match_all": {} },
    "sort": { "trans_num": { "order": "desc" } }
    }
    ```

4.  Run the following command to query the category and brand columns in the data:

    ```
    POST /testrds/_search? pretty
    {
    "query": { "match_all": {} },
    "_source": ["category", "brand"]
    }
    ```

5.  Run the following command to query data entries where the category column is set to **Raw**:

    ```
    POST /testrds/_search? pretty
    {
    "query": { "match": {"category":"Raw"} }
    }
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134312/155505013740094_en-US.png)

    For more information about how to access Elasticsearch, see [Elasticsearch access test](../../../../../reseller.en-US/Quick Start/Elasticsearch access test.md#) and [Elastic documentation](https://cloud.elastic.co/#help/).


## FAQ {#section_czx_5qr_zgb .section}

-   An error occurred while accessing the database.

    Solution: Add the internal and public IP addresses of the ECS instance that in the resource group to the DataWorks database whitelist.

-   An error occurred while accessing the Alibaba Cloud Elasticsearch instance.

    Solution: perform the following steps:

    1.  Check whether you have selected the resource group created in the preceding step from **Configure Resource Group**.

        -   Go to the next step if you have selected the correct resource group.
        -   If you have not selected the correct resource group, click **Configure Resource Group** to select the correct one. Confirm and click **Run**.
    2.  Check whether you have added the [IP address of the ECS instance](../../../../../reseller.en-US/User Guide/Data integration/Common configuration/Add whitelist.md#) and the IP address of the resource group to the whitelist of the Elasticsearch instance.

        -   Go to the next step if you have added these IP addresses to the whitelist.
        -   If you have not added these IP addresses to the whitelist, add the [IP address of the ECS instance](../../../../../reseller.en-US/User Guide/Data integration/Common configuration/Add whitelist.md#) and the IP address of the resource group to the whitelist of the Elasticsearch instance.

            **Note:** If you use the internal IP address, configure a system whitelist for the Elasticsearch instance on the **Security** page. If you use the public IP address, configure a whitelist for the Elasticsearch instance on the Security page to allow visits from public IP addresses. The whitelist must include the [IP address of the ECS instance](../../../../../reseller.en-US/User Guide/Data integration/Common configuration/Add whitelist.md#) and the IP address of the resource group.

    3.  Check whether the configuration of the script is correct Check the endpoint, accessId, and accessKey. The endpoint must be set to the internal or public IP address of the Elasticsearch instance. The accessId must be set to the username of the Elasticsearch instance. The default name is elastic. The accessKey must be set to the password of the Elasticsearch instance.


