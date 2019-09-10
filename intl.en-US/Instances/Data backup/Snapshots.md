# Snapshots {#concept_err_fts_zgb .concept}

This topic describes the snapshot feature of Alibaba Cloud Elasticsearch.

Log on to the Alibaba Cloud Elasticsearch console, click **Instance Name** \> **Snapshots** to navigate to the Snapshots \(Free Trial\) page.

![Snapshots (Free Trial) page](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134298/156811831340194_en-US.png)

|Parameter|Description|
|---------|-----------|
|**Auto Snapshot**|When the **Auto Snapshot** switch is in the **green** color, auto snapshot is enabled. By default, auto snapshot is disabled.|
|**Auto Snapshot Period**|If auto snapshot is disabled, the **You must enable auto snapshot first** message is displayed. **Note:** Auto snapshot uses the system time of the region where the Elasticsearch instance is created. Do not perform any snapshot operations when the system is creating snapshots.

 |
|**Edit Configuration**|If auto snapshot is enabled, you can click **Edit Configuration** in the upper-right corner to open the Auto Snapshot Configuration dialog box and then set the time for creating snapshots.![Auto snapshot configuration](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134298/156811831340196_en-US.png)

 **Note:** 

-   The **Frequency** parameter is set to Daily.
-   The **Create Snapshot At** parameter specifies the specific time for creating a snapshot daily. Valid values are from 0 to 23 hours.
-   Alibaba Cloud Elasticsearch only stores snapshots that are created in the last three days.

 |
|**Restore from Snapshot**|Click **View Tutorial** to learn how to restore data from a snapshot.|
|**Snapshot Status**|Click **View Tutorial** to learn more information about snapshot status.|

