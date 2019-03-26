# Log monitoring {#concept_lqd_szl_zgb .concept}

Alibaba Cloud Elasticsearch provides the open-source Elasticsearch v5.5.3 and the X-Pack Business Edition to the scenarios such as data analysis and data search. A range of features such as enterprise-level rights management, security monitoring alerts, and automatic report generation are built upon open-source Elasticsearch.

## Monitoring log configuration {#section_az4_5gm_zgb .section}

**Log collection**

By default, X-Pack monitors clients and sends the collected cluster information every **10 seconds** to the index prefixed with .monitoring-\* of the instance you bought.

The indexes .monitoring-es-6-\* and .monitoring-kibana-6-\* are available and created on a daily basis. The collected information is saved in the index prefixed with .monitoring-es-6- and suffixed with the current date.

The .monitoring-es-6-\* index occupies a relatively large disk space. It stores information such as cluster status, cluster statistics, node statistics, and index statistics.

**System index display**

Select Show system indices on the Kibana page to view the space occupied by the index.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134323/155358139240012_en-US.png)

**Log retention**

By default, the monitored indexes of the past seven days are stored. These .monitoring-es-6-\* indexes occupy the ES instance space. The index size depends on the number of indexes \(including system indexes\) and the number of nodes in the cluster. To prevent the indexes from occupying most of instance space, use the following methods:

1.  Set the index retention days through the following API:

    ```
    PUT _cluster/settings
    {"persistent": {"xpack.monitoring.history.duration":"1d"}}
    # The number of days shall be configured according to your requirements. The indexes shall be retained at least one day.
    ```

2.  Specify the indexes to be monitored.

    You can specify which indexes need to be monitored through the API to reduce the disk space occupied by the .monitoring-es-6-\* indexes. In the following example, the system indexes are not monitored.

    ```
    PUT _cluster/settings
    {"persistent": {"xpack.monitoring.collection.indices": "*,-. *"}}
    # The disabled index information is not displayed in the Monitoring module of Kibana. For example, you cannot see the disabled index information in the index list or on the index monitoring page. In this situation, the index list obtained through _cat/indices is different from the index list displayed in the Monitoring module of Kibana.
    ```


**Note:** In practice, you can use both methods to save disk space.

