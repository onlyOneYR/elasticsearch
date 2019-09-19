# 创建Pipeline任务 {#task_2261556 .task}

本文档为您介绍通过Kibana Pipeline管理页面，控制多个Logstash实例添加、删除和编辑管道的方法。

管道实现方式：

-   通过Logstash配置文件定义了Logstash管道，使用-f<path/to/file\>启动一个Logstash实例（即使用了一个配置文件定义了管道实例）。
-   通过pipelines.yml配置文件在同一进程中运行多个管道。
-   通过Kibana访问并配置单进程Pipeline，目前阿里云Logstash Service只支持通过Kibana控制台配置管道。本文以此为例。

## 通过Kibana配置Pipeline {#section_umt_qff_njf .section}

1.  [登录ES实例的Kibana控制台](../../../../cn.zh-CN/实例/可视化控制/Kibana/登录Kibana控制台.md#)。
2.  单击左侧导航栏的**Management**。
3.  在Management页面，单击**Logstash**下的**Pipelines**。
4.  在Pipelines页面中，单击**Create pipeline**。![创建管道步骤](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1591392/156885670760371_zh-CN.png)


5.  在Create pipeline页面，输入**Pipeline ID**和**Description**，并根据需求配置其他参数。![创建管道页面](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1591392/156885670760372_zh-CN.png)

 配置时，可将鼠标移至参数上，查看相关说明。

    |参数|说明|
    |--|--|
    |Pipeline ID|管道名称。|
    |Description|管道配置的描述。|
    |Pipeline|管道配置。需要配置正确的输入、输出源地址。例如：     ``` {#codeblock_5s4_2ud_ct6}
input {
    kafka {
    bootstrap_servers => ["192.168.xx.xx:9092,192.168.xx.xx:9092,192.168.xx.xx:9092"]
    group_id => "group_1"
    topics => ["logstash_test"]
    consumer_threads => 6
    decorate_events => true
    }
}
output {
elasticsearch {
hosts => ["es-cn-o40xxxxxxxxxxxxwm.elasticsearch.aliyuncs.com:9200"]
index => "logstash_test_1"
password => "es_password"
user => "elastic"
}
}
    ```

 |
    |Pipeline workers|用于运行管道的过滤器和输出阶段的并行工作器数。|
    |Pipeline batch size|单个工作线程在执行过滤器和输出之前收集的最大事件数。|
    |Pipeline batch delay|在将小型批处理发送给管道工作者之前等待每个事件的时间（以毫秒为单位）。|
    |Queue type|事件缓冲的内部排队模型。选项是内存中队列的内存，或者是基于磁盘的确认队列的持久性。|
    |Queue max bytes|队列的总容量。|
    |Queue checkpoint writes|启用持久队列时强制检查点之前写入的最大事件数。|

6.  单击**Create and deploy**完成创建。 创建成功后，系统直接返回Pipelines页面，展示创建成功的管道。
7.  在Pipelines页面，单击已经创建成功的**管道ID**，可在Edit Pipeline页面修改管道配置。 **管道ID**不可修改。

## 管道管理配置 {#section_u1w_0ya_mu8 .section}

1.  [进入Logstash实例管理页面](cn.zh-CN/Logstash服务/实例管理.md#section_mt6_okl_tiw)。
2.  单击左侧导航栏的**管道管理**。
3.  在管道管理页面，单击**关联Elasticsearch实例**右侧的**修改**。![管道管理页面](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1591392/156885670760370_zh-CN.png)

 目前Logstash的**管道管理方式**仅支持**集中式管道管理**。您需要首先在所要关联的ES实例的Kibana控制台中，创建和配置Logstash管道。
4.  在修改配置页面，选择Elasticsearch实例，并输入ES实例的用户名和密码。 用户名为所选的ES集群的用户名（一般为elastic），密码为创建集群时设置的密码。
5.  单击**测试连通性并获取管道列表**。 连通成功后，系统显示**管道ID**下拉框。
6.  选择**管道ID**。 

    如果您还没有**管道ID**，可单击**前往创建**链接，前往所选Elasticsearch实例的Kibana控制台创建并配置管道，详情请参见[通过Kibana配置Pipeline](#section_umt_qff_njf)。

    ![前往创建](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1591392/156885670760376_zh-CN.png)

    管道创建成功后，返回Logstash管道管理的修改配置页面，再次单击**测试连通性并获取管道列表**，获取新创建的管道ID。

    ![获取管道ID](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1591392/156885670760374_zh-CN.png)

    **警告：** 管道配置变更需要重启Logstash进程，请在不影响业务的情况下，继续执行以下操作。

7.  勾选重启Logstash进程重启注意事项，单击**确认**。 确认后，Logstash会进行重启。重启过程中，可在[任务列表](cn.zh-CN/Logstash服务/实例管理.md#section_npm_fg7_6jr)中查看重启进度。重启成功后，即可完成Logstash实例的管道配置，并启动相应的数据传输进程。

