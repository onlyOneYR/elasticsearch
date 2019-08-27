# BSearch-QueryBuilder插件使用介绍 {#concept_261567 .concept}

BSearch-QueryBuilder又称高级查询，是一个纯前端的工具插件。通过BSearch-QueryBuilder插件，您可以无需编写复杂的DSL语句，而是以可视化的方式完成复杂的查询请求。本文档为您介绍BSearch-QueryBuilder插件的使用方法，帮助您快速使用BSearch-QueryBuilder插件完成查询业务。

## BSearch-QueryBuilder的特性 {#section_v2x_85q_3f3 .section}

BSearch-QueryBuilder具有如下特性。

-   简单易用：BSearch-QueryBuilder插件提供了可视化的界面点选操作来构造Elasticsearch的DSL查询请求，无编码即可完成自定义条件的数据查询，减少了复杂的DSL的学习成本。也可辅助开发人员编写或验证DSL语句的正确性。
-   方便快捷：已经定义的复杂查询条件会保存在Kibana中，避免重复构造查询条件。
-   小巧轻盈：约占用14MB的磁盘空间，不会常驻内存运行，不影响Kibana和Elasticsearch的正常运行。
-   安全可靠：BSearch-QueryBuilder插件不会对用户的数据进行改写、存储和转发，源代码已经通过了阿里云安全审计。

## 背景信息 {#section_mr3_162_9uo .section}

Query DSL是一个Java开源框架，用于构建安全类型的SQL查询语句，能够使用API代替传统的拼接字符串来构造查询语句。目前Query DSL支持的平台包括JPA、JDO、SQL、Java Collections、RDF、Lucene以及Hibernate Search。

Elasticsearch提供了一整套基于JSON的DSL查询语言来定义查询。Query DSL是由一系列抽象的查询表达式组成，特定查询能够包含其它的查询（如bool），部分查询能够包含过滤器（如constant\_score），还有的可以同时包含查询和过滤器（如 filtered）。您可以从ES支持的查询集合里面选择任意一个查询表达式，或者从过滤器集合里面选择任意一个过滤器进行组合，构造出复杂的查询。但编写DSL容易出错，仅有少数专业程序人员精通，QueryBuilder能够帮助对Elasticsearch DSL不甚了解或者想提升编写效率的用户快速生成DSL。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231260049225_zh-CN.png)

## 准备工作 {#section_tl7_i7c_s9f .section}

在使用BSearch-QueryBuilder插件前，请先[购买一个Elasticsearch实例](../../../../intl.zh-CN/快速入门/开通阿里云Elasticsearch服务.md#)，实例版本为6.3或6.7（不支持5.5.3）。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231260149427_zh-CN.png)

**说明：** 您也可以使用已经创建的实例，如果实例版本不符合要求，可进行版本升级。

## 安装BSearch-QueryBuilder插件 {#section_09m_zuc_pm4 .section}

**说明：** 在安装BSearch-QueryBuilder插件前，请确保您的Kibana节点为**2核4G**或以上规格，否则需要进行[集群升配](intl.zh-CN/用户指南/实例管理/集群升配.md#)。

1.  进入[阿里云Elasticsearch控制台](https://elasticsearch.console.aliyun.com/)。
2.  单击实例名称链接，再单击左侧导航栏的**可视化控制**。
3.  在**可视化控制**页面，单击**Kibana**模块的**修改配置**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231260149321_zh-CN.png)

4.  在**Kibana配置**页面，单击**插件配置**列表中**bsearch\_querybuilder**右侧的**安装**。

    **说明：** 确认安装后会触发Kibana节点重启，为避免影响您的Kibana操作，请确认后在再执行。

5.  确认安装并重启Kibana节点。

    重启后即可完成插件的安装，安装成功后，插件的状态显示为**已安装**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231260149322_zh-CN.png)

    **说明：** 此过程可能耗时较长，请耐心等待。


## 使用BSearch-QueryBuilder插件 {#section_37c_alq_zuo .section}

1.  返回Elasticsearch实例的**可视化控制**页面，单击**Kibana**模块中的**进入控制台**。
2.  输入Kibana控制台的用户名和密码，单击**登录**。

    默认的用户名为**elastic**，密码为您购买实例时设置的密码。

3.  在Kibana控制台中，单击**Discover** \> **Query**。

    **说明：** 在查询前，请确保您已经创建了一个索引模式。否则需要在Kibana控制台中，单击**Management**，再单击**Kibana**模块中的**Index Patterns** \> **Create index pattern**，按照提示创建一个索引模式。

4.  在查询区域选择查询和过滤条件，单击**提交**。

    提交成功后，系统会显示查询结果。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231260149357_zh-CN.png)

    单击查询区域的![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231260149385_zh-CN.png)可添加一个查询条件；单击![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231260249386_zh-CN.png)可为查询添加一个子过滤条件；单击![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231260249391_zh-CN.png)可删除一个查询或过滤条件。

    具体的查询方式请参见下文的[BSearch-QueryBuilder插件使用示例](#section_mc9_1mf_vau)。


## BSearch-QueryBuilder插件使用示例 {#section_mc9_1mf_vau .section}

BSearch-QueryBuilder支持模糊查询、多条件组合查询和自定义时间范围查询等多种查询方式。

-   模糊查询

    下图中表示对**email**这个条件进行模糊查询，并要求**email**中模糊匹配**iga**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231260249284_zh-CN.png)

    最终得到的匹配结果如下。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231260249285_zh-CN.png)

-   多条件组合查询

    下图的查询条件表示**index**必须为**tryme\_book**，同时要对**type**进行过滤，要求**type**等于**大学教辅**、**数学**、**对外汉语教学**或**大学教材**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231260249286_zh-CN.png)

    最终得到的匹配结果如下。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231260349287_zh-CN.png)

-   自定义时间范围查询

    当您需要对时间字段进行筛选时，可使用时间类型的筛选功能。下图中对**utc\_time**进行时间范围筛选，查询`[当前时间-240天,当前时间]`范围内的数据。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231260349288_zh-CN.png)

    最终得到的匹配结果如下。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231260449289_zh-CN.png)


结合以上说明，构造一个复杂的查询条件，如下图所示。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231260449233_zh-CN.png)

而实际对应的DSL如下图所示。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231260449234_zh-CN.png)

可以看出通过使用BSearch-QueryBuilder插件，可以极大地降低Elasticsearch查询的难度。

