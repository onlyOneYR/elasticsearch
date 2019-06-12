# XPack Watcher {#concept_xgy_rzl_zgb .concept}

## 概述 {#section_owf_jcm_zgb .section}

您可以通过添加Watcher实现当满足某些条件时执行某些操作，比如当logs索引中出现ERROR日志时，自动发送报警邮件或钉钉消息，可以简单的理解为Watcher是一个基于ElasticSearch实现的监控报警服务。

## 功能介绍 {#section_ez5_pcm_zgb .section}

XPack Watcher功能主要由**Trigger**、**Input**、**Condition**、**Actions**组成。

**Trigger**

确定何时检查，在配置Watcher时必须设置。支持丰富的时间计划方式，详情请参见[Schedule Trigger](https://www.elastic.co/guide/en/x-pack/5.5/trigger-schedule.html)。

**Input**

可以理解为您需要对监控的索引执行的筛选条件，详情请参见[Inputs](https://www.elastic.co/guide/en/x-pack/5.5/input.html)。

**Condition**

执行Actions的条件。

**Actions**

当条件发生时，执行的具体操作。

## 配置方式 {#section_i1m_tcm_zgb .section}

阿里云ES的Watcher功能不支持直接与公网进行通讯，需要基于阿里云ES实例内网地址来进行通讯（专有网络VPC环境）。如果您需要使用XPack Watcher，您需要通过架设一台能同时访问公网和阿里云ES实例的阿里云ECS实例做代理去执行Actions。

以配置Webhook Action为例（Webhook采用钉钉群机器人）：

1.  购买阿里云ECS

    在阿里云官网购买一台与阿里云ES实例相同区域和VPC的[阿里云ECS实例](https://www.alibabacloud.com/product/ecs)，并且需要能够访问公网。

    **说明：** 

    -   阿里云ECS实例与阿里云ES实例的VPC必须相同。
    -   阿里云ECS实例需要能访问公网。
2.  配置安全组

    在阿里云ECS控制台实例列表界面，单击对应实例右方的**更多**连接，找到安全组配置，跳转到安全组列表界面，配置添加安全组规则。

    -   规则方向选择入方向。
    -   授权策略为默认的允许。
    -   协议类型选择自定义TCP。
    -   优先级通常默认即可。
    -   端口范围填写您常用的端口（配置Nginx时需要用到，本文以8080为例）。
    -   授权类型选择地址段访问。
    -   授权对象添加您购买的阿里云ES实例所有节点的IP地址。
    **说明：** **获取阿里云ES实例IP地址列表**

    登陆您购买的阿里云ES实例的Kibana控制台，点击左侧Tab页面中的Monitoring，再点击nodes就可以看到您购买的阿里云ES实例所有节点的IP列表。

3.  配置Nginx代理

    1.  修改nginx配置文件，参考以下配置替换**Nginx 安装配置**中描述的server部分配置。

        ```
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

    2.  参考上面描述修改完配置后，加载新配置文件重启nginx。

        ```
        /usr/local/webserver/nginx/sbin/nginx -s reload            # 重新载入配置文件
        /usr/local/webserver/nginx/sbin/nginx -s reopen            # 重启 Nginx
        ```

    **说明：** **获取钉钉群机器人webhook地址**

    组建一个钉钉报警接收群，在群的右上角找到群机器人然后添加一个自定义通过webhook接入的机器人，添加并获取群机器人的webhook地址。

4.  设置报警
    1.  登陆阿里云ES实例Kibana控制台，点击左侧的**Dev Tools** 页签。下文以创建log\_error\_watch为例，每隔10s查询logs索引中是否出现**ERROR日志**，如果出现0次以上则触发报警。

        ```
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

        上述**actions**中配置的**url**，必须是您购买的与阿里云ES实例相同区域和VPC的阿里云ECS实例的内网IP地址，并且已经按照上述方式进行了安全组配置，否则不能进行通信。

    2.  如果您想要删除该报警任务，可以参考下面。

        `DELETE _xpack/watcher/watch/log_error_watch`


## 常见问题 {#section_el5_1fm_zgb .section}

-   No handler found for uri

    如果您在设置报警时出现以下异常，表示您购买的阿里云ES实例未开启Watcher功能，需要您在阿里云ES控制台实例管理界面中的**高级配置** \> **YML文件配置**中，配置上`xpack.watcher.enabled: true`。

    No handler found for uri \[/\_xpack/watcher/watch/log\_error\_watch\_2\] and method \[PUT\]

    **说明：** 注意定期清理.watcher-history索引，阿里云ES目前没有定期清理.watcher-history索引的功能，需要根据您的具体需要定期清理.watcher-history没用的索引（可以在您购买的ECS上跑一个定时任务调用ElasticSearch删除索引的API）。


