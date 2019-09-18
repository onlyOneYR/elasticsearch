# 访问阿里云Elasticsearch实例 {#concept_wmx_cmk_zgb .concept}

在使用阿里云Elasticsearch（简称ES）服务前，您需要确保能够访问ES实例。本文档为您介绍访问阿里云Elasticsearch实例的方法。

## 通过Kibana控制台访问 {#section_s9w_qyv_zh1 .section}

阿里云Elasticsearch实例提供Kibana控制台，为您的业务提供扩展的可能性。Kibana控制台作为Elastic生态系统的组成部分，支持无缝衔接Elasticsearch服务，可以让您实时了解阿里云Elasticsearch实例的运行状态并进行管理。

1.  [创建阿里云Elasticsearch实例](cn.zh-CN/快速入门/创建实例/创建阿里云Elasticsearch实例.md#)。

    **说明：** 

    在登录Kibana控制台前，请确保**Kibana公网访问**为**开启**状态，并且**Kibana访问白名单**中包含您需要登录Kibana控制台的机器IP。可在[Kibana访问配置](../../../../cn.zh-CN/实例/可视化控制/Kibana/访问配置.md#)页面进行查验。

2.  单击**实例ID** \> **可视化控制**。
3.  在可视化控制页面，单击**Kibana**模块中的**进入控制台**。
4.  在登录页面，输入用户名和密码，单击**登录**。

    用户名为**elastic**，密码为您创建实例时设置的密码。

5.  在Kibana控制台中，单击左侧导航栏的**开发工具**，在**Console**中使用如下命令访问ES实例。

    ![Kibana开发工具访问ES](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134284/156878734358786_zh-CN.png)

    ``` {#codeblock_jyk_ick_c6j}
    GET/
    ```

    访问成功后，结果如下：

    ``` {#codeblock_5nb_udk_59n}
    {
      "name" : "QxxxxIp",
      "cluster_name" : "es-cn-vxxxxxxxxxxmedp",
      "cluster_uuid" : "Xxxxxxxxxxxxxxw",
      "version" : {
        "number" : "6.7.0",
        "build_flavor" : "default",
        "build_type" : "tar",
        "build_hash" : "8453f77",
        "build_date" : "2019-03-21T15:32:29.844721Z",
        "build_snapshot" : false,
        "lucene_version" : "7.7.0",
        "minimum_wire_compatibility_version" : "5.6.0",
        "minimum_index_compatibility_version" : "5.0.0"
      },
      "tagline" : "You Know, for Search"
    }
    ```


## 通过阿里云ECS访问 {#section_ft6_4i2_ypt .section}

在通过阿里云ECS访问阿里云ES实例时，您需要首先创建一个与ES处于同一区域、同一可用区，同一VPC下的ECS实例，详情请参见[使用向导创建实例](../../../../cn.zh-CN/实例/创建实例/使用向导创建实例.md#)。

**说明：** 您也可以使用您已经创建的ECS实例，但需要确保与ES实例在相同区域、相同可用区和相同VPC下。如果ECS处于经典网络下，期望访问专有网络（VPC）中的ES，请先参考[经典网络问题](../../../../cn.zh-CN/常见问题/经典网络问题.md#)进行相关配置。

1.  在本地通过SSH登录ECS实例。
2.  使用如下的curl命令访问阿里云ES实例。

    **说明：** 如果系统提示curl command not found，您需要首先使用yum install curl，在ECS中安装curl。

    ``` {#codeblock_s9p_3ex_gri}
    curl -u <username>:<password> http://<host>:<port>
    ```

    |变量名|说明|
    |---|--|
    |<username\>|阿里云ES实例的访问账号，建议通过非**elastic**账号访问。 **说明：** 

    -   支持通过**elastic**账号访问，但因为在修改**elastic**账号对应密码后需要一些时间来生效，在密码生效期间会影响服务访问，因此不建议通过**elastic**来访问。
    -   若您创建的阿里云ES实例版本包含with\_X-Pack信息，则访问该阿里云ES实例时，必须指定用户名和密码。
 |
    |<password\>|阿里云ES实例的密码，为您在创建ES实例时设置的密码，或初始化Kibana时指定的密码。|
    |<host\>|阿里云ES实例的**内网地址**，可在实例的基本信息页面获取。|
    |<port\>|阿里云ES实例的端口，一般为**9200**，可在实例的基本信息页面获取。|

    访问示例：

    ``` {#codeblock_1fb_n90_10l}
    curl -u elastic:es_password http://es-cn-vxxxxxxxxxxxxmedp.elasticsearch.aliyuncs.com:9200
    ```

    访问成功后，返回如下结果：

    ![使用ECS访问ES结果示例](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134284/156878734358858_zh-CN.png)

    ``` {#codeblock_up0_drz_men}
    {
      "name" : "QxxxxIp",
      "cluster_name" : "es-cn-vxxxxxxxxxxmedp",
      "cluster_uuid" : "Xxxxxxxxxxxxxxw",
      "version" : {
        "number" : "6.7.0",
        "build_flavor" : "default",
        "build_type" : "tar",
        "build_hash" : "8453f77",
        "build_date" : "2019-03-21T15:32:29.844721Z",
        "build_snapshot" : false,
        "lucene_version" : "7.7.0",
        "minimum_wire_compatibility_version" : "5.6.0",
        "minimum_index_compatibility_version" : "5.0.0"
      },
      "tagline" : "You Know, for Search"
    }
    ```


## 通过ES客户端访问 {#section_k8c_jmn_9ty .section}

建议您使用Java High Level Rest Client，详情请参见[使用Java API操作阿里云Elasticsearch集群](../../../../cn.zh-CN/最佳实践/使用Java Rest Client调用Document API最佳实践/概述.md#)。

