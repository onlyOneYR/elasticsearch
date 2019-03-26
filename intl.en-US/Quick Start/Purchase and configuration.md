# Purchase and configuration {#concept_xd1_cmk_zgb .concept}

## Purchase Elasticsearch products {#section_bpg_tjl_zgb .section}

1.  You can find the Alibaba Cloud Elasticsearch product page from the Alibaba Cloud product navigation. You can also visit the [Alibaba Cloud Elasticsearch product details page](https://www.alibabacloud.com/product/elasticsearch) directly. Click **Buy Now** to go to the buy page.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134282/155356847239931_en-US.png)

2.  Select the corresponding configuration, VPC, VSwitch, and specification based on your business needs. For more information, see [Buy page parameters](intl.en-US/Quick Start/Buy page parameters.md).

    **Note:** If you have not activated the VPC or VSwitch service, activate the services by referring to [Virtual Private Cloud](https://www.alibabacloud.com/product/vpc) under VPC. If you have activated the VPC and vSwitch services, you can directly use the services.

3.  Follow the instructions on the page to complete the configuration and then click **Buy Now**. You will be redirected to the order confirmation page. Confirm the information, agree to the terms of service, and click **Activate**. The page will display that your service has been successfully activated.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134282/155356847239933_en-US.png)

4.  After your service has been activated, click **Elasticsearch Console** to log on the Alibaba Cloud Elasticsearch console.

5.  You can view the Elasticsearch instances that you have purchased on the Alibaba Cloud Elasticsearch console. Please wait until the instance is activated.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134282/155356847239935_en-US.png)

6.  Before importing data to the activated Elasticsearch instance, you must manually create indexes and mappings. Errors may occur if the indexes and mappings are not created at the same time. For example, if data is being imported to the instance after you have deleted the existing index mappings on the instance, the instance will automatically create index mappings that do not meet your requirements. This may cause errors.

    To prevent this problem, the **Create Index Automatically** feature is disabled by default. You must first create indexes and mappings before importing data. Otherwise, an error will occur.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134282/155356847239936_en-US.png)

    **Note:** 

    If your business depends on the auto index creation feature, you can enable it by modifying the **YML Configurations** on the Elasticsearch Cluster Configuration page in the Alibaba Cloud Elasticsearch console, and then restart the instance for the changes to take effect.

7.  After completed these steps, import data to the instance and test the search function.

    **Note:** The default Alibaba Cloud Elasticsearch account is `elastic`, and its password is the one that you have set on the buy page. The password is the same as the one that is used to log on to Kibana. For more information about importing data and testing the search function, see [Elasticsearch access test](intl.en-US/Quick Start/Elasticsearch access test.md).


## Configure monitoring and alerts {#section_a1b_5jl_zgb .section}

Alibaba Cloud Elasticsearch allows you to monitor instances and supports SMS message alerts. Elasticsearch also allows you to set an alert threshold. For more information, see[ES CloudMonitor alarm](../../../../../intl.en-US/Cloud Monitor/ES CloudMonitor alarm.md).

-   Cluster status
-   Cluster query QPS \(count/second\)
-   Cluster write QPS \(count/second\)
-   Node CPU utilization \(%\)
-   Node disk utilization \(%\)
-   Node HeapMemory utilization \(%\)
-   Node load\_1m

## Elasticsearch access configuration {#section_jmx_5jl_zgb .section}

**Access methods**

-   Search for data using the Kibana console integrated in the Alibaba Cloud Elasticsearch console.
-   Search for data using a user application or the cURL command line tool. You must deploy the application or install the cURL command line tool on an Alibaba Cloud ECS instance that shares the same VPC with your Alibaba Cloud Elasticsearch instance. The ECS and Elasticsearch instances must be in the same region.
-   Access through the Public Address of Alibaba Cloud Elasticsearch.

**Precautions for ECS-based access**

-   If your ECS instance is in a classic network, configure ClassicLink for Elasticsearch access. For details, see [Classic network errors](../../../../../intl.en-US/FAQ/Classic network errors.md).

-   ClassicLink supports only unidirectional access from a classic network to a VPC and does not allow bidirectional access between the two networks.

**ECS-based access \(optional\)**

If you have purchased an Alibaba Cloud ECS instance \(in the same region as your Alibaba Cloud Elasticsearch instance\) under the VPC of Alibaba Cloud Elasticsearch, you can use this ECS instance as the client to deploy the user program or install the cURL command line tool for search.

If you do not have a qualified Alibaba Cloud ECS instance, purchase one in the same VPC as your Alibaba Cloud Elasticsearch instance. Select the required instance type on the [Elastic Compute Service](https://www.alibabacloud.com/product/ecs) page.

-   ECS products purchased in the same VPC as Alibaba Cloud Elasticsearch are used to deploy user applications. They can be used as clients.
-   If no ECS product is available in the same VPC as Alibaba Cloud Elasticsearch, you can still test the search function using the Kibana console. You can also choose to buy an ECS product in the same VPC.

**ECS cURL-based access \(optional\)**

1.  Log on to the ECS instance in the same VPC as the Elasticsearch cluster through SSH, and install the cURL command line tool on the instance.
2.  Add cURL to the environment variables and use it to access the Alibaba Cloud Elasticsearch service remotely.

## Configure the Elasticsearch blacklist and whitelist {#section_qhw_vjl_zgb .section}

Alibaba Cloud Elasticsearch provides an X-Pack plug-in of the enterprise edition to enhance the security when you access a node through the URL of your Alibaba Cloud Elasticsearch instance. You can create an IP blacklist and whitelist to control the access to an Elasticsearch instance. For more information, see [ES集群配置](../../../../../intl.en-US/User Guide/Instance management/ES集群配置.md).

