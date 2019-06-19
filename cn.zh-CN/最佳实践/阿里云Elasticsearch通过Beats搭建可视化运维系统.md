# 阿里云Elasticsearch通过Beats搭建可视化运维系统 {#concept_kcx_4hm_zgb .concept}

本文档以使用Metricbeat采集Mac电脑的指标信息，投递到阿里云Elasticsearch上，并在Kibana中生成对应Dashborard的场景为例，为您介绍通过Beats和阿里云Elasticsearch搭建可视化运维系统的方法。

## 教程概述 {#section_irt_33m_zgb .section}

Beats平台集合了多种单一用途的数据采集器，这些采集器安装后可用作轻量型代理，从成百上千或成千上万台机器向Logstash或Elasticsearch发送数据。

Metricbeat是一个轻量级的指标采集器，用于从系统和服务中收集指标。从CPU到内存，从Redis到Nginx，Metricbeat能够以一种轻量型的方式，输送各种系统和服务的统计数据。

本案例为您演示如何使用Metricbeat采集一台Mac电脑的指标信息，投递到阿里云Elasticsearch上，并且在Kibana中生成对应的Dashborard，整体步骤如下。

1.  [准备工作](#)。
2.  [配置阿里云Elasticsearch](#)。
3.  [配置Metricbeat](#)。
4.  [在Kibana中查看Dashboard](#)。

**说明：** 

-   您也可以参考本案例的步骤，使用Metricbeat采集一台Linux系统或Windows系统电脑的指标信息，投递到阿里云Elasticsearch上。
-   本案例参考文档：[借助Beats快速搭建可视化运维系统](https://yq.aliyun.com/articles/618611)。

## 注意事项 {#section_9f1_t1j_693 .section}

本案例使用了**0.0.0.0/1,128.0.0.0/1**的Elasticsearch实例公网白名单，这个配置将导致您的阿里云Elasticsearch基本上完全暴露在公网中，在进行同样配置前请先评估是否可以接受这个风险。

## 准备工作 {#section_jr3_8er_5dz .section}

在开始本案例前，您需要完成以下准备工作。

-   [购买阿里云Elasticsearch实例](../../../../cn.zh-CN/快速入门/购买和配置.md#section_bpg_tjl_zgb)。

    **说明：** 如果您需要通过阿里云Elasticsearch实例的内网地址来访问，还需要先购买一台与阿里云Elasticsearch实例相同VPC和Region的阿里云ECS实例进行访问操作。

-   下载Metricbeat。
    -   MAC系统的Metricbeat安装包[下载地址](https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-6.3.2-darwin-x86_64.tar.gz?spm=a2c4e.11153940.blogcont618611.15.4bb4639amOtBZb&file=metricbeat-6.3.2-darwin-x86_64.tar.gz)。
    -   32位Linux系统的Metricbeat安装包[下载地址](https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-6.3.2-linux-x86.tar.gz?spm=a2c4e.11153940.blogcont618611.16.4bb4639amOtBZb&file=metricbeat-6.3.2-linux-x86.tar.gz)
    -   64位Linux系统的Metricbeat安装包[下载地址](https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-6.3.2-linux-x86_64.tar.gz?spm=a2c4e.11153940.blogcont618611.17.4bb4639amOtBZb&file=metricbeat-6.3.2-linux-x86_64.tar.gz)
    -   32位Windows系统的Metricbeat安装包[下载地址](https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-6.3.2-windows-x86.zip?spm=a2c4e.11153940.blogcont618611.18.4bb4639amOtBZb&file=metricbeat-6.3.2-windows-x86.zip)
    -   64位Windows系统的Metricbeat安装包[下载地址](https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-6.3.2-windows-x86_64.zip?spm=a2c4e.11153940.blogcont618611.19.4bb4639amOtBZb&file=metricbeat-6.3.2-windows-x86_64.zip)

## 配置阿里云Elasticsearch {#section_0oa_zsm_i2l .section}

1.  登录[阿里云Elasticsearch控制台](https://elasticsearch.console.aliyun.com/)，单击**实例ID** \> **安全配置**。
2.  打开**公网地址**开关，待配置生效后，单击**公网地址访问白名单**右侧的**修改**，将您MAC机器对外的公网IP配置到公网地址访问白名单中。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134309/156092597040014_zh-CN.png)

    **说明：** 如果您使用的是公司或WIFI等网络，需要将公网出口的跳板机IP配置进去。如果获取不到，建议配置**0.0.0.0/1,128.0.0.0/1**来开放尽可能多的IP（本篇以此为例）。 需要特别注意这个配置将导致您的阿里云Elasticsearch基本上完全暴露在公网中，需要先评估下是否可以接受这个风险。

3.  返回实例的**基本信息**页面，获取您阿里云Elasticsearch实例的**公网地址**备用。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134309/156092597140016_zh-CN.png)

4.  切换到**ES集群配置**页面，单击**YML文件配置**右侧的**修改配置**，将**自动创建索引**设置为**允许自动创建索引**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134309/156092597140017_zh-CN.png)

    **说明：** 此配置需要重启您的Elasticsearch实例才能生效，为保证您的业务不受影响，请谨慎操作。

5.  勾选**该操作会重启实例，请确认后操作**，单击**确认**。

    此过程约持续30分钟左右，请您耐心等待。重启完成后，即可完成Elasticsearch实例的配置。


## 配置Metricbeat {#section_9lq_8o1_zbz .section}

1.  将在[准备工作](#)中下载的Metricbeat安装包解压缩，并进入Metricbeat文件夹。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134309/156092597140018_zh-CN.png)

2.  打开并编辑metricbeat.yml中`Elasticsearch output`部分内容，并取消对应内容的注释状态。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134309/156092597240019_zh-CN.png)

    -   `hosts`：为阿里云Elasticsearch实例的公网/内网地址（本篇以阿里云Elasticsearch实例的公网地址为例）。
    -   `protocol`：需要配置为`http`。
    -   `username`：默认是`elastic`。
    -   `password`：为购买阿里云Elasticsearch实例时填写的登录密码。
3.  执行以下命令，启动Metricbeat。

    ``` {#codeblock_syd_atq_kpb}
    ./metricbeat -e -c metricbeat.yml
    ```

    启动成功后，Metricbeat就开始向您的阿里云Elasticsearch推送数据了。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134309/156092597240020_zh-CN.png)


## 在Kibana中查看Dashboard {#section_hqi_ruu_1tq .section}

进入您阿里云Elasticsearch实例的Kibana控制台，单击左侧导航栏的**Dashboard**，进入**Dashboard**页面查看相关信息。

**说明：** 如果Kibana控制台中没有创建过Index Patterns，切换到**Dashboard**页面后可能无法正常展示对应信息。此时可在Kibana控制台的**Management**页面单击**Index Patterns**，并按照提示创建一个**Index Patterns**，再切换到**Dashboard**页面查看对应内容。

-   各类相关指标列表。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134309/156092597240021_zh-CN.png)

-   Metricbeat-cpu指标信息。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134309/156092597340022_zh-CN.png)

    **说明：** 您可以将数据定义成5s刷新一次，并且可以生成对应的报表，接入webhook对异常进行告警。


