# Cloud data import {#concept_m5f_phm_zgb .concept}

## Import data from Alibaba Cloud to Alibaba Cloud ES \(offline\) {#section_rnx_5br_zgb .section}

Alibaba Cloud stores an abundance of cloud storage and database products. If you want to analyze and search for data in these products, visit and [Data Integration](https://www.alibabacloud.com/product/data-integration), which allows you to synchronize offline data to Elasticsearch **every five minutes**.

## Supported data source {#section_kyf_fcr_zgb .section}

-   Alibaba Cloud database \(MySQL, PostgreSQL, SQL Server, PPAS, MongoDB, and HBase\)
-   Alibaba Cloud DRDS
-   Alibaba Cloud MaxCompute \(ODPS\)
-   Alibaba Cloud OSS
-   Alibaba Cloud Table Store
-   Self-developed HDFS, Oracle, FTP, DB2, and self-developed versions of the previous cloud databases

**Note:** Data synchronization may produce public network traffic cost.

## Procedure {#section_yn2_hcr_zgb .section}

Take the following steps to import offline data.

-   Prepare an ECS instance that can interact with Elasticsearch within a VPC. This ECS instance will obtain data sources and execute a job to write ES data \(the job is centrally issued by Data Integration\).
-   You need to activate the Data Integration service and register the ECS instance to the Data Integration service as an executable job resource.
-   Configure a data synchronization script and make it run periodically.

**Steps**

1.  Buy an ECS instance that is in the same VPC as the Elasticsearch service. Allocate a public IP address to the ECS instance or enable the elastic IP address for the ECS instance. To lower costs, you can use an existing ECS instance. For how to buy an ECS instance, see [Step 2. Create an instance](../../../../../intl.en-US/Quick Start for Entry-Level Users/Step 2. Create an instance.md#)Buy an ECS instance.

    **Note:** 

    -   CentOS 6, CentOS 7, and AliyunOS are recommended.
    -   If the added ECS instance needs to run MaxCompute or synchronization tasks, verify whether the current Python version of the ECS instance is 2.6 or 2.7. \(The Python version of CentOS 5 is 2.4 while those of other operating systems are later than 2.6.\)
    -   Ensure that the ECS instance has a public IP address.
2.  Log on to the [Data Integration console](https://workbench.data.aliyun.com/console#/) to open the workbench.

    If Data Integration or DataWorks has been enabled, you can see:

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134310/155358513840057_en-US.png)

    If Data Integration or DataWorks is not enabled, the following message is displayed. Follow the instructions to activate the Data Integration service. This is a **paid service**, so check the quoted price against your budget.

3.  Go to the [Project Management-Scheduling Resource Management](https://workbench-cn-shanghai.data.aliyun.com/console#/62890/scheduleManage) page of the Data Integration service to configure the ECS instance in the VPC as a scheduling resource. For more information, see [Add task resources](../../../../../intl.en-US/User Guide/Data integration/Common configuration/Add task resources.md#).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134310/155358513840059_en-US.png)

4.  Configure the data synchronization script in the Data Integration service. For the configuration procedure, see [Script mode configuration](../../../../../intl.en-US/User Guide/Data integration/Task configuration/Configure reader plug-in/Script mode configuration.md#)Data synchronization script configuration. For the instructions on configuring Elasticsearch, see [Configure Elasticsearch Writer](../../../../../intl.en-US/User Guide/Data integration/Task configuration/Configure writer plug-in/Configure Elasticsearch Writer.md#)ES Writer.

    **Note:** 

    -   The synchronization script configuration includes three parts: Reader is the configuration of upstream data source \(cloud product ready for data synchronization\), Writer is the configuration of ES, and setting refers to the synchronization configurations such as packet loss rate and maximum concurrency.
    -   The accessId and accessKey of ES Writer are the Elasticsearch user name and password, respectively.
5.  After configuring the script, submit the data synchronization job. Set the job execution cycle and click **Ok**.

    **Note:** 

    -   If you are configuring a periodic scheduling, set the parameters such as Job Start Time, Execution Interval, and Job Lifecycle in this pop-up window.
    -   A periodic job is executed at 00:00 on the next day according to the rule you have configured.
6.  After the submission, go to the [O&M Center-Task Scheduling](https://workbench-cn-shanghai.data.aliyun.com/schedule.html#/cycleTask) page to find the submitted job, and change its scheduling resource from **default** to the **scheduling resource you have configured**.


## Import real-time data {#section_ykh_hdr_zgb .section}

This function is currently under development and will become available in the future.

