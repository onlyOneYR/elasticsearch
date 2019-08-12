# BSearch-Label插件使用介绍 {#concept_1663893 .concept}

BSearch-Label是一个纯前端的数据打标插件。通过BSearch-Label插件，您可以无需编写复杂的DSL语句，而是以可视化的方式完成数据打标。本文档为您介绍BSearch-Label插件的使用方法，帮助您快速使用BSearch-Label插件完成查询业务。

## 背景信息 {#section_ybx_3k2_sib .section}

通常情况下，在分析数据的时候，您可能不仅是单纯的浏览，而是希望通过某些查询条件对数据进行分析，并对某个字段（或者新增一个字段）赋予一个特殊的值（标签）来标注不同的数据，这一过程被称为“打标”。对数据打标后，您可以根据这个标签进行聚合分类统计，也可以根据标签的不同值进行快速过滤。标注的数据还可以直接为后续的流程所使用。

## 安装BSearch-Label插件 {#section_20f_7k1_879 .section}

**说明：** 当您购买了阿里云Elasticsearch实例后，我们会为您赠送一个1核2G的Kibana节点。由于插件需要耗费较多的资源，所以在安装插件前，您需要将该Kibana节点升级为**2核4G**或以上规格，详细请参见[集群升配](cn.zh-CN/用户指南/实例管理/集群升配.md#)。

![Kibana节点升级](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/615344/156560557149787_zh-CN.png)

1.  登录[阿里云Elasticsearch控制台](https://elasticsearch.console.aliyun.com/)，并[购买一个Elasticsearch实例](../../../../cn.zh-CN/快速入门/开通阿里云Elasticsearch服务.md#)。
2.  单击**实例ID** \> **可视化控制**。
3.  在**Kibana**模块中，单击**修改配置**。

    ![可视化控制页面](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156560557249321_zh-CN.png)

4.  在**Kibana配置**页面的**插件配置**区域，单击插件列表**操作**栏下的**安装**。

    **说明：** 

    -   确认安装后会触发Kibana节点重启，所以在安装过程中Kibana不能正常提供服务，为避免影响您的Kibana操作，请确认后操作。
    -   如果您的Kibana规格低于**2核4G**，系统会提示您进行集群升配，请按照提示将您的Kibana节点升级到**2核4G**或以上规格。
5.  确认安装并重启Kibana节点。

    重启后即可完成插件的安装，安装成功后，插件的状态显示为**已安装**。

    ![插件安装成功](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156560557249322_zh-CN.png)

    **说明：** 此过程可能耗时较长，请耐心等待。


## 使用BSearch-Label插件 {#section_z9z_qm0_m0l .section}

1.  返回Elasticsearch实例的**可视化控制**页面，单击**Kibana**模块中的**进入控制台**。
2.  输入Kibana控制台的用户名和密码，单击**登录**。

    默认的用户名为**elastic**，密码为您购买实例时设置的密码。

3.  在Kibana控制台中，单击**Discover** \> **打标**。

    **说明：** 在查询前，请确保您已经创建了一个索引模式。否则需要在Kibana控制台中，单击**Management**，再单击**Kibana**模块中的**Index Patterns** \> **Create index pattern**，按照提示创建一个索引模式。

4.  根据您的需求，选择以下任意一种方式完成数据打标。
    -   对已有字段进行打标。

        如下示例，先查询到名字是张三的数据，然后选择age字段，将其标记为**18**，单击**确认打标**。

        ![对已有字段进行打标](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1318852/156560557255145_zh-CN.jpg)

        打开**历史打标**开关，可查看历史打标任务详情。

        ![历史打标结果](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1318852/156560557355146_zh-CN.jpg)

    -   新增字段进行打标。

        如下示例，先查询到名字是张三的数据，然后勾选**自定义打标字段**，新增一个字段tag，将其标记为**teenager**，单击**确认打标**。

        ![新增字段进行打标](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1318852/156560557355149_zh-CN.jpg)

        查看打标结果。

        ![查看打标结果](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1318852/156560557355152_zh-CN.jpg)


