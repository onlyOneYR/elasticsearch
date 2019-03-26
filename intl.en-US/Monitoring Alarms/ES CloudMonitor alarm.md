# ES CloudMonitor alarm {#concept_xzq_rzl_zgb .concept}

Alibaba Cloud Elasticsearch supports instance monitoring and allows text message alerting. You can set the alerting thresholds according to your needs.

## Important {#section_gkm_yzl_zgb .section}

It is strongly recommended to configure monitoring alerts.

-   Cluster status \( \(whether the cluster status indicator is green or red\)
-   Node disk usage \(%\) \(alerting threshold must be lower than 75%, and cannot exceed 80%\)
-   Node HeapMemory usage \(%\) \(alerting threshold must be lower than 85%, and cannot exceed 90%\)

## Other requirements {#section_rdn_zzl_zgb .section}

-   Node CPU usage \(%\) \(alerting threshold cannot exceed 95%\)
-   Node load\_1m \(reference value: 80% of the number of CPU cores\)
-   Cluster query QPS \(Count/Second\) \(reference value: practical test result\)
-   Cluster write QPS \(Count/Second\) \(reference value: practical test result\)

## Instructions for use {#section_owt_b1m_zgb .section}

Enter mode

-   Elasticsearch console
-   CloudMonitor Elasticsearch tab page

**Elasticsearch console**

Log on to the ES console and go to the ES instance basic information page. Click **Cluster Monitor** to go to the **ES Cloud Monitor** module.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134321/155358134639982_en-US.png)

**Cloud Monitor Elasticsearch tab**

Log on to the Alibaba Cloud console using your account, select **Cloud Monitor** in the product navigator, and choose Elasticsearch from the cloud service monitor menu.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134321/155358134739983_en-US.png)

## Monitor index configuration {#section_ffl_q1m_zgb .section}

1.  Choose the area you want to check and click the ES instance ID.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134321/155358134739984_en-US.png)

2.  Create alert policies on the index details page.

    On this page, you can check the historical cluster monitoring statistics. The monitoring statistics of the past month are stored. After creating alert policies, you can configure alert monitoring for this instance.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134321/155358134739985_en-US.png)

3.  Enter the policy name and description.

    In the following example, the monitoring on disk usage, cluster status, and node HeapMemory usage is configured.

    -   The cluster status green, yellow, and red match 0.0, 1.0, and 2.0, respectively. Set the values to configure the cluster status alert indexes.
    -   Within the channel silence time, one index can trigger alerting only once.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134321/155358134739986_en-US.png)

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134321/155358134739987_en-US.png)

4.  Select the alert contact group.

    To create a contact group, click **Quickly Create a Contact Group**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134321/155358134739988_en-US.png)

5.  Click **Confirm** to save the alert settings.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134321/155358134739989_en-US.png)


**Note:** 

Elasticsearch monitoring data is collected five minutes after the instance runs properly. Then the monitoring statistics are displayed.

