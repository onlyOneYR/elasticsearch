# Elasticsearch功能配置 {#concept_878575 .concept}

本文档为您介绍Elasticsearch一些基本功能的配置方法，包括ECS访问配置、VPC私网和公网地址访问白名单配置以及监控报警配置。

## ECS访问配置 {#section_1pd_nbq_34u .section}

阿里云Elasticsearch支持以下几种访问方式。

-   登录阿里云Elasticsearch控制台集成的[Kibana控制台](../../../../intl.zh-CN/用户指南/可视化控制/Kibana/登录Kibana控制台.md#)，在**Dev Tools**页面进行访问。
-   通过阿里云Elasticsearch对应VPC下的阿里云ECS（Elasticsearch与ECS区域相同），部署用户程序或安装curl命令进行访问。
-   通过阿里云Elasticsearch对应的公网地址进行访问。
-   参考elastic官方提供的其它[Elasticsearch client](https://www.elastic.co/guide/en/elasticsearch/client/index.html)进行访问。

基于ECS访问（可选）

**说明：** 

-   若您的ECS在经典网络中，则需要通过配置Classiclink进行访问，详情请参见[经典网络问题](../../../../intl.zh-CN/常见问题/经典网络问题.md#)。
-   Classiclink访问方式，只支持从经典网络单向访问VPC网络，不支持双向访问。

如果在阿里云Elasticsearch对应的VPC下，已拥有阿里云ECS（Elasticsearch与ECS区域相同），可以不用重新购买阿里云ECS，也可以使用该ECS作为客户端，部署用户程序或安装curl命令进行搜索。

如果您还没有符合条件的阿里云ECS，则需要首先购买一个与您的阿里云Elasticsearch实例在同一VPC下的阿里云ECS，购买方式请参见[使用向导创建实例](../../../../intl.zh-CN/实例/创建实例/使用向导创建实例.md#)。

**说明：** 

-   在阿里云Elasticsearch对应的VPC下购买的ECS产品，主要是用于部署用户程序，作为客户端访问。
-   若阿里云Elasticsearch对应VPC下没有ECS，也可以使用Kibana中的Console进行搜索测试，或重新在阿里云Elasticsearch对应的VPC下，购买一个ECS产品。

基于ECS curl访问（可选） 

1.  在本地通过ssh登录与Elasticsearch集群处于同一个VPC的ECS，并在机器中安装curl。
2.  将curl添加到机器的环境变量中，通过curl远程访问阿里云Elasticsearch服务。

## VPC私网和公网访问配置 {#section_g2p_g4b_oa6 .section}

阿里云的Elasticsearch提供了商业版的X-pack插件，在使用阿里云Elasticsearch实例的内网或公网域名地址访问服务时，增加了进一步的安全控制。用户可以通过配置VPC私网或公网地址访问白名单来控制对阿里云Elasticsearch实例的访问。详情请参见[安全配置](../../../../intl.zh-CN/用户指南/实例管理/安全配置.md#)。

## 监控报警配置 {#section_jqu_p91_0vz .section}

阿里云Elasticsearch已支持对实例进行如下监控，并允许通过短信报警。可根据您的需求，自定义报警阈值。详情请参见[ES云监控报警](../../../../intl.zh-CN/监控报警/阿里云Elasticsearch云监控报警.md)进行配置。

-   集群状态
-   集群查询QPS\(Count/Second\)
-   集群写入QPS\(Count/Second\)
-   节点CPU使用率\(%\)
-   节点磁盘使用率\(%\)
-   节点HeapMemory使用率\(%\)
-   节点load\_1m

