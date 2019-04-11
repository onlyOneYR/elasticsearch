# Elasticsearch cluster configuration {#concept_d1d_rzl_zgb .concept}

## Word splitting {#section_xkt_wvs_zgb .section}

This feature uses the synonym dictionary. New indexes will use the updated synonym dictionary. For more information, see [Configure synonyms for Elasticsearch instances](reseller.en-US/User Guide/Instance management/Configure synonyms for Elasticsearch instances.md).

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134291/155497166540135_en-US.png)

**Note:** 

-   After you upload and submit a synonym dictionary file, the Alibaba Cloud Elasticsearch instance will not restart immediately. It takes some time for the new configuration to take effect.
-   If an index that is created before the uploaded synonym dictionary file takes effect needs to use synonyms, you must recreate the indexes and configure synonyms.

Write one synonym expression in each row and save the code as a `UTF-8` encoded `.txt` file. Examples:

```
corn, maize => maize, corn
begin, start => start, begin
```

Configuration procedure:

1.  Upload and save a synonym dictionary file in the Alibaba Cloud Elasticsearch console. Make sure that the uploaded file takes effect.
2.  When you create an index and configure the `settings`, you need to specify the `"synonyms_path": "analysis/your_dict_name.txt"` path. Add a `mapping` for this index to configure synonyms for the specified field.
3.  Confirm the synonyms and upload a file for testing.

## YML configurations {#section_bgc_xvs_zgb .section}

The **YML Configurations** page displays the settings of the current Alibaba Cloud Elasticsearch instance.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134291/155497166540137_en-US.png)

**Modify YML configurations**

After you modify the **YML Configurations**, you must restart the Alibaba Cloud Elasticsearch instance for the new configuration to take effect.

**Note:** After you modify the **YML Configurations**, select **This operation requires a restart of the instance. Exercise with caution.** at the bottom of the page and click OK. The Alibaba Cloud Elasticsearch instance automatically restarts.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134291/155497166540138_en-US.png)

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134291/155497166540139_en-US.png)

-   Create Index Automatically: if you enable this feature, it allows the system to automatically create new indexes if a new file is uploaded to the Alibaba Cloud Elasticsearch instance and no indexes have been created on the file. We recommend that you disable this feature. Indexes created by this feature may not meet your requirements.
-   Delete Index With Specified Name: this feature indicates whether you are required to specify the name of the index that you need to delete. If you select Delete Index Name with Wild Characters, you can delete multiple indexes by using a wildcard character. Indexes that are deleted cannot be restored. Proceed with caution.
-   Audit Log Index: if you enable this feature, index logs are created and stored when you **create**, **delete**, **modify**, or **view** an Alibaba Cloud Elasticsearch instance. These logs consume disk space and affect the performance. We recommend that you disable this feature. Proceed with caution.
-   Watcher: if you enable this feature, it allows you to use the X-Pack Watcher feature. Make sure that you regularly clear the `.watcher-history*` index. This index consumes large amounts of disk space.
-   Other Configurations: the following parameters are supported. For more information, see [YML configuration](reseller.en-US/User Guide/Instance management/YML configuration.md).

    **Note:** Excluding the parameters that have an Alibaba Cloud Elasticsearch version specified, the remaining parameters can only be applied to Elasticsearch V5.5.3 and V6.3.2.

    -   http.cors.enabled
    -   http.cors.allow-origin
    -   http.cors.max-age
    -   http.cors.allow-methods
    -   http.cors.allow-headers
    -   http.cors.allow-credentials
    -   reindex.remote.whitelist
    -   action.auto\_create\_index
    -   action.destructive\_requires\_name
    -   thread\_pool.bulk.queue\_size \(Elasticsearch V5.5.3 with X-Pack\)
    -   thread\_pool.write.queue\_size \(Elasticsearch V6.3.2 with X-Pack\)
    -   thread\_pool.search.queue\_size

