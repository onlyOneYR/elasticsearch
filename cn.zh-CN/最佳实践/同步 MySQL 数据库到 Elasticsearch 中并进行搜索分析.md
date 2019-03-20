# 同步 MySQL 数据库到 Elasticsearch 中并进行搜索分析 {#concept_mvk_jpr_zgb .concept}

阿里云上拥有丰富的云存储、云数据库产品。如果您希望针对这些产品中的数据进行分析和搜索，可以通过DataWorks的数据集成服务，将离线数据同步到Elasticsearch中，最快可达到5分钟一次。

**说明：** 做数据同步时可能会产生公网流量费用，请您知晓。

## 准备工作 {#section_r22_rpr_zgb .section}

完成离线数据的分析与搜索，需要您完成以下几步操作：

-   创建一个数据库，您可以选择使用阿里云的RDS数据库，也可以在本地服务器上自建数据库。本文档以RDS MySQL数据库为例，数据库字段及数据如下图所示。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134312/155305101540085_zh-CN.png)

-   购买一台可以与VPC内的Elasticsearch交互的ECS，这台ECS将获取数据源数据并执行写Elasticsearch数据的任务（该任务将由数据集成系统统一下发）。

-   开通DataWorks的数据集成服务，并且将ECS作为一个可以执行任务的资源，注册到数据集成服务中去。

-   配置一个数据同步的脚本，并且让其可以周期性的执行起来。

-   创建一个Elasticsearch实例，用来存储数据集成系统同步成功的数据。


## 操作步骤 {#section_cgx_spr_zgb .section}

**数据同步**

1.  [搭建IPv4专有网络](../../../../../cn.zh-CN/快速入门/搭建IPv4专有网络.md#)。

2.  进入[Elasticsearch控制台](https://elasticsearch-cn-hangzhou.console.aliyun.com/)，单击**创建**，创建一个Elasticsearch实例。

**说明：** **地域**、**专有网络**、**虚拟交换机**与您第一步中创建的专有网络保持一致。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134312/155305101540086_zh-CN.png)

3.  购买一台与Elasticsearch服务处于同一个VPC内的ECS服务器，并分配一个公网IP合或开通弹性IP，为了节省您的成本，您可以复用已有的ECS服务器。

**说明：** 

-   建议使用 centos6、centos7 或者 aliyunos。
-   如果您添加的 ECS 需要执行 MaxCompute 任务或者同步任务，需要检查当前 ECS 的 python 版本是否是 python2.6或2.7 的版本（centos5 的版本为 2.4 ，其余 os 自带了 2.6 以上版本）。
-   请确保 ECS 有公网 IP。
4.  进入[DataWorks 控制台](https://workbench.data.aliyun.com/consolenew#/)，并进入工作区。

    -   如果您已经开通过DataWorks数据集成产品，您将会看到如下页面：

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134312/155305101540087_zh-CN.png)

    -   如果您未开通过DataWorks数据集成产品，您将会看到如下页面。您需要按照步骤开通数据集成服务，此开通动作会产生费用，请您按照费用提示进行预算评估。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134312/155305101640088_zh-CN.png)

5.  单击DataWorks项目下方的**进入数据集成**。

6.  在数据集成页面，选择左侧导航栏中的**资源组**，单击**新增资源组**。

7.  根据界面提示，输入资源组名称和服务器信息。此服务器为您已经购买的ECS服务器，服务器信息说明如下：

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134312/155305101640089_zh-CN.png)

    -   ECS UUID：[步骤 3：连接ECS实例](../../../../../cn.zh-CN/个人版快速入门/步骤 3：连接ECS实例.md#) 服务器，执行 `dmidecode | grep UUID`，取返回值。

    -   机器 IP/机器CPU（核）/机器内存（GB）：您ECS实例的公网IP/CPU/内存。您可以在ECS控制台上单击实例名称，在**配置信息**模块，找到相关信息。

    -   按照界面提示，完成**安装Agent步骤**。其中第五步为开通服务器的8000端口，可以跳过，保持系统默认即可。

8.  配置数据库白名单，添加该资源组的IP地址和DataWorks服务器的IP地址，到您的数据库白名单中。配置方法请参见[添加白名单](../../../../../cn.zh-CN/使用指南/数据集成/常见配置/添加白名单.md#)。

9.  资源组创建成功后，选择左侧导航栏的**数据源**，单击**新增数据源**。

10. 单击**MySQL**，进入**新增MySQL数据源**页面，填入数据源信息，如下图所示。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134312/155305101640090_zh-CN.png)

    数据源类型：本文档以**阿里云数据库（RDS）**为例，您也可以选择**有公网IP**和**无公网IP**。各配置项的详细信息请参见[配置MySQL数据源](../../../../../cn.zh-CN/使用指南/数据集成/数据源配置/配置MySQL数据源.md#)。

11. 选择左侧导航栏的**同步任务**，单击**新建**，选择**脚本模式**。

12. 在导入模板对话框中，选择**数据源类型** \> **MySQL**，**数据源**为您第10步中新增的数据源名称，**目标类型**为**Elasticsearch**，完成后单击**确认**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134312/155305101640091_zh-CN.png)

13. 配置数据同步脚本。具体配置请参考[脚本模式配置](../../../../../cn.zh-CN/使用指南/数据集成/作业配置/配置Reader插件/脚本模式配置.md#)，Elasticsearch 的配置规则请参考[配置Elasticsearch Writer](../../../../../cn.zh-CN/使用指南/数据集成/作业配置/配置Writer插件/配置Elasticsearch Writer.md#)。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134312/155305101640092_zh-CN.png)

    **说明：** 

    -   同步脚本的配置分为三个部分，Reader用来配置您上游数据源（待同步数据的云产品）的config，Writer用来配置 Elasticsearch的config，setting用来配置同步中的一些丢包和最大并发等。
    -   endpoint为Elasticsearch 的内网或外网地址，如果您使用的是内网地址，请在Elasticsearch的集群配置页面，配置Elasticsearch的系统白名单。如果您是用的是外网地址，请在Elasticsearch的网络配置页面，配置 Elasticsearch的公网地址访问白名单（包括[DataWorks服务器的IP地址](../../../../../cn.zh-CN/使用指南/数据集成/常见配置/添加白名单.md#)和您所使用的资源组的IP地址）。
    -   Elasticsearch Writer中accessId和accessKey需要配置您的Elasticsearch的访问用户名（默认为elastic）和密码。
    -   index为Elasticsearch实例的索引，您需要使用该索引名称访问Elasticsearch的数据。
14. 同步脚本配置完成后，单击页面右侧的**配置任务资源组**，选择您第7步创建的资源组名称，完成后单击**运行**，将MySQL中的数据同步到Elasticsearch中。


## 数据搜索分析 {#section_r2j_jqr_zgb .section}

1.  进入Elasticsearch控制台，单击右上角的**kibana控制台**，选择**Dev Tools**。

2.  执行如下命令，查看已经同步过来的数据。

    ```
    POST /testrds/_search?pretty
    {
    "query": { "match_all": {}}
    }
    ```

    testrds为您同步数据时，设置的index字段的值。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134312/155305101640093_zh-CN.png)

3.  执行如下命令，按照trans\_num字段对文档进行排序。

    ```
    POST /testrds/_search?pretty
    {
    "query": { "match_all": {} },
    "sort": { "trans_num": { "order": "desc" } }
    }
    ```

4.  执行如下命令，搜索文档中的category和brand字段。

    ```
    POST /testrds/_search?pretty
    {
    "query": { "match_all": {} },
    "_source": ["category", "brand"]
    }
    ```

5.  执行如下命令，搜索category为**生**的文档。

    ```
    POST /testrds/_search?pretty
    {
    "query": { "match": {"category":"生"} }
    }
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134312/155305101640094_zh-CN.png)

    更多命令和访问方式，请参考[ES访问测试](../../../../../cn.zh-CN/快速入门/ES访问测试.md#) 和[Elastic.co官方帮助中心](https://cloud.elastic.co/#help/)。


## 常见问题 {#section_czx_5qr_zgb .section}

-   同步过程中出现无法连接数据库的相关错误。

    解决方法：将您资源组中所使用的ECS服务器的内网IP和外网IP，都添加到您数据库的白名单中。

-   同步过程中无法连通Elasticsearch实例的相关错误。

    解决方法：按照下面步骤进行排查。

    1.  检查在运行同步脚本之前，是否在页面右侧的**配置任务资源组**中选择了您前面步骤创建的资源组。

        -   是，执行下一步。
        -   否，单击页面右侧的**配置任务资源组**，选择您前面步骤创建的资源组。完成后单击**运行**。
    2.  检查是否在Elasticsearch实例的白名单中，添加了[DataWorks服务器的IP地址](../../../../../cn.zh-CN/使用指南/数据集成/常见配置/添加白名单.md#)和您所使用的资源组的IP地址。

        -   是，执行下一步。
        -   否，将[DataWorks服务器的IP地址](../../../../../cn.zh-CN/使用指南/数据集成/常见配置/添加白名单.md#)和您所使用的资源组的IP地址，添加到 Elasticsearch 实例的白名单中。

            **说明：** 如果您使用的是内网地址，请在Elasticsearch的**集群配置**页面，配置Elasticsearch的系统白名单。如果您是用的是外网地址，请在Elasticsearch的网络配置页面，配置Elasticsearch的公网地址访问白名单（包括[DataWorks服务器的IP地址](../../../../../cn.zh-CN/使用指南/数据集成/常见配置/添加白名单.md#)和您所使用的资源组的IP地址）。

    3.  检查您的同步脚本配置是否正确。包括endpoint（您 Elasticsearch 实例的内网或外网地址）、accessId（Elasticsearch 实例的访问用户名，默认为elastic）和accessKey（Elasticsearch实例的访问密码）。


