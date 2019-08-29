# ES集群配置 {#concept_d1d_rzl_zgb .concept}

## 分词配置 {#section_xkt_wvs_zgb .section}

作用于ES的同义词库，新的索引将会采用更新后的词库，配置操作详情请参见[配置同义词](cn.zh-CN/用户指南/实例管理/同义词配置.md)。

![ES分词配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134291/156706465440135_zh-CN.png)

**说明：** 

-   上传同义词词典文件保存提交后，不会触发阿里云ES实例重启，但需要一些时间来生效。
-   在上传同义词词典文件保存生效之前创建的索引如果需要使用同义词功能，需要重建索引并进行同义词相关配置。

每行一个同义词表达式，保存为`utf-8`编码的`.txt`文件。例如：

``` {#codeblock_oyr_8j6_x2q}
西红柿,番茄 =>西红柿,番茄
社保,公积金 =>社保,公积金
```

配置步骤：

1.  在阿里云ES控制台上传同义词词典文件，保存并生效成功。
2.  在创建索引配置 `setting`时，配置 `"synonyms_path": "analysis/your_dict_name.txt"`，再为该索引配置 `mapping`为指定字段设置同义词。
3.  校验同义词并上传测试数据进行搜索测试。

## 高级配置 {#section_99c_55k_4ji .section}

阿里云Elasticsearch 6.7.0及以上版本的实例（要求规格大于等于32GB）提供了垃圾回收器的配置功能，支持**CMS垃圾回收器**和**G1垃圾回收器**的相互切换，切换方法如下。

**说明：** 

-   垃圾回收器配置完成后，需要进行集群重启才能生效，请确认后操作。
-   只有6.7.0及以上版本的阿里云Elasticsearch实例才可以修改垃圾回收类型，5.5.3和6.3.x版本不支持修改。
-   6.7.0及以上版本的Elasticsearch实例，如果数据规格的内存大于等于32GB，支持CMS和G1垃圾回收器的相互切换，小于32GB只能使用CMS垃圾回收器。

    ![实例规格](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134291/156706465457956_zh-CN.png)


1.  在阿里云Elasticsearch实例的基本信息页面，单击**高级配置**右侧的**修改配置**。

    **警告：** 以下步骤会触发集群自动重启，请确认后操作。

2.  在修改配置页面，选择**G1回收器**，单击**确定**。

    ![切换垃圾回收器](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134291/156706465457961_zh-CN.png)

    确定后，集群会自动重启。重启成功后，即可完成垃圾回收器的切换。


## YML文件配置 {#section_5bc_pbm_wr0 .section}

控制台界面中的**YML文件配置**表示当前阿里云ES实例的使用配置。

![ES YML文件配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134291/156706465440137_zh-CN.png)

 修改配置 

修改**YML文件配置**后，需要通过重启阿里云ES实例生效。

**说明：** 修改**YML文件配置**完成后，再滑动到页面底部勾选**该操作会重启实例，请确认后操作**确认提交后，会自动触发重启阿里云ES实例进行生效。

![ES YML参数配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134291/156706465440138_zh-CN.png)

-   **自动创建索引**：此设定允许您的阿里云ES实例在接收到新文档后，如果没有对应索引，是否允许系统帮您自动新建索引。自动创建的索引有可能不符合您的预期，不建议开启。
-   **删除索引指定名称**：此配置表示在删除索引时是否需要明确指定索引名称，如果配置为支持通配符删除索引，则可以使用通配符进行批量删除索引。索引删除后不可恢复，请谨慎使用此配置。
-   **Auditlog索引**：此设定允许记录您的阿里云ES实例对应的增、删、改、查等操作产生的索引日志，该日志信息会占用您的磁盘空间，同时也会影响性能，不建议开启，请谨慎使用此配置。
-   **开启Watcher**：开启后可使用X-Pack的Watcher功能，请注意定时清理`.watcher-history*`索引，避免占用大量磁盘空间。
-   **其他configure配置**：支持部分配置项如下，详情请参见[yml配置](cn.zh-CN/用户指南/实例管理/yml配置.md)。

    **说明：** 以下没有标识具体适用于某个ES版本的参数，默认兼容ES 5.5.3和6.3.2版本。

    -   http.cors.enabled
    -   http.cors.allow-origin
    -   http.cors.max-age
    -   http.cors.allow-methods
    -   http.cors.allow-headers
    -   http.cors.allow-credentials
    -   reindex.remote.whitelist
    -   action.auto\_create\_index
    -   action.destructive\_requires\_name
    -   thread\_pool.bulk.queue\_size（只适用于Elasticsearch 5.5.3 with X-Pack版本）
    -   thread\_pool.write.queue\_size（只适用于Elasticsearch 6.3.2 with X-Pack版本）
    -   thread\_pool.search.queue\_size

