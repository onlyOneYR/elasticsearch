# logstash部署 {#concept_kbr_tbt_zgb .concept}

## 依赖环境准备 {#section_lbm_5ct_zgb .section}

1.  购买阿里云ES 以及 能够同时访问自建集群和阿里云ES的ECS实例（已符合条件的ECS不需要重复购买），并准备1.8及以上版本的JDK。

    可以是经典网络的ECS实例，前提是该ECS实例能够通过 [经典网络问题](../../../../../intl.zh-CN/常见问题/经典网络问题.md#) 访问VPC内的阿里云ES服务。

2.  下载5.5.3版本的 logstash。

    在 [elastic官网](https://www.elastic.co/downloads/past-releases) 页面中，找到与ElasticSearch版本一致的 logstash 下载（建议下载5.5.3版）。

3.  对下载的 logstash 压缩包进行解压缩。

    ```
    tar -xzvf logstash-5.5.3.tar.gz
    # ElasticSearch从5.x版本之后，进行了配置文件的严格校验。
    ```


## 测试用例 {#section_y35_ddt_zgb .section}

1.  创建数据接入账号密码
    -   创建角色

        ```
        curl -XPOST -H "Content-Type: application/json" -u elastic:es-password http://***instanceId***.elasticsearch.aliyuncs.com:9200/_xpack/security/role/***role-name*** -d '{"cluster": ["manage_index_templates", "monitor"],"indices": [{"names": [ "logstash-*" ], "privileges":["write","delete","create_index"]}]}'
        # es-password 是您登录kibana的密码
        # ***instanceId*** 是您的阿里云ES实例id
        # ***role-name*** 您想使用的角色名称
        # logstash默认的索引名称以logstash-当前日期命名，所以在添加用户角色的时候，需要有对logstash-*索引读写权限。
        ```

    -   创建用户

        ```
        curl -XPOST -H "Content-Type: application/json" -u elastic:es-password http://***instanceId***.elasticsearch.aliyuncs.com:9200/_xpack/security/user/***user-name*** -d '{"password" : "***logstash-password***","roles" : ["***role-name***"],"full_name" : "***your full name***"}'
        # es-password 是您登录kibana的密码
        # ***instanceId*** 是您的阿里云ES实例id
        # ***user-name*** 是您想创建的数据接入用户名
        # ***logstash-password*** 是您创建的数据接入用户的密码
        # ***role-name*** 是您之前创建的角色名称
        # ***your full name*** 为当前用户名设置一个全名描述
        ```

        **说明：** 以上创建角色和用户，同样可以在 kibana 页面中进行配置。

    -   添加角色

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134317/155358863540177_zh-CN.png)

    -   添加用户

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134317/155358863540178_zh-CN.png)

2.  编写conf文件

    详情请参见官方 [配置文件结构](https://www.elastic.co/guide/en/logstash/5.5/configuration-file-structure.html) 文档。

    **样例**

    在 ECS 中创建 `test.conf` 文件，添加以下配置：

    ```
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
    # ***instanceId*** 是您的阿里云ES实例id
    # ***user-name*** 是您想创建的数据接入用户名
    # ***logstash-password*** 是您创建的数据接入用户的密码
    # 用户名和密码需要用英文引号引起来防止特殊字符在启动logstash时报错
    ```

    **执行**

    按照配置的conf文件，执行logstash：

    ```
    bin/logstash -f path/to/your/test.conf
    # 在logstash提供了丰富的 input、filter、output 插件，只需要简单的配置就可是实现数据的流转。
    # 该例子展示了通过 logstash 获取 file 中的变化，提交到 elasticsearch 集群。只要监控的 file 文件有新增内容，logstash 就会自动的索引到 elasticsearch 集群中。
    ```


## 常见问题解决 {#section_cdf_d2t_zgb .section}

**更改集群自动创建索引配置**

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134317/155358863540179_zh-CN.png)

阿里云Elasticsearch为了保证用户操作数据的时候的安全性，默认把自动创建索引的配置设置为不允许。

logstash在上传数据的时候是采用的提交数据创建索引的方式，并不是采用的create index的 API 创建的索引。所以在使用 logstash 上传数据的时候，需要把集群的自动创建索引的配置先设置为允许。

**说明：** 修改该配置并确认后，阿里云ES集群会自动重启。

**没有创建索引没有权限**

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134317/155358863540180_zh-CN.png)

核对一下自己创建的接入数据的用户拥有的角色，是否具有write、delete、create\_index这几个的权限。

**内存不足**

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134317/155358863540181_zh-CN.png)

logstash 默认配置的是**1g**内存，如果您申请的 ECS 内存不足，可以适当调小 logstash 内存使用。修改 config/jvm.options 中的内存配置。

**配置test.conf时用户名密码没有添加引号**

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134317/155358863540182_zh-CN.png)

如果在配置您的任务文件时，即上文提到的test.conf文件，用户名和密码中有特殊字符但是又没有用引号括起来，就会出现上述的错误信息。

## 补充说明 {#section_ivq_n2t_zgb .section}

如果希望可以监控 logstash 节点，并收集监控日志：

-   需要您在安装 logstash 的时候同时为 logstash 安装 X-Pack 的插件，详情请参见[下载地址](https://artifacts.elastic.co/downloads/packs/x-pack/x-pack-5.5.3.zip) 。
-   在完成下载后，需要对X-Pack进行部署安装。
-   ```
bin/logstash-plugin install
        file:///path/to/file/x-pack-5.5.3.zip
```

-   添加 logstash 监控用户，阿里云ElasticSearch集群默认禁掉 **logstash\_system** 用户，需要创建一个以角色为 **logstash\_system** 的用户名（用户名不可为 **logstash\_system**）。可以根据自己的意愿命名，本文以用户**logstash\_system\_monitor**为例，推荐以下两种方式创建用户。

-   通过 kibana 管理模块添加监控用户
    1.  登录 kibana 管理页面，参考以下图片操作。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134317/155358863540183_zh-CN.png)

    2.  点击 **Create user** 功能按钮。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134317/155358863640184_zh-CN.png)

    3.  输入需要的信息保存提交。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134317/155358863640185_zh-CN.png)

-   通过指令添加用户

    ```
    curl -u elastic:es-password -XPOST http://***instanceId***.elasticsearch.aliyuncs.com:9200/_xpack/security/user/logstash_system_monitor -d '{"password" : "***logstash-monitor-password***","roles" : ["logstash_system"],"full_name" : "your full name"}'
    # es-password 是您登录kibana的密码
    # ***instanceId*** 是您的阿里云ES实例id
    # ***logstash-monitor-password*** 是您创建的 logstash_system_monitor 的密码
    ```


