# Build a visualized O&M system with Beats {#concept_kcx_4hm_zgb .concept}

## Background {#section_irt_33m_zgb .section}

Beats is a platform for single-purpose data shippers. After you install Beats, the lightweight Beats agents send data from your instances to target objects, such as Logstash or Elasticsearch.

As an agent of Beats and a lightweight shipper, Metricbeat is designed to collect metrics from your systems and services, and then send the metrics to target objects, such as Elasticsearch. Metricbeat is a lightweight method to send system and service statistics from CPUs to memory, Redis to NGINX, and much more.

This topic describes how to use Metricbeat to collect metrics from a MacBook, send the metrics to an Alibaba Cloud Elasticsearch instance, and generate a corresponding dashboard in Kibana.

**Note:** The procedures to collect metrics from a computer that runs a Linux or Windows system and to send the metrics to an Alibaba Cloud Elasticserach instance are similar.

1.  Purchase and configure an Alibaba Cloud Elasticsearch instance

    If you do not have an Alibaba Cloud Elasticsearch instance, you must activate Alibaba Cloud Elasticsearch and create an instance [Prerequisites](../../../../../reseller.en-US/Quick Start/Prerequisites.md#). You can then send the data collected from the MacBook to the Alibaba Cloud Elasticsearch instance through the internal or public IP address of the instance.

    **Note:** 

    -   If you access the Alibaba Cloud Elasticsearch instance through its public IP address, you must switch on Public Address and configure a **public IP address whitelist** on the Security page.

    -   If you access the Alibaba Cloud Elasticsearch instance through its internal IP address, you must create an Alibaba Cloud Elastic Compute Service \(ECS\) instance in the same **VPC** and **region** as the Alibaba Cloud Elasticsearch instance to manage access to the Elasticsearch.

    1.  Log on to the Alibaba Cloud Elasticsearch console, click the instance name or ID, and then click Security in the left-side navigation pane. On the Security page, switch on Public Address.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134309/155496639940014_en-US.png)

    2.  Add the public IP address of the MacBook to the whitelist.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134309/155496640040015_en-US.png)

**Note:** If you use a public network, add the IP address of the jump server that controls outbound network traffic of the public network to the whitelist. If you cannot obtain the IP address of the jump server, add 0.0.0.0/1,128.0.0.0/1 to the whitelist to allow a certain part of IP addresses. **This setting exposes the Alibaba Cloud Elasticsearch instance to the public network. Evaluate the risks and proceed with caution**.

    3.  After you complete the configuration, click Basic Information in the left-side navigation pane and copy the public IP address of the Alibaba Cloud Elaticsearch instance.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134309/155496640040016_en-US.png)

    4.  Modify the YML configuration. On the YML Configurations page, enable **Create Index Automatically**. By default, this feature is disabled. This operation restarts the Elasticsearch instance and takes some time to take effect.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134309/155496640040017_en-US.png)

2.  Download and configure Metricbeat
    -   [Metricbeat installation package for Mac operating systems](https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-6.3.2-darwin-x86_64.tar.gz?spm=a2c4e.11153940.blogcont618611.15.4bb4639amOtBZb&file=metricbeat-6.3.2-darwin-x86_64.tar.gz).
    -   [Metricbeat installation package for 32-bit Linux operating systems](https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-6.3.2-linux-x86.tar.gz?spm=a2c4e.11153940.blogcont618611.16.4bb4639amOtBZb&file=metricbeat-6.3.2-linux-x86.tar.gz).
    -   [Metricbeat installation package for 64-bit Linux operating systems](https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-6.3.2-linux-x86_64.tar.gz?spm=a2c4e.11153940.blogcont618611.17.4bb4639amOtBZb&file=metricbeat-6.3.2-linux-x86_64.tar.gz).
    -   [Metricbeat installation package for 32-bit Windows operating systems](https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-6.3.2-windows-x86.zip?spm=a2c4e.11153940.blogcont618611.18.4bb4639amOtBZb&file=metricbeat-6.3.2-windows-x86.zip).
    -   [Metricbeat installation package for 64-bit Windows operating systems](https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-6.3.2-windows-x86_64.zip?spm=a2c4e.11153940.blogcont618611.19.4bb4639amOtBZb&file=metricbeat-6.3.2-windows-x86_64.zip).
    1.  Download, unzip, and open the Metricbeat file.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134309/155496640040018_en-US.png)

    2.  Open and edit the **Elasticsearch output** section of the metricbeat.yml file. You need to uncomment the corresponding content.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134309/155496640040019_en-US.png)

        **Note:** 

        Alibaba Cloud Elasticsearch provides the following access control information:

        -   hosts: the public or internal IP address of the Alibaba Cloud Elasticsearch instance. This example uses the public IP address.
        -   protocol: set to http.
        -   username: the default username is **elastic**.
        -   password: the password that is used to log on to Alibaba Cloud Elasticsearch.
3.  Activate Metricbeat

    Run the following command to activate and use Metricbeat to send data to the Alibaba Cloud Elasticsearch instance.

    ```
    ./metricbeat -e -c metricbeat.yml
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134309/155496640040020_en-US.png)

4.  View the dashboard in Kibana

    Click Kibana Console in the upper-right corner in the Alibaba Cloud Elasticsearch console. You will be directed to the Dashboard page, as shown in the following figure:

    **Note:** 

    If you have not created an **index pattern** in the Kibana console, the corresponding information may not be displayed on the dashboard. To resolve this issue, create an index pattern and view the information on the Dashboard page again.

    1.  List of metrics.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134309/155496640040021_en-US.png)

    2.  CPU metrics.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134309/155496640040022_en-US.png)

        **Note:** You can schedule the system to refresh data every five seconds and generate reports, and configure a webhook to send alerts when an exception occurs.


