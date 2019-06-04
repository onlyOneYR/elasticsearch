# Head插件安装（Chrome） {#task_405348 .task}

本文档为您介绍Elasticsearch Head插件安装的方法。通过在Chrome浏览器中安装Head插件，您可以使用阿里云Elasticsearch实例的公网地址来访问集群服务并执行相关操作。

通过Chrome浏览器安装Head插件前，请确保您能够访问chrome.google.com域名。

-   Elasticsearch Head是第三方支持的服务/插件。
-   在公网环境下，Head插件不支持通过阿里云Elasticsearch内网地址和端口访问集群服务。

1.  在Chrome浏览器中打开插件安装链接（[https://chrome.google.com/webstore/detail/elasticsearch-head/ffmkiejjmecolpfloofpjologoblkegm](https://chrome.google.com/webstore/detail/elasticsearch-head/ffmkiejjmecolpfloofpjologoblkegm)），单击**添加至 Chrome**。![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/328056/155964615448231_zh-CN.png)


2.  在弹出的确认对话框中，单击**添加扩展程序**。![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/328056/155964615448232_zh-CN.png)

 

    系统会自动下载并安装Head插件，安装成功后，系统会弹出安装成功的对话框。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/328056/155964615448233_zh-CN.png)

3.  进入[阿里云Elasticsearch控制台](https://elasticsearch.console.aliyun.com/)，开启Elasticsearch实例的[公网地址](../intl.zh-CN/用户指南/实例管理/基本信息.md#section_sy4_5dm_zgb)，然后将访问机器的公网IP配置到[公网地址访问白名单](../intl.zh-CN/用户指南/实例管理/安全配置.md#section_ux5_yct_zgb)中。![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/328056/155964615448234_zh-CN.png)

 

    **说明：** 

    -   获取访问机器的公网IP：打开Google首页输入IP，单击**查看自己的IP地址**网页链接，进入网页后即可获取机器的公网IP。
    -   开启阿里云Elasticsearch实例的公网地址后，默认禁止所有IPv4地址的访问。
4.  单击Chrome浏览器地址栏右侧的放大镜图标，进入Elasticsearch集群连接页面。![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/328056/155964615448235_zh-CN.png)


5.  在Elasticsearch集群连接页面的地址栏中输入`http://<Elasticsearch实例的公网地址>:<端口号>/`，单击**连接**。![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/328056/155964615448238_zh-CN.png)

 您可以在Elasticsearch实例的基本信息页面获取Elasticsearch实例的公网地址和端口号，默认的端口号为9200，示例连接地址如下：

    ``` {#codeblock_ems_m1h_s38}
    http://es-cn-45xxxxxxxxx01xw6w.public.elasticsearch.aliyuncs.com:9200/
    ```

6.  在弹出的登录对话框中，输入Elasticsearch实例的访问**用户名**和**密码**（登录kibana控制台的用户名和密码），单击**登录**。![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/328056/155964615448240_zh-CN.png)

 

    **说明：** 因阿里云Elasticsearch商业版本集成了X-Pack安全访问策略，需要在访问集群服务时输入授权信息。如果没有弹出登录对话框，请先检查阿里云Elasticsearch的公网地址访问白名单中是否包含了对应访问机器的公网IP，或者尝试清除浏览器缓存后重试。

7.  登录成功后，根据您的业务进行相关操作。![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/328056/155964615548241_zh-CN.png)



