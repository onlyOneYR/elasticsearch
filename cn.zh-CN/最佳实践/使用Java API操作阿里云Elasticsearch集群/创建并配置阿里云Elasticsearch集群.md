# 创建并配置阿里云Elasticsearch集群 {#task_525687 .task}

本文档为您介绍创建并配置阿里云Elasticsearch集群的方法，包括了购买实例、开启公网地址、配置白名单、配置自动创建索引等操作。

阿里云Elasticsearch只支持专有网络，因此在创建Elasticsearch集群前，请先[创建专有网络和交换机](../../SP_22/DNVPC11885991/ZH-CN_TP_2434_V13.dita#concept_isl_ghv_rdb)。

1.  登录[阿里云Elasticsearch控制台](https://elasticsearch.console.aliyun.com)并购买一个实例。 

    本案例购买的实例信息如下图，其中版本需要选择6.3，选择其他版本可能会存在兼容性问题。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/422732/156756740648838_zh-CN.png)

    **说明：** 在购买过程中需要设置Elasticsearch集群的登录密码，请记录您设置的密码，并妥善保管，在使用Java API连接您的集群时会使用到该密码。

    实例购买成功后，会进入实例列表页面，等待**状态**变为**正常**后，继续执行以下步骤。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/422732/156756740748839_zh-CN.png)

    **说明：** 此过程大概需要30分钟左右，请耐心等待。

2.  开启实例公网地址。 

    单击实例名称，进入**安全配置**页面，打开**公网地址**开关。

    **说明：** **公网地址**打开后，可在实例的**基本信息**页面查看，请记录该公网地址，在使用Java API连接您的集群时会使用到该地址。

3.  修改Elasticsearch实例的公网地址访问白名单，将您本机的公网IP地址加入白名单中。![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/422732/156756740748843_zh-CN.png)

 获取本机公网IP地址的方法：在百度输入IP进行搜索，即可获取您本机的公网IP。
4.  开启Elasticsearch集群的自动创建索引配置。 

    **说明：** 该操作会重启实例，为保证您的业务不受影响，请仔细确认后操作！

    1.  进入Elasticsearch实例的**ES集群配置**页面，单击**YML文件配置**模块右侧的**修改配置**。
    2.  在YML参数配置页面，将**自动创建索引**设置为**允许自动创建索引**。
    3.  勾选页面下方的**该操作或重启实例，请确认后操作**，单击**确认**。![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/422732/156756740748845_zh-CN.png)



Elasticsearch集群重启完成后，您就可以[使用Java API来操作集群](ZH-CN_TP_422735_V1.dita#task_525695)了。

