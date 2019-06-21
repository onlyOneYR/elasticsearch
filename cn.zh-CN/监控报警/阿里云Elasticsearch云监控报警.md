# 阿里云Elasticsearch云监控报警 {#concept_xzq_rzl_zgb .concept}

阿里云Elasticsearch已支持对实例进行监控，并允许通过短信接收报警。您可以根据需求，自定义报警阈值。本文档为您介绍阿里云Elasticsearch云监控报警的配置方法，帮助您快速地使用云监控报警对实例进行实时监控。

## 监控报警项 {#section_gkm_yzl_zgb .section}

**说明：** 强烈建议您配置监控报警。

以下三个报警项较为重要，强烈建议您进行配置。

-   集群状态。

    主要监控集群状态为绿色还是红色。

-   节点磁盘使用率\(%\)。

    报警阀值控制在75%以下，不要超过80%。

-   节点HeapMemory使用率\(%\)。

    报警阀值控制在85%以下，不要超过90%。


建议您同时配置以下几个报警项。

-   节点CPU使用率\(%\)。

    报警阀值控制在95%以下，不要超过95%。

-   节点load\_1m。

    以CPU核数的80%为参考值。

-   集群查询QPS\(Count/Second\)。

    以实际测试结果作为参考。

-   集群写入QPS\(Count/Second\)。

    以实际测试结果作为参考


## 进入云监控报警控制台 {#section_owt_b1m_zgb .section}

阿里云Elasticsearch为您提供以下两种方式进入云监控报警控制台。

-   Elasticsearch控制台。

    登录[阿里云Elasticsearch控制台](https://elasticsearch.console.aliyun.com/)，单击实例ID。在实例的**基本信息**页面，单击右上角的**集群监控**，即可进入对应Elasticsearch实例的云监控控制台页面。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134321/156111289239982_zh-CN.png)

-   云监控控制台。
    1.  登录[阿里云控制台](https://data.aliyun.com/)，选择产品导航栏下的**云监控**。
    2.  在云监控控制台中，单击左侧菜单栏的**云服务监控** \> **Elasticsearch**。
    3.  选择实例所在区域，并单击实例ID，即可进入对应Elasticsearch实例的云监控控制台页面。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134321/156111289239983_zh-CN.png)


## 监控指标配置 {#section_ffl_q1m_zgb .section}

1.  进入您阿里云Elasticsearch实例的[云监控报警控制台](#section_owt_b1m_zgb)。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134321/156111289349861_zh-CN.png)

    您可在此页面查看集群的历史监控数据，目前只保留一个月内的监控信息。通过创建报警规则，可对此实例配置报警监控。

2.  在实例的云监控控制台页面，单击右上角的**创建报警规则**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134321/156111289339985_zh-CN.png)

3.  在**创建报警规则**页面，设置**报警规则**。

    以添加**节点磁盘使用率**监控、**集群状态**监控、**节点HeapMemory使用率**监控为例，添加方式如下图所示。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134321/156111289339986_zh-CN.png)

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134321/156111289339987_zh-CN.png)

    -   集群的状态对应**Green**、**Yellow**、**Red**，转换成数值对应**0.0**、**1.0**、**2.0**。所以在配置**集群状态**报警指标时，需要按照对应数值的大小进行报警配置。
    -   **通道沉默时间**是指，同一个指标在一定时间范围内，只会触发一次报警。

        **说明：** 其他参数说明请参见[报警规则参数说明](../../../../cn.zh-CN/用户指南/报警服务/报警规则/报警规则参数说明.md#)。

4.  配置**通知方式**，选择**云账号报警联系人**。

    如果您还没有报警联系组，可以单击**快速创建联系人组**，进行创建。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134321/156111289439988_zh-CN.png)

5.  单击**确认**，保存报警配置，完成配置。

    配置完成后，Elasticsearch实例的监控信息将在实例正常生产后5分钟内开始采集，并提供监控数据展示。


