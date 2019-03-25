# ES云监控报警 {#concept_xzq_rzl_zgb .concept}

阿里云Elasticsearch已支持对实例进行监控，并允许通过短信报警。可根据您的需求，自定义报警阈值。

## 重要 {#section_gkm_yzl_zgb .section}

强烈建议配置监控报警。

-   集群状态。（主要监控集群状态是否为绿色或红色）
-   节点磁盘使用率\(%\)。（报警阀值控制在75%以下，不要超过80%）
-   节点HeapMemory使用率\(%\)。（报警阀值控制在85%以下，不要超过90%）

## 次要 {#section_rdn_zzl_zgb .section}

-   节点CPU使用率\(%\)。（报警阀值控制在95%以下，不要超过95%）
-   节点load\_1m。（以CPU核数的80%为参考值）
-   集群查询QPS\(Count/Second\)。（以实际测试结果作为参考）
-   集群写入QPS\(Count/Second\)。（以实际测试结果作为参考）

## 使用说明 {#section_owt_b1m_zgb .section}

进入方式：

-   Elasticsearch控制台。
-   云监控Elasticsearch标签页面。

**Elasticsearch控制台**

登录ES控制台，进入您的ES实例基本信息界面，点击集群监控转到ES云监控。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134321/155350383539982_zh-CN.png)

**云监控Elasticsearch标签**

使用账号登录阿里云控制台，选择产品导航栏下的云监控，再选择云服务监控菜单栏下的Elasticsearch。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134321/155350383539983_zh-CN.png)

## 监控指标配置 {#section_ffl_q1m_zgb .section}

1.  选择您需要查看的区域，点击ES实例ID

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134321/155350383539984_zh-CN.png)

2.  指标详情页面点击创建报警规则

    您可在此页面查看到集群既往监控数据，目前只保留1月内的监控信息。通过创建报警规则，可对此实例配置报警监控。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134321/155350383539985_zh-CN.png)

3.  填写相应的规则名称及规则描述

    以下配置主要以添加，磁盘报警监控、集群状态监控、节点HeapMemory使用率监控为例。

    -   集群的状态Green、Yellow、Red转换成数值对应 0.0、1.0、2.0。所以在配置集群状态报警指标时，需要按照对应数值大小进行配置报警。
    -   通道沉默时间是指，同一个指标在一定时间范围内，只会触发一次报警。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134321/155350383539986_zh-CN.png)

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134321/155350383539987_zh-CN.png)

4.  选择报警联系组

    如果没有报警联系组，可以点击快速创建联系人组，进行创建。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134321/155350383539988_zh-CN.png)

5.  点击确认按钮，保存报警配置。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134321/155350383539989_zh-CN.png)


**说明：** 

Elasticsearch监控信息将在实例正常生产后5分钟内开始采集，并提供监控数据展示。

