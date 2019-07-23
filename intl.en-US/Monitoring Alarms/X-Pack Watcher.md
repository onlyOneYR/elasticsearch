# X-Pack Watcher {#concept_xgy_rzl_zgb .concept}

This topic describes how to configure X-Pack Watcher. With X-Pack Watcher, you can use a watch to trigger specific actions. For example, you can create a watch to search the `logs` index for `errors` and then send alerts through emails or DingTalk messages. X-Pack Watcher is a monitoring and alerting service based on Elasticsearch.

**Note:** X-Pack Watcher can be applied to Alibaba Cloud Elasticsearch instances deployed in only one zone. It does not support Elasticsearch instances deployed across multiple zones.

## Features {#section_ez5_pcm_zgb .section}

X-Pack Watcher allows you to create watches. A watch consists of a Trigger, Input, Condition, and Actions.

-   Trigger 

    Determines when the watch is executed. All watches must have a trigger. X-Pack Watcher allows you to create various types of triggers. For more information, see [Schedule Trigger](https://www.elastic.co/guide/en/x-pack/5.5/trigger-schedule.html).

-   Input 

    Loads data into the payload of a watch. Inputs are used as filters to match the specified type of index data. For more information, see [Inputs](https://www.elastic.co/guide/en/x-pack/5.5/input.html).

-   Condition 

    Controls whether the actions of a watch are executed.

-   Actions 

    Determines the actions to be executed when the specified conditions are met.


## Procedure {#section_i1m_tcm_zgb .section}

X-Pack Watcher of Alibaba Cloud Elasticsearch cannot directly access the Internet. To use this feature, you must purchase an Alibaba Cloud ECS instance that can access the Internet and Alibaba Cloud Elasticsearch. The ECS instance is used as a proxy to perform actions. X-Pack Watcher uses the private network address of the ECS instance to communicate in a VPC network.

The following example shows how to use a Webhook action to connect the DingTalk Chatbot to your service.

1.  [Purchase an Alibaba Cloud ECS instance](../../../../reseller.en-US/Quick Start for Entry-Level Users/Step 2. Create an instance.md#).

    The purchased ECS instance must meet the following requirements:

    **Note:** 

    -   The ECS instance must be in the same region and VPC network as your Alibaba Cloud Elasticsearch instance.
    -   The ECS instances must have access to the Internet.
2.  Configure a security group
    1.  On the Instances page of the Alibaba Cloud ECS console, click **More** on the right side of the ECS instance, and then select **Network and Security Group** \> **Configure Security Group**.
    2.  In the **Security Groups** list, click **Add Rules** in the **Actions** column.
    3.  On the **Security Group Rules** page, click **Add Security Group Rule**.
    4.  Set the parameters, and click **OK** to complete the configuration.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134322/156387036749922_en-US.png)

        -   Set the **Rule Direction** to **Ingress**.
        -   Use the default **Action** setting: **Allow**.
        -   Set the **Protocol Type** to **Customized TCP**.
        -   Use the default **Priority** setting.
        -   Set the **Port Range** to your frequently used port. This port is required for NGINX configuration. In this example, port 8080 is specified.
        -   Set the **Authorization Type** to **IPv4 CIDR Block**.
        -   In the **Authorization Objects** field, enter the IP addresses of all nodes in your Alibaba Cloud Elasticsearch instance.

            **Note:** You can use the following method to query the IP addresses of the nodes.

            Log on to the Kibana console of the Alibaba Cloud Elasticsearch instance, click **Monitoring** in the left-side navigation pane, and then click **Nodes**.

3.  Configure an NGINX proxy.

    1.  Modify the NGINX configuration file. Reference the following configuration and then replace the `server` configuration in **Install and configure NGINX** with this configuration.

        ``` {#codeblock_pyg_tx7_osr}
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
              proxy_pass Enter the Webhook address of the DingTalk Chatbot here
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

    2.  After you complete the replacement, reload the NGINX configuration file and then restart NGINX.

        ``` {#codeblock_fjz_qbf_2ht}
        /usr/local/webserver/nginx/sbin/nginx -s reload            # Reload the NGINX configuration file
        /usr/local/webserver/nginx/sbin/nginx -s reopen            # Restart Nginx
        ```

    **Note:** You can use the following method to query the Webhook address of the DingTalk Chatbot.

    Create an alert contact group in DingTalk. Click the DingTalk group, click the More icon in the upper-right corner, click **ChatBot**, and select Custom to add a Webhook ChatBot. You can then view the Webhook address of the ChatBot.

4.  Create a watch.

    Log on to the Kibana console of your Alibaba Cloud Elasticsearch instance. In the left-side navigation pane, click **Dev Tools**, and then call the corresponding API operation to create a watch in the **Console**.

    The following example shows how to create a watch named `log_error_watch` to search the `logs` index for `errors` at an interval of `10s`. If more than `0` errors are found, an alert is triggered.

    ``` {#codeblock_vpp_brv_0zd}
    PUT _xpack/watcher/watch/log_error_watch
    {
      "trigger": {
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
          "url" : "http:// Private IP address of your ECS instance:8080",
          "body" : "{\"msgtype\": \"text\", \"text\": { \"content\": \"An error has been found. Handle the issue immediately.\"}}"
        }
      }
    }
    }
    ```

    **Note:** 

    The `url` specified in the `actions` must be the private IP address of the purchased Alibaba Cloud ECS instance that is deployed in the same region and VPC network as your Elasticsearch instance. You must also make sure that you have followed the preceding procedure to create a security group for the ECS instance. Otherwise, Watcher cannot send alerts.

    If you no longer need this watch, run the following command to delete the watch.

    ``` {#codeblock_amk_1qh_9gg}
    DELETE _xpack/watcher/watch/log_error_watch
    ```


## FAQ {#section_el5_1fm_zgb .section}

Issue: An exception occurred while configuring a watch: `No handler found for uri [/_xpack/watcher/watch/log_error_watch_2] and method [PUT]`

Solution: You have not enabled the Watcher feature for your Alibaba Cloud Elasticsearch instance. Follow these steps to enable the Watcher feature.

1.  Log on to the [Alibaba Cloud Elasticsearch console](https://elasticsearch.console.aliyun.com/), and select **Instance ID** \> **Cluster Configuration**.
2.  On the **Cluster Configuration** page, click **Modify Configuration** on the right side of **YML Configuration**.
3.  On the YML Configuration page, select **Enable** for **Watcher**.

    **Note:** After you enable Watcher, the Elasticsearch instance will be restarted. Make sure that your businesses are not adversely affected by the restart process before you confirm the operation.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134322/156387036749905_en-US.png)

4.  Select the **This operation will restart the instance. Continue?** check the box, and then click **OK**.

    It may take up to 30 minutes to restart the Elasticsearch instance. Please wait. After the Elasticsearch instance is restarted, Watcher is enabled.


