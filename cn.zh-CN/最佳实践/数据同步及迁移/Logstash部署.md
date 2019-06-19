# Logstash部署 {#concept_kbr_tbt_zgb .concept}

Logstash是一个开源的数据收集引擎，具有实时传输数据的能力。它可以统一过滤来自不同源的数据，并按照您制定的规范将过滤的数据输出到目标源中。本文档为您介绍在ECS上部署Logstash的方法，并通过一个简单的示例为您演示Logstash的使用步骤。

## 概述 {#section_w7z_xf1_syk .section}

本文档为您介绍了[部署安装Logstash](#section_y1z_seo_6ia)、[使用Logstash同步增量数据](#section_y35_ddt_zgb)以及[监控Logstash节点](#section_fxn_usm_sjy)的方法，并在最后给出了操作过程中的[常见问题](#section_cdf_d2t_zgb)。

## 准备工作 {#section_lbm_5ct_zgb .section}

1.  购买阿里云Elasticsearch以及能够同时访问自建集群和阿里云Elasticsearch的ECS实例（已符合条件的ECS不需要重复购买）。

    您可以购买经典网络的ECS实例，前提是该ECS实例能够通过[经典网络](../../../../cn.zh-CN/常见问题/经典网络问题.md#)访问VPC内的阿里云Elasticsearch服务。

2.  安装JDK，要求JDK版本为1.8及以上版本。

## 部署安装Logstash {#section_y1z_seo_6ia .section}

1.  下载5.5.3版本的Logstash。

    在[Elastic官网](https://www.elastic.co/downloads/past-releases)页面中，下载与ElasticSearch版本一致的Logstash（建议下载5.5.3版本）。

2.  对下载的Logstash压缩包进行解压缩。

    ``` {#codeblock_utf_ab5_i08}
    tar -xzvf logstash-5.5.3.tar.gz
    ```

    **说明：** ElasticSearch从5.x版本之后，会对配置文件进行严格校验。


## 使用Logstash同步增量数据 {#section_y35_ddt_zgb .section}

1.  创建数据接入的用户名和密码。
    1.  创建角色。

        在您购买的ECS的命令行界面，执行以下命令，创建角色。

        ``` {#codeblock_2g5_d8a_yuo}
        curl -XPOST -H "Content-Type: application/json" -u elastic:es-password http://***instanceId***.elasticsearch.aliyuncs.com:9200/_xpack/security/role/***role-name*** -d '{"cluster": ["manage_index_templates", "monitor"],"indices": [{"names": [ "logstash-*" ], "privileges":["write","delete","create_index"]}]}'
        ```

        -   `es-password`：阿里云Elasticsearch实例的密码，即您登录Kibana控制台的密码。
        -   `***instanceId***`：阿里云Elasticsearch实例的ID，可在实例的基本信息页面获取。
        -   `***role-name***`：您想使用的角色名称。
        **说明：** 

        -   Logstash默认的索引名称以`logstash-当前日期`命名，所以在添加用户角色的时候，需要有对`logstash-*`索引开放读写权限。
        -   您也可以在Kibana控制台中创建角色。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134317/156093766940177_zh-CN.png)

    2.  创建用户。

        在您购买的ECS的命令行界面，执行以下命令，创建用户。

        ``` {#codeblock_32k_x7f_o0n}
        curl -XPOST -H "Content-Type: application/json" -u elastic:es-password http://***instanceId***.elasticsearch.aliyuncs.com:9200/_xpack/security/user/***user-name*** -d '{"password" : "***logstash-password***","roles" : ["***role-name***"],"full_name" : "***your full name***"}'
        ```

        -   `es-password`：阿里云Elasticsearch实例的密码，即您登录Kibana控制台的密码。
        -   `***instanceId***`：阿里云Elasticsearch实例的ID，可在实例的基本信息页面获取。
        -   `***user-name***`：您想创建的数据接入的用户名。
        -   `***logstash-password***`：您创建的数据接入用户的密码。
        -   `***role-name***`：您[上一步](#)创建的角色的名称。
        -   `***your full name***`：当前用户名的全名描述。
        **说明：** 

        您也可以在Kibana控制台中创建用户。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134317/156093766940178_zh-CN.png)

        上图中的**Roles**需要选择您上一步创建的角色名称。

2.  编写conf文件。

    在您购买的ECS中创建`test.conf`文件，并参考以下内容进行配置。

    ``` {#codeblock_fv7_p3r_1rz}
    input {
        file {
            path => "/your/file/path/xxx"
            }
    }
    filter {
    }
    output {
      elasticsearch {
        hosts => ["http://***instanceId***.elasticsearch.aliyuncs.com:9200"]
        user => "***user-name***"
        password => "***logstash-password***"
      }
    }
    ```

    **说明：** 

    Logstash提供了丰富的input、filter、output插件，只需要简单的配置就可是实现数据的流转，详情请参见官方[配置文件结构](https://www.elastic.co/guide/en/logstash/5.5/configuration-file-structure.html)文档。

    -   `***instanceId***`：阿里云Elasticsearch实例的ID，可在实例的基本信息页面获取。
    -   `***user-name***`：您[上一步](#)中创建的数据接入的用户名。
    -   `***logstash-password***`：您[上一步](#)中创建的数据接入用户的密码。

        **说明：** 用户名和密码需要使用英文引号，防止特殊字符在启动logstash时报错。

3.  执行logstash命令。

    按照[上一步](#)中配置的conf文件，执行logstash命令。

    ``` {#codeblock_aya_kv2_1ga}
    bin/logstash -f path/to/your/test.conf
    ```

    命令执行成功后，系统会自动通过Logstash获取`file`中的变化，并提交到Elasticsearch集群。只要监控的`file`文件有新增内容，Logstash就会自动索引到Elasticsearch集群中。


## 监控Logstash节点 {#section_fxn_usm_sjy .section}

您可以通过以下步骤监控Logstash节点，并收集监控日志。

1.  为Logstash安装X-Pack插件。

    单击[此处](https://artifacts.elastic.co/downloads/packs/x-pack/x-pack-5.5.3.zip)下载X-Pack插件，完成后执行以下命令对X-Pack进行部署安装。

    ``` {#codeblock_45m_fom_l11}
    bin/logstash-plugin install
            file:///path/to/file/x-pack-5.5.3.zip
    ```

2.  创建Logstash监控用户。

    阿里云Elasticsearch集群默认会禁掉`logstash_system`用户，因此您需要创建一个角色为`logstash_system`的用户名（用户名不可以配置为`logstash_system`）。本文档以`logstash_system_monitor`用户名为例，为您讲解以下两种方式创建用户的方法。

    -   通过命令行方式添加用户。

        ``` {#codeblock_5m3_jpg_0bd}
        curl -u elastic:es-password -XPOST http://***instanceId***.elasticsearch.aliyuncs.com:9200/_xpack/security/user/logstash_system_monitor -d '{"password" : "***logstash-monitor-password***","roles" : ["logstash_system"],"full_name" : "your full name"}'
        ```

        -   `es-password`：阿里云Elasticsearch实例的密码，即您登录Kibana控制台的密码。
        -   `***instanceId***`：阿里云Elasticsearch实例的ID，可在实例的基本信息页面获取。
        -   `***logstash-monitor-password***`：您创建的`logstash_system_monitor`用户的密码。
    -   通过Kibana控制台添加监控用户。
        1.  进入Kibana控制台，单击**Management** \> **Users**。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134317/156093766940183_zh-CN.png)

        2.  在Users页面，单击**Create user**。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134317/156093767040184_zh-CN.png)

        3.  输入下图所示的配置信息，单击**Save**。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134317/156093767040185_zh-CN.png)


## 常见问题 {#section_cdf_d2t_zgb .section}

-   更改集群**自动创建索引**配置。

    阿里云Elasticsearch为了保证用户操作数据的安全性，默认把**自动创建索引**的配置设置为不允许。

    Logstash在上传数据的时候，使用的是提交数据的方式创建索引，而不是使用create index API的方式。所以在使用Logstash上传数据之前，需要首先把集群的**自动创建索引**设置为允许。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134317/156093767040179_zh-CN.png)

    **说明：** 修改配置并确认后，阿里云Elasticsearch会自动重启，为保证您的业务不受影响，请谨慎操作。

-   创建索引时提示没有权限。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134317/156093767140180_zh-CN.png)

    请检查您创建的接入数据的用户拥有的角色，是否具有write、delete、create\_index权限。

-   系统提示内存不足。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134317/156093767140181_zh-CN.png)

    Logstash默认配置的是1GB的内存，如果您申请的ECS内存不足，可以修改config/jvm.options中的内存配置，适当调小Logstash内存的使用。

-   配置test.conf时，用户名和密码没有添加引号。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134317/156093767140182_zh-CN.png)

    如果在配置您的任务文件时（上文提到的test.conf文件），用户名和密码中有特殊字符但是又没有用引号括起来，就会出现上述的错误信息。


