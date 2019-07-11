# XPack Watcher {#concept_xgy_rzl_zgb .concept}

本文档为您介绍XPack Watcher的配置方法。您可以通过添加XPack Watcher实现当满足某些条件时执行某些操作，比如当`logs`索引中出现`error`日志时，自动发送报警邮件或钉钉消息。可以简单的理解为Watcher是一个基于Elasticsearch实现的监控报警服务。

**说明：** XPack Watcher功能主要适用于单可用区的阿里云Elasticsearch实例，不支持跨多可用区的阿里云Elasticsearch实例。

## 功能介绍 {#section_ez5_pcm_zgb .section}

XPack Watcher功能主要由Trigger、Input、Condition、Actions组成。

-   Trigger 

    确定何时检查，在配置Watcher时必须设置。支持丰富的时间计划方式，详情请参见[Schedule Trigger](https://www.elastic.co/guide/en/x-pack/5.5/trigger-schedule.html)。

-   Input 

    可以理解为您需要对监控的索引执行的筛选条件，详情请参见[Inputs](https://www.elastic.co/guide/en/x-pack/5.5/input.html)。

-   Condition 

    执行Actions的条件。

-   Actions 

    当条件发生时，执行的具体操作。


## 配置方式 {#section_i1m_tcm_zgb .section}

阿里云Elasticsearch的Watcher功能不支持直接与公网进行通讯，需要基于阿里云Elasticsearch实例的内网地址来进行通讯（专有网络VPC环境）。如果您需要使用XPack Watcher，还需要购买一台能同时访问公网和阿里云Elasticsearch实例的阿里云ECS实例，作为代理去执行Actions。

以配置Webhook Action为例（Webhook采用钉钉群机器人）。

1.  [购买阿里云ECS实例](../../../../cn.zh-CN/个人版快速入门/创建ECS实例.md#)。

    购买的ECS要与阿里云Elasticsearch实例在同一个区域和VPC下，并且需要能够访问公网。

    **说明：** 

    -   阿里云ECS实例与阿里云Elasticsearch实例的VPC必须相同。
    -   阿里云ECS实例需要能访问公网。
2.  配置安全组。
    1.  在阿里云ECS控制台的实例列表页面，单击对应实例右方的**更多** \> **网络和安全组** \> **安全组配置**。
    2.  在**安全组列表**右侧的**操作**栏下，单击**配置规则**。
    3.  在**安全组规则**页面，单击**添加安全组规则**。
    4.  填写相关参数，单击**确定**，即可完成配置。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134322/156283887649922_zh-CN.png)

        -   **规则方向**选择**入方向**。
        -   **授权策略**为默认的**允许**。
        -   **协议类型**选择**自定义TCP**。
        -   **优先级**通常默认即可。
        -   **端口范围**填写您常用的端口（配置Nginx时需要用到，本文以8080为例）。
        -   **授权类型**选择**IPv4地址段访问**。
        -   **授权对象**添加您购买的阿里云Elasticsearch实例所有节点的IP地址。

            **说明：** 您可以通过以下方式获取阿里云Elasticsearch实例的IP地址列表。

            登录您购买的阿里云Elasticsearch实例的Kibana控制台，单击左侧菜单栏的**Monitoring**，再单击**Nodes**。

3.  配置Nginx代理。

    详情请参见[Nginx安装配置](http://www.runoob.com/linux/nginx-install-setup.html)。

    1.  修改Nginx配置文件，参考以下配置替换**Nginx安装配置**中描述的`server`部分的配置。

        ``` {#codeblock_pyg_tx7_osr}
        server
          {
            listen 8080;#监听端口
            server_name localhost;#域名
            index index.html index.htm index.php;
            root /usr/local/webserver/nginx/html;#站点目录
              location ~ .*\.(php|php5)?$
            {
              #fastcgi_pass unix:/tmp/php-cgi.sock;
              fastcgi_pass 127.0.0.1:9000;
              fastcgi_index index.php;
              include fastcgi.conf;
            }
            location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|ico)$
            {
              expires 30d;
          # access_log off;
            }
            location / {
              proxy_pass 钉钉机器人webhook地址，直接拷贝过来粘贴就行;
            }
            location ~ .*\.(js|css)?$
            {
              expires 15d;
           # access_log off;
            }
            access_log off;
          }
        }
        ```

    2.  配置修改完成后，加载新配置文件并重启Nginx。

        ``` {#codeblock_fjz_qbf_2ht}
        /usr/local/webserver/nginx/sbin/nginx -s reload            # 重新载入配置文件
        /usr/local/webserver/nginx/sbin/nginx -s reopen            # 重启Nginx
        ```

    **说明：** 您可以通过以下方式获取钉钉群机器人的webhook地址。

    创建一个钉钉报警接收群，在群的右上角找到**群机器人**，然后添加一个自定义通过webhook接入的机器人，并获取群机器人的webhook地址。详情请参见[自定义机器人](https://open-doc.dingtalk.com/docs/doc.htm?spm=a219a.7629140.0.0.karFPe&treeId=257&articleId=105735&docType=1)。

4.  设置报警。

    登录阿里云Elasticsearch实例的Kibana控制台，单击左侧菜单栏的**Dev Tools**，在**Console**中使用API创建一个报警文档。

    下文以创建`log_error_watch`为例，每隔`10s`查询`logs`索引中是否出现`error`日志，如果出现`0`次以上则触发报警。

    ``` {#codeblock_vpp_brv_0zd}
    PUT _xpack/watcher/watch/log_error_watch
    {
      "trigger": {
        "schedule": {
          "interval": "10s"
        }
      },
      "input": {
        "search": {
          "request": {
            "indices": ["logs"],
            "body": {
              "query": {
                "match": {
                  "message": "error"
                }
              }
            }
          }
        }
      },
      "condition": {
        "compare": {
          "ctx.payload.hits.total": {
            "gt": 0
          }
        }
      },
      "actions" : {
      "test_issue" : {
        "webhook" : {
          "method" : "POST",
          "url" : "http://您的ECS内网IP:8080",
          "body" : "{\"msgtype\": \"text\", \"text\": { \"content\": \"error 日志出现了，请尽快处理\"}}"
        }
      }
    }
    }
    ```

    **说明：** 

    上述`actions`中配置的`url`必须是您购买的与阿里云Elasticsearch实例相同区域和VPC的阿里云ECS实例的内网IP地址，并且已经按照上述方式进行了安全组配置，否则不能进行通信。

    如果您不再需要执行报警任务，可以使用以下命令删除该报警任务。

    ``` {#codeblock_amk_1qh_9gg}
    DELETE _xpack/watcher/watch/log_error_watch
    ```


## 常见问题 {#section_el5_1fm_zgb .section}

问题：在设置报警时出现`No handler found for uri [/_xpack/watcher/watch/log_error_watch_2] and method [PUT]`异常。

解决方法：设置报警时以上异常，表示您购买的阿里云Elasticsearch实例未开启Watcher功能，可通过以下方式开启。

1.  [登录阿里云Elasticsearch控制台](https://elasticsearch.console.aliyun.com/)，单击**实例ID** \> **ES集群配置**。
2.  在**ES集群配置**页面，单击**YML文件配置**右侧的**修改配置**。
3.  在YML参数配置页面，将**开启Watcher**设置为**开启**。

    **说明：** 开启Watcher操作会触发集群重启，为保证您的业务不受影响，请确认后操作。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134322/156283887649905_zh-CN.png)

4.  勾选**该操作会重启实例，请确认后操作**，然后单击**确认**。

    重启过程约持续30分钟，请耐心等待。重启完成后，即可完成Watcher的开启。


