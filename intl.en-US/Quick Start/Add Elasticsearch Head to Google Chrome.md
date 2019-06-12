# Add Elasticsearch Head to Google Chrome {#task_405348 .task}

This topic describes how to add Elasticsearch Head to Google Chrome. With Elasticsearch Head, you can use the public network address of an Alibaba Cloud Elasticsearch instance to access the instance and perform operations.

Before you add Elasticsearch Head to Google Chrome, make sure that you can access the domain chrome.google.com.

**Note:** 

-   Elasticsearch Head is a third-party extension.
-   In a public network, you cannot use the internal network address and port of an Alibaba Cloud Elasticsearch instance to access the instance with Elasticsearch Head.

1.  Enter the Elasticsearch Head link [https://chrome.google.com/webstore/detail/elasticsearch-head/ffmkiejjmecolpfloofpjologoblkegm](https://chrome.google.com/webstore/detail/elasticsearch-head/ffmkiejjmecolpfloofpjologoblkegm) into the address bar of Google Chrome, and then click **Add to Chrome**.![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/328056/155964615648231_en-US.png)


2.  Click **Add extension** in the dialog box.![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/328056/155964615648232_en-US.png)

 

    The system then downloads and installs Elasticsearch Head. After the installation is complete, the system prompts a message indicating that Elasticsearch Head has been installed.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/328056/155964615648233_en-US.png)

3.  Log on to the [Alibaba Cloud Elasticsearch console](https://elasticsearch.console.aliyun.com/), enable [public address](../DNELASTICSEARCH19100375/EN-US_TP_134295.dita#concept_sjs_cts_zgb/section_elq_yct_zgb) for your Elasticsearch instance, and add the public network IP address of your host to [public IP addresses with whitelist](../DNELASTICSEARCH19100375/EN-US_TP_134295.dita#concept_sjs_cts_zgb/section_ux5_yct_zgb).![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/328056/155964615648234_en-US.png)

 

    **Note:** 

    -   You can enter IP into the search bar on the Google homepage, and then click the What Is My IP Address link to view the public network IP address of your host.
    -   By default, the public network access function forbids all IPv4 addresses.
4.  Click the Elasticsearch Head icon on the right side of the Google Chrome address bar to open the Elasticsearch cluster connection page.![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/328056/155964615648235_en-US.png)


5.  Enter `http://Elasticsearch Instance Public Network Address:Port Number/` into the address bar, and then click **Connect**.![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/328056/155964615648238_en-US.png)

 You can log on to the Elasticsearch console and view the public network address and port number of the Elasticsearch instance on the basic information page. The default port number is 9200. The following is a sample connection address:

    ``` {#codeblock_ems_m1h_s38}
    http://es-cn-45xxxxxxxxx01xw6w.public.elasticsearch.aliyuncs.com:9200/
    ```

6.  In the Sign in dialog box, enter the **username** and **password** that are used to log on to the Kibana console of the Elasticsearch instance, and then click **Log on**.![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/328056/155964615648240_en-US.png)

 

    **Note:** The Alibaba Cloud Elasticsearch with Commercial Feature is integrated with X-Pack for security purposes. Therefore, you must enter the username and password for authentication before you can log on to the instance. If the Sign in dialog box is not displayed, verify that the public network whitelist of Alibaba Cloud Elasticsearch contains the public network IP address of your host, or clear the cache of your Web browser and then try again.

7.  After you log on to the Elasticsearch instance, you can then perform other operations.![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/328056/155964615748241_en-US.png)



