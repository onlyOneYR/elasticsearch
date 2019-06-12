# 使用DataWorks实现MaxCompute与阿里云ES数据同步 {#concept_m4j_4ns_zgb .concept}

阿里云上拥有丰富的云存储、云数据库产品。如果您希望对这些产品中的数据进行分析和搜索，可以通过DataWorks的数据集成服务，将离线数据同步到阿里云Elasticsearch中进行搜索分析，最快可达到5分钟一次。

**说明：** 做数据同步时可能会产生公网流量费用，请您知晓。

## 准备工作 {#section_pmq_5ns_zgb .section}

完成离线数据的分析与搜索，您需要完成以下几步操作：

-   [步骤一：创建和查看表](../../../../cn.zh-CN/快速入门/步骤一：创建和查看表.md#)，并[步骤二：导入数据](../../../../cn.zh-CN/快速入门/步骤二：导入数据.md#)。实际情况中，您可以将[Hadoop数据迁移MaxCompute最佳实践](../../../../cn.zh-CN/最佳实践/数据迁移/Hadoop数据迁移MaxCompute最佳实践.md#)再进行同步，本案例使用的表结构和部分数据如下：

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155866689440112_zh-CN.png)

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155866689440113_zh-CN.png)

-   创建一个阿里云Elasticsearch实例，用来存储数据集成系统同步成功的数据。

-   购买一台与阿里云Elasticsearch相同VPC的阿里云ECS，这台ECS将获取数据源数据并执行写阿里云Elasticsearch数据的任务（该任务将由数据集成系统统一下发）。

-   开通DataWorks数据集成服务，并且将ECS作为一个可以执行任务的资源，注册到数据集成服务中去。

-   配置一个数据同步脚本，并让其周期性执行。


## 操作步骤 {#section_rpb_yns_zgb .section}

1.  创建阿里云Elasticsearch和ECS实例
    1.  [搭建IPv4专有网络](../../../../cn.zh-CN/快速入门/搭建IPv4专有网络.md#)，本案例创建了一个位于**华东1**区，名称为**es\_test\_vpc**的专有网络，对应的交换机名称为**es\_test\_switch**。

    2.  进入[阿里云Elasticsearch控制台](https://elasticsearch-cn-hangzhou.console.aliyun.com/)，创建一个阿里云Elasticsearch实例。

**说明：** **地域**、**专有网络**、**虚拟交换机**与您第一步中创建的专有网络保持一致。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155866689440114_zh-CN.png)

    3.  购买一台与阿里云Elasticsearch实例处于同一个VPC内的ECS服务器，并分配一个公网IP或开通弹性IP，为了节省您的成本，您可以复用已有且符合条件的ECS服务器。

        本案例创建了一个位于**华东1，可用区F**的ECS实例，使用**CentOS 7.4 64**位系统，并勾选**分配公网地址**，网络配置如下：

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155866689440115_zh-CN.png)

        **说明：** 

        -   建议使用CentOS 6、CentOS 7 或者 Aliyun Linux。
        -   如果您添加的ECS需要执行MaxCompute任务或者同步任务，需要检查当前ECS的python版本是否是python2.6或2.7 的版本（CentOS 5 的版本为2.4，其余CentOS自带了2.6以上版本）。
        -   请确保 ECS 有公网 IP。
2.  配置数据同步
    1.  进入[DataWorks控制台](https://workbench.data.aliyun.com/consolenew#/)创建项目，本案例使用名称为**bigdata\_DOC**的DataWorks项目。

        -   如果您已经开通过DataWorks数据集成产品，您将会看到如下页面：

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155866689440116_zh-CN.png)

        -   如果您未开通过DataWorks数据集成产品，将会看到如下页面。您需要按照步骤开通数据集成服务，此开通动作会产生费用，请您按照费用提示进行预算评估。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155866689540117_zh-CN.png)

    2.  单击DataWorks项目下方的**进入数据集成**。

    3.  创建资源组。

        1.  在数据集成页面，选择左侧导航栏中的**资源组**，单击**新增资源组**。

        2.  按照以下步骤，完成资源组的添加：

            1.  创建资源组：自定义输入资源组名称，本案例的资源组名称为**es\_test\_resource**。

                ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155866689540118_zh-CN.png)

            2.  添加服务器。

                ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155866689540119_zh-CN.png)

                -   ECS UUID：[步骤 3：连接ECS实例](../../../../cn.zh-CN/个人版快速入门/步骤 3：连接ECS实例.md#) 服务器，执行 `dmidecode | grep UUID`，取返回值。

                    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155866689540120_zh-CN.png)

                -   机器 IP/机器CPU（核）/机器内存（GB）：ECS实例的公网IP/CPU/内存。进入ECS控制台，单击实例名称链接，在**配置信息**模块，可以找到相关信息。

            3.  安装Agent：按照界面提示，完成**安装Agent步骤**。由于本案例使用的是VPC网络，不需要开通服务器的8000端口。
            4.  检查联通：联通成功后，状态会显示为**可用**。如果状态为**不可用**，您可以登录该ECS服务器，使用`tail -f /home/admin/alisatasknode/logs/heartbeat.log`命令查看DataWorks与该ECS服务器之间心跳报文是否超时。
    4.  添加数据源。

        1.  在数据集成页面，选择左侧导航栏中的**数据源**，单击**新增数据源**。

        2.  选择数据源类型为**MaxCompute**。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155866689540121_zh-CN.png)

        3.  输入数据源信息，本案例创建的数据源名称为**odps\_es**，如下所示。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155866689540122_zh-CN.png)

            -   **ODPS空间名称**：在DataWorks的数据开发页面，表对应的空间名称显示在左上角图标右侧，如下图所示：

                ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155866689540123_zh-CN.png)

            -   **Access Id**/**Access Key**：鼠标移至您的用户名称上，选择**用户信息**，如下图所示：

                ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155866689540793_zh-CN.png)

                在个人信息页面，鼠标移至您的用户头像上，单击**accesskeys**进行获取，如下图所示：

                ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155866689540124_zh-CN.png)

    5.  配置同步任务。

        1.  在**数据开发**页面，单击左侧菜单栏中的**数据开发**，打开业务流程导航栏：

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155866689540797_zh-CN.png)

        2.  右键单击导航栏中的**数据集成**，选择**新建数据集成节点** \> **同步节点**，输入同步任务名称：

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155866689540798_zh-CN.png)

        3.  成功创建同步节点后，单击新建同步节点右上角的**转换脚本**，选择**确认**即可进入脚本模式：

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155866689540799_zh-CN.png)

        4.  单击脚本模式右上角的**导入模板**，在弹框中分别选择读取端的来源类型和数据源、写入端的目标类型和数据源，单击**确认**生成初始脚本：

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155866689540800_zh-CN.png)

        5.  配置数据同步脚本，具体配置请参考[脚本模式配置](../../../../cn.zh-CN/使用指南/数据集成/作业配置/配置Reader插件/脚本模式配置.md#)，Elasticsearch的配置规则请参考[配置Elasticsearch Writer](../../../../cn.zh-CN/使用指南/数据集成/作业配置/配置Writer插件/配置Elasticsearch Writer.md#)。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155866689540126_zh-CN.png)

**说明：** 

-   同步脚本的配置分为三个部分，Reader用来配置您上游数据源（待同步数据的云产品）的config，Writer用来配置阿里云Elasticsearch的config，setting用来配置同步中的一些丢包和最大并发等。
-   endpoint为阿里云Elasticsearch的内网或外网地址，本案例使用内网地址，所以不用配置白名单。如果您是用的是外网地址，请在阿里云Elasticsearch的网络配置页面，配置阿里云Elasticsearch的公网地址访问白名单（包括[DataWorks服务器的IP地址](../../../../cn.zh-CN/使用指南/数据集成/常见配置/添加白名单.md#)和您所使用的资源组的IP地址）。
-   Elasticsearch Writer中accessId和accessKey需要配置您的阿里云Elasticsearch的访问用户名（默认为elastic）和密码。
-   index为阿里云Elasticsearch实例的索引，您需要使用该索引名称访问阿里云Elasticsearch的数据。本案例中的index名为es\_index。
-   如果您的ODPS表是一个分区表，需要在partition字段中设置分区信息，本案例中的分区信息为**pt=1**。
            配置代码示例如下：

            ```
            {
            "configuration": {
            "reader": {
            "plugin": "odps",
            "parameter": {
              "partition": "pt=1",
              "datasource": "odps_es",
              "column": [
                "create_time",
                "category",
                "brand",
                "buyer_id",
                "trans_num",
                "trans_amount",
                "click_cnt"
              ],
              "table": "hive_doc_good_sale"
            }
            },
            "writer": {
            "plugin": "elasticsearch",
            "parameter": {
              "accessId": "elastic",
              "endpoint": "http://es-cn-mpXXXXXXX.elasticsearch.aliyuncs.com:9200",
              "indexType": "elasticsearch",
              "accessKey": "XXXXXX",
              "cleanup": true,
              "discovery": false,
              "column": [
                {
                  "name": "create_time",
                  "type": "string"
                },
                {
                  "name": "category",
                  "type": "string"
                },
                {
                  "name": "brand",
                  "type": "string"
                },
                {
                  "name": "buyer_id",
                  "type": "string"
                },
                {
                  "name": "trans_num",
                  "type": "long"
                },
                {
                  "name": "trans_amount",
                  "type": "double"
                },
                {
                  "name": "click_cnt",
                  "type": "long"
                }
              ],
              "index": "es_index",
              "batchSize": 1000,
              "splitter": ","
            }
            },
            "setting": {
            "errorLimit": {
              "record": "0"
            },
            "speed": {
              "throttle": false,
              "concurrent": 1,
              "mbps": "1",
              "dmu": 1
            }
            }
            },
            "type": "job",
            "version": "1.0"
            }
            ```

        6.  同步脚本配置完成后，单击**运行**，将ODPS中的数据同步到阿里云Elasticsearch中。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155866689640802_zh-CN.png)

3.  结果验证
    1.  进入阿里云Elasticsearch控制台，单击右上角的**kibana控制台**，选择**Dev Tools**。

    2.  执行如下命令，查看数据是否已经同步到ES中。

```
POST /es_index/_search?pretty
{
"query": { "match_all": {}}
}
```

        es\_index为您同步数据时，设置的index字段的值。

        如果数据同步成功，会显示以下界面：

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134315/155866689640128_zh-CN.png)

    3.  执行如下命令，按照trans\_num字段对文档进行排序。

        ```
        POST /es_index/_search?pretty
        {
        "query": { "match_all": {} },
        "sort": { "trans_num": { "order": "desc" } }
        }
        ```

    4.  执行如下命令，搜索文档中的category和brand字段。

        ```
        POST /es_index/_search?pretty
        {
        "query": { "match_all": {} },
        "_source": ["category", "brand"]
        }
        ```

    5.  执行如下命令，搜索category为生鲜的文档。

        ```
        POST /es_index/_search?pretty
        {
        "query": { "match": {"category":"生鲜"} }
        }
        ```

        更多命令和访问方式，请参考[ES访问测试](../../../../cn.zh-CN/快速入门/ES访问测试.md#)和[Elastic.co官方帮助中心](https://cloud.elastic.co/#help/)。


## 常见问题 {#section_mkf_brs_zgb .section}

**无法连通阿里云ES实例相关报错**

1.  检查在运行同步脚本之前，是否在页面右侧的**配置任务资源组**中选择了您前面步骤创建的资源组。

    -   是，执行下一步。
    -   否，单击页面右侧的**配置任务资源组**，选择您前面步骤创建的资源组，完成后单击**运行**。
2.  检查您的同步脚本配置是否正确，包括endpoint（您的阿里云Elasticsearch实例的内网或外网地址，使用外网地址需要配置公网地址访问白名单）、accessId（阿里云Elasticsearch实例的访问用户名，默认为elastic）和accessKey（阿里云Elasticsearch实例的访问密码）。


