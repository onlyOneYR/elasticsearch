# 借助Beats搭建可视化运维系统 {#concept_kcx_4hm_zgb .concept}

## 背景介绍 {#section_irt_33m_zgb .section}

Beats平台集合了多种单一用途数据采集器，这些采集器安装后可用作轻量型代理，从成百上千或成千上万台机器向 Logstash 或 Elasticsearch 发送数据。

Metricbeat是一个轻量级的指标采集器，用于从系统和服务收集指标。从 CPU 到内存，从 Redis 到 Nginx，Metricbeat 能够以一种轻量型的方式，输送各种系统和服务统计数据。

这篇文章主要向用户演示，如何使用Metricbeat采集一台Mac电脑的指标信息，投递到阿里云Elasticsearch（以下统称阿里云ES）上，并且在Kibana中生成对应dashborard。

**说明：** 使用Metricbeat采集一台基于Linux系统或windows系统电脑的指标信息，投递到阿里云Elasticsearch（以下统称阿里云ES）上也是类似的操作步骤。

1.  购买并配置阿里云ES

    如果没有阿里云ES实例，需要先进行[准备工作](../../../../../intl.zh-CN/快速入门/准备工作.md#)。可以通过阿里云ES提供的内网地址/公网地址将本地MAC中的数据推送给阿里云ES。

    **说明：** 

    -   如果是通过阿里云ES公网地址来访问（本篇以此为例），需要先打开阿里云ES实例公网访问的开关，并在网络配置及数据备份界面中配置**公网地址访问白名单**。

    -   如果是通过阿里云ES内网地址来访问，需要先购买一台与阿里云ES实例相同**VPC**和**Region**的阿里云ECS实例进行访问操作。

    1.  登陆阿里云ES控制台，单击阿里云ES实例管理，切换到网络配置及数据备份界面，打开公网地址开关。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134309/155496639340014_zh-CN.png)

    2.  将自己的MAC机器对外的公网IP配置到公网地址访问白名单中。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134309/155496639340015_zh-CN.png)

**说明：** 如果你使用的是公司或WIFI等网络，需要将公网出口的跳板机IP配置进去。如果获取不到，建议配置0.0.0.0/1,128.0.0.0/1来开放尽可能多的IP（本篇以此为例），**需要特别注意这个配置将导致你的阿里云ES基本上完全暴露在公网中，需要先评估下是否可以接受这个风险。**

    3.  配置完成后，获取阿里云ES的公网地址备用。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134309/155496639340016_zh-CN.png)

    4.  修改YML文件配置**允许自动创建索引**（默认不允许），该操作会触发重启阿里云ES进行生效，需要一些时间来生效。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134309/155496639440017_zh-CN.png)

2.  下载并配置Metricbeat
    -   MAC系统的Metricbeat安装包[下载地址](https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-6.3.2-darwin-x86_64.tar.gz?spm=a2c4e.11153940.blogcont618611.15.4bb4639amOtBZb&file=metricbeat-6.3.2-darwin-x86_64.tar.gz)。
    -   32位Linux系统的Metricbeat安装包[下载地址](https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-6.3.2-linux-x86.tar.gz?spm=a2c4e.11153940.blogcont618611.16.4bb4639amOtBZb&file=metricbeat-6.3.2-linux-x86.tar.gz)。
    -   64位Linux系统的Metricbeat安装包[下载地址](https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-6.3.2-linux-x86_64.tar.gz?spm=a2c4e.11153940.blogcont618611.17.4bb4639amOtBZb&file=metricbeat-6.3.2-linux-x86_64.tar.gz)。
    -   32位Windows系统的Metricbeat安装包[下载地址](https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-6.3.2-windows-x86.zip?spm=a2c4e.11153940.blogcont618611.18.4bb4639amOtBZb&file=metricbeat-6.3.2-windows-x86.zip)。
    -   64位Windows系统的Metricbeat安装包[下载地址](https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-6.3.2-windows-x86_64.zip?spm=a2c4e.11153940.blogcont618611.19.4bb4639amOtBZb&file=metricbeat-6.3.2-windows-x86_64.zip)。
    1.  下载到本地某一个文件夹后，解压缩，进入Metricbeat文件夹。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134309/155496639440018_zh-CN.png)

    2.  打开并编辑 metricbeat.yml 中**Elasticsearch output**部分内容，需取消对应内容注释状态。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134309/155496639440019_zh-CN.png)

        **说明：** 

        阿里云ES提供了用户名和密码的访问控制。

        -   hosts：为阿里云ES实例的公网/内网地址（本篇以阿里云ES公网地址为例）。
        -   protocol：需要配置为 http。
        -   username：默认是**elastic**。
        -   password：为购买阿里云ES服务时填写的登陆密码。
3.  启动Metricbeat

    启动Metricbeat向阿里云ES推送数据。

    ```
    ./metricbeat -e -c metricbeat.yml
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134309/155496639440020_zh-CN.png)

4.  在Kibana中查看dashboard

    打开阿里云ES实例中集成的kibana控制台，切换到dashboard界面展示如下。

    **说明：** 

    如果kibana控制台中没有创建过**Index Patterns**，切换到dashboard界面后可能无法正常展示对应信息，此时可以创建1个Index Patterns，再切换到dashboard界面查看对应内容。

    1.  各类相关指标列表。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134309/155496639440021_zh-CN.png)

    2.  Metricbeat-cpu指标信息。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134309/155496639440022_zh-CN.png)

        **说明：** 数据可以定义成5s刷新一次，并且可以生成对应的report，接入webhook对异常进行告警。


