# logstash-input-sls插件使用说明 {#concept_2277371 .concept}

logstash-input-sls插件是阿里云Logstash系统自带的默认插件。作为Logstash的input插件，logstash-input-sls插件提供了从日志服务获取日志的功能。

**说明：** logstash-input-sls插件是阿里云Logstash提供的开源插件，欢迎您跟我们一起共建开源社区，维护该插件，详情请参见[logstash-input-logservice](https://github.com/aliyun/logstash-input-logservice/blob/master/README.md)。

## 功能特性 {#section_4ie_rgy_60j .section}

-   支持分布式协同消费：可配置多台服务器同时消费某一Logstore。
-   高性能：基于Java ConsumerGroup实现，单核消费速度可达20MB/s。
-   高可靠：消费进度保存到服务端，宕机恢复后会从上一次checkpoint处自动恢复。
-   自动负载均衡：根据消费者数量自动分配shard，消费者增加/退出后会自动进行负载均衡。

## 安装logstash-input-sls插件 {#section_j2w_9tx_gf4 .section}

1.  [进入Logstash实例管理页面](cn.zh-CN/Logstash服务/Logstash实例管理/实例管理.md#section_mt6_okl_tiw)。
2.  单击左侧导航栏的**插件配置**。
3.  在系统默认插件列表中，单击**logstash-input-sls**右侧的**安装**。

    **警告：** 安装logstash-input-sls插件会触发集群重启，请确认后再执行以下步骤。

4.  在操作提示对话框中，认真阅读系统提示，单击**确认**。

    ![安装logstash-input-sls插件](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1805959/156888634361264_zh-CN.png)

    确认后，Logstash实例会进行重启，重启过程中可在任务列表中[查看任务进度详情](cn.zh-CN/Logstash服务/Logstash实例管理/实例管理.md#section_npm_fg7_6jr)。重启成功后，即可完成**logstash-input-sls**插件的安装。

    安装成功后，如果不再使用，可单击**logstash-input-sls**插件右侧的**卸载**，使用同样的方式进行卸载。

    **说明：** 卸载插件也会触发集群重启，请确认后操作。


## 使用方式 {#section_upw_ao4_l50 .section}

在使用**logstash-input-sls**插件前，请首先[安装logstash-input-sls插件](#section_j2w_9tx_gf4)。

[创建Pipeline任务](cn.zh-CN/Logstash服务/快速入门/创建Pipeline任务.md#)，在任务创建过程中创建管道时，按照以下示例配置pipeline参数。该示例配置了Logstash消费某一个Logstore，并将日志打印，进行标准输出。

``` {#codeblock_qew_1lt_a9d}
input {
  logservice{
  endpoint => "your project endpoint"
  access_id => "your access id"
  access_key => "your access key"
  project => "your project name"
  logstore => "your logstore name"
  consumer_group => "consumer group name"
  consumer_name => "consumer name"
  position => "end"
  checkpoint_second => 30
  include_meta => true
  consumer_name_with_ip => true
  }
}

output {
  stdout {}
}
```

|参数名|参数类型|是否必填|说明|
|---|----|----|--|
|endpoint|string|是|日志服务项目所在的Endpoint，详情请参见[Endpoint列表](../../../../cn.zh-CN/API 参考/服务入口.md#)。|
|access\_id|string|是|阿里云Access Key ID，需要具备ConsumerGroup相关权限，详情请参见[使用消费组消费](../../../../cn.zh-CN/实时消费/消费组消费/通过消费组消费日志.md#)。|
|access\_key|string|是|阿里云Access Key Secret，需要具备ConsumerGroup相关权限，详情请参见[使用消费组消费](../../../../cn.zh-CN/实时消费/消费组消费/通过消费组消费日志.md#)。|
|project|string|是|日志服务项目名。|
|logstore|string|是|日志服务日志库名。|
|consumer\_group|string|是|消费组名。|
|consumer\_name|string|是|消费者名，同一个消费组内消费者名不能重复，否则会出现未定义行为。|
|position|string|是|消费位置，可选： -   begin：从日志库写入的第一条数据开始消费。
-   end：从当前时间点开始消费。
-   yyyy-MM-dd HH:mm:ss：从指定时间点开始消费。

 |
|checkpoint\_second|number|否|每隔几秒checkpoint一次，建议10~60秒，不能低于10秒，默认30秒。|
|include\_meta|boolean|否|传入日志是否包含Meta，Meta包括日志source、time、tag以及topic，默认为true。|
|consumer\_name\_with\_ip|boolean|否|消费者名是否包含IP地址，默认为true。分布式协同消费下必须设置为true。|

例如某Logstore有10个shard，每个shard数据流量1M/s；每台Logstash机器处理的能力为3M/s，可分配5台Logstash服务器；每个服务器设置相同的consumer\_group和consumer\_name，consumer\_name\_with\_ip字段设置为true。

这种情况每台服务器会分配到2个shard，分别处理2M/s的数据。

## 性能基准测试 {#section_n9z_rbi_a62 .section}

测试环境

-   处理器：Intel\(R\) Xeon\(R\) Platinum 8163 CPU @ 2.50GHz，4 Core
-   内存：8GB
-   环境：Linux

Logstash配置

``` {#codeblock_myc_dkt_1vt}
input {
  logservice{
  endpoint => "cn-hangzhou.log.aliyuncs.com"
  access_id => "***"
  access_key => "***"
  project => "test-project"
  logstore => "logstore1"
  consumer_group => "consumer_group1"
  consumer => "consumer1"
  position => "end"
  checkpoint_second => 30
  include_meta => true
  consumer_name_with_ip => true
  }
}

output {
  file {
  path => "/dev/null"
  }
}
```

测试过程

1.  使用Java Producer向Logstore发送数据，分别达到每秒发送2MB、4MB、8MB、16MB、32MB数据。

    每条日志约500字节，包括10个Key和Value对。

2.  启动Logstash，消费Logstore中的数据并确保消费延迟没有上涨（消费速度能够跟上生产的速度）。

测试结果

|流量（MB/S）|处理器占用（%）|内存占用（GB）|
|--------|--------|--------|
|32|170.3|1.3|
|16|83.3|1.3|
|8|41.5|1.3|
|4|21.0|1.3|
|2|11.3|1.3|

