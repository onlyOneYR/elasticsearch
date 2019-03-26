# XPack Watcher {#concept_xgy_rzl_zgb .concept}

## Overview {#section_owf_jcm_zgb .section}

You can add Watcher to Elasticsearch as a monitoring and alarm service to trigger actions when certain conditions are met. For example, when log indexes contain ERROR, a watch automatically sends an alarm by email or DingTalk.

## Features {#section_ez5_pcm_zgb .section}

Watcher supports multiple features, including **Triggers**, **Inputs**, **Conditions**, and **Actions**.

**Trigger**

Triggers determine the date and time to execute watches. Triggers are required to configure watches. Watcher provides multiple types of schedule triggers. For more information, see [Schedule Trigger](https://www.elastic.co/guide/en/x-pack/5.5/trigger-schedule.html).

**Inputs**

You can use inputs to filter indexes monitored by Watcher. For more information, see [Inputs](https://www.elastic.co/guide/en/x-pack/5.5/input.html).

**Conditions**

A condition determines whether or not to execute actions.

**Actions**

Actions are executed when certain conditions are met.

## Configuration {#section_i1m_tcm_zgb .section}

Watches in Alibaba Cloud Elasticsearch cannot communicate through the public network. You can only access the internal endpoint of the instance over a VPC network. To use Watcher, you must create an Alibaba Cloud ECS instance that can access both the public network and Alibaba Cloud Elasticsearch instance. The ECS instance runs as a proxy to execute actions.

The following example shows how to configure Webhook actions. This example uses the DingTalk Chatbot.

1.  Purchase an Alibaba Cloud ECS instance

    Purchase an [Alibaba Cloud ECS instance](https://www.alibabacloud.com/product/ecs). Make sure that the ECS instance can access the public network.

    **Note:** 

    -   The Alibaba Cloud ECS instance and Elasticsearch instance must share the same VPC network.
    -   The Alibaba Cloud ECS instance must have access to the public network.
2.  Configure a security group

    Go to the instances page in the Alibaba Cloud ECS console, click **More** on the right side of the target instance, select Security Group Configuration, and then add a security group rule on the security group list page.

    -   Set the direction of the rule to Inbound.
    -   Use the default action of the authorization policy: Allow.
    -   Set the custom protocol to Custom TCP.
    -   Use the default priority setting.
    -   Configure the port range as needed. This example uses port 8080 for Nginx.
    -   Set the authorization type to CIDR.
    -   Add IP addresses of all nodes for your Alibaba Cloud Elasticsearch instance as authorization objects.
    **Note:** **Obtain an Alibaba Cloud Elasticsearch instance IP address list:**

    Log on to the Kibana console of the Elasticsearch instance that you have purchased, click Monitoring, and click Nodes to view IP addresses of all nodes for your Elasticsearch instance.

3.  Configure a Nginx proxy

    1.  Modify the Nginx configuration file. The following example shows how to configure the server settings in the **Nginx configuration** file:

        ```
        server
          {
            listen 8080;#Listening port
            server_name localhost;#Domain name
            index index.html index.htm index.php;
            root /usr/local/webserver/nginx/html;#Website directory
              location ~ . *\.(php|php5)? $
            {
              #fastcgi_pass unix:/tmp/php-cgi.sock;
              fastcgi_pass 127.0.0.1:9000;
              fastcgi_index index.php;
              include fastcgi.conf;
            }
            location ~ . *\.(gif|jpg|jpeg|png|bmp|swf|ico)$
            {
              expires 30d;
          # access_log off;
            }
            location / {
              proxy_pass Paste the Webhook address of the DingTalk Chatbot here.
            }
            location ~ . *\.(js|css)? $
            {
              expires 15d;
           # access_log off;
            }
            access_log off;
          }
        }
        ```

    2.  After you have configured the Nginx configuration file, reload the configuration file and restart Nginx.

        ```
        /usr/local/webserver/nginx/sbin/nginx -s reload # Reload the configuration file
        /usr/local/webserver/nginx/sbin/nginx -s reopen # Restart Nginx
        ```

    **Note:** Obtain the Webhook address of the DingTalk Chatbot:

    Create a DingTalk alarm reception group. Click Group Settings in the upper-right corner, select ChatBot, add a Webhook robot, and then obtain the Webhook address of the robot.

4.  Set alarms
    1.  Log on to the Kibana console of the Elasticsearch instance, and click the left-side **Dev Tools** tab. The following example shows how to create a watcher named log\_error\_watch to check whether the log indexes contain **ERROR** every 10 seconds. Once an error log entry is detected, the watcher triggers an alarm.

        ```
        PUT _xpack/watcher/watch/log_error_watch
        {
          "trigger": 2
            "schedule": {
              "interval": "10s"
            }
          },
          "inputs": [
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
              "url" : "http:// The private IP address of your ECS instance:8080",
              "body" : "{\"msgtype\": \"text\", \"text\": { \"content\": \"An error log entry has been detected. Handle the issue immediately.\"}}"
            }
          }
        }
        }
        ```

        **Note:** 

        The **URL** in the **actions** must be the internal IP address of your ECS instance that shares the same region and VPC with your Elasticsearch instance. The ECS instance must have been added to a security group that is created by following the steps in this example. Otherwise, the ECS instance cannot communicate with the Elasticsearch instance.

    2.  You can run the following command to delete a watcher.

        `DELETE _xpack/watcher/watch/log_error_watch`


## FAQs {#section_el5_1fm_zgb .section}

-   No handler has been found for URI

    The following error message indicates that the watcher feature has not been enabled for your Elasticsearch instance. You must go to the instance management page in the Alibaba Cloud Elasticsearch console, choose **Advanced Settings** \> **YML File**, and then add `xpack.watcher.enabled: true`.

    No handler found for uri \[/\_xpack/watcher/watch/log\_error\_watch\_2\] and method \[PUT\]

    **Note:** Currently, Alibaba Cloud Elasticsearch cannot periodically clear .watcher-history indexes. You must manually clear the .watcher-history indexes that you no longer need. You can schedule a task on your ECS instance to call the corresponding API operations to delete indexes.


