# 使用DataWorks实现Hadoop数据同步到阿里云Elasticsearch {#concept_fzr_y2r_zgb .concept}

本文向您详细介绍如何通过DataWorks数据同步功能，将Hadoop数据同步到阿里云Elasticsearch上，并进行搜索分析。您也可以使用Java代码进行同步，具体请参考[通过ES-Hadoop将Hadoop数据写入阿里云Elasticsearch](intl.zh-CN/最佳实践/数据同步及迁移/通过ES-Hadoop将Hadoop数据写入阿里云Elasticsearch.md#)和[在 E-MapReduce 中使用 ES-Hadoop](../../../../intl.zh-CN/最佳实践/在 E-MapReduce 中使用 ES-Hadoop.md#)。

## 环境准备 {#section_r33_hfr_zgb .section}

1.  搭建Hadoop集群

    进行数据同步前，您需要保证自己的Hadoop集群环境正常。本文使用阿里云EMR服务自动化搭建Hadoop集群，详细过程请参见：[步骤三：创建集群](../../../../intl.zh-CN/快速入门/步骤三：创建集群.md#)。

    EMR Hadoop版本信息如下：

    EMR版本: EMR-3.11.0

    集群类型: HADOOP

    软件信息: HDFS2.7.2 / YARN2.7.2 / Hive2.3.3 / Ganglia3.7.2 / Spark2.3.1 / HUE4.1.0 / Zeppelin0.8.0 / Tez0.9.1 / Sqoop1.4.7 / Pig0.14.0 / ApacheDS2.0.0 / Knox0.13.0

    Hadoop集群使用VPC网络，区域为华东1（杭州），主实例组ECS计算资源配置公网及内网IP，高可用选择为否（非HA模式），具体配置如下图所示。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155963099340066_zh-CN.png)

2.  购买和配置Elasticsearch

    登录[Elasticsearch 控制台](https://elasticsearch-cn-hangzhou.console.aliyun.com/#/instances)，参考[购买和配置](../../../../intl.zh-CN/快速入门/购买和配置.md#)，购买一个Elasticsearch实例。选择与EMR集群相同的区域和VPC网络配置，如下图所示。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155963099340067_zh-CN.png)

3.  创建DataWorks工作空间

    [创建工作空间](../../../../intl.zh-CN/准备工作/管理员使用云账号/创建工作空间.md#)，区域选择**华东1**区。本文直接使用已经存在的项目bigdata\_DOC。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155963099340068_zh-CN.png)


## 数据准备 {#section_hcm_jfr_zgb .section}

在Hadoop集群中创建测试数据，步骤如下：

1.  进入[EMR控制台界面](https://emr.console.aliyun.com/)，单击左侧菜单栏的**交互式工作台**。

2.  选择**文件** \> **新建交互式任务**。本文新建一个名为**es\_test\_hive**的交互式任务，类型为**Hive**，关联集群为环境准备中创建的EMR Hadoop集群，如下图所示。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155963099340069_zh-CN.png)

3.  输入hive建表语句。本文建表语句如下：

    ``` {#codeblock_u4i_1dj_1gw}
    CREATE TABLE IF NOT
    
    EXISTS hive_esdoc_good_sale(
     create_time timestamp,
     category STRING,
     brand STRING,
     buyer_id STRING,
     trans_num BIGINT,
     trans_amount DOUBLE,
     click_cnt BIGINT
     )
     PARTITIONED BY (pt string) ROW FORMAT
    DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    ```

4.  单击**运行**。观察到**Query executed successfully**提示，说明成功在EMR Hadoop集群上创建了名称为**hive\_esdoc\_good\_sale**的表，如下图所示。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155963099340070_zh-CN.png)

5.  插入测试数据。您可以选择从OSS或其他数据源导入测试数据，也可以手动插入少量的测试数据。本文使用手动插入数据的方法，脚本如下：

    ``` {#codeblock_jcr_wrr_6y0}
    insert into
    hive_esdoc_good_sale PARTITION(pt =1 ) values('2018-08-21','外套','品牌A','lilei',3,500.6,7),('2018-08-22','生鲜','品牌B','lilei',1,303,8),('2018-08-22','外套','品牌C','hanmeimei',2,510,2),(2018-08-22,'卫浴','品牌A','hanmeimei',1,442.5,1),('2018-08-22','生鲜','品牌D','hanmeimei',2,234,3),('2018-08-23','外套','品牌B','jimmy',9,2000,7),('2018-08-23','生鲜','品牌A','jimmy',5,45.1,5),('2018-08-23','外套','品牌E','jimmy',5,100.2,4),('2018-08-24','生鲜','品牌G','peiqi',10,5560,7),('2018-08-24','卫浴','品牌F','peiqi',1,445.6,2),('2018-08-24','外套','品牌A','ray',3,777,3),('2018-08-24','卫浴','品牌G','ray',3,122,3),('2018-08-24','外套','品牌C','ray',1,62,7) ;
    ```

6.  数据插入成功后，执行`select * from hive_esdoc_good_sale where pt =1;`语句，检查Hadoop集群表中是否已存在数据可用于同步。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155963099340071_zh-CN.png)


## 数据同步 {#section_kbc_yfr_zgb .section}

**说明：** 由于DataWorks项目所处的网络环境与Hadoop集群中的数据节点（data node）网络通常不可达，我们可通过自定义资源组的方式，将DataWorks的同步任务运行在Hadoop集群的Master节点上（Hadoop集群内Master节点和数据节点通常可达）。

1.  查看Hadoop集群的数据节点

    1.  在EMR控制台上，单击左侧菜单栏的**集群**。

    2.  选择您的集群，单击右侧的**管理**。

    3.  在集群管理控制台，单击左侧菜单栏的**主机列表**，查看集群master节点和数据节点信息，如下图所示。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155963099340072_zh-CN.png)

        **说明：** 通常非HA模式的EMR上Hadoop集群的master节点主机名为 **emr-header-1**，datanode主机名为**emr-worker-X**。

    4.  单击上图中Master节点的ECS ID，进入ECS实例详情页。单击**远程连接**进入ECS，参考通过`hadoop dfsadmin -report`命令查看数据节点信息，如下图所示。

**说明：** Master节点的ECS登录密码为您创建集群时设置的密码。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155963099340073_zh-CN.png)

2.  新建自定义资源组

    1.  进入DataWorks的数据集成页面，选择**资源组** \> **新增资源组**。关于自定义资源组的详细信息请参考[新增任务资源](../../../../intl.zh-CN/使用指南/数据集成/常见配置/新增任务资源.md#)。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155963099340074_zh-CN.png)

    2.  根据界面提示，输入资源组名称和服务器信息。此服务器为您EMR集群的master节点，服务器信息说明如下：

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155963099340075_zh-CN.png)

        -   **网络类型**为**专有网络**。

**说明：** 对于专有网络类型，需输入服务器UUID。对于经典网络类型，需输入服务器名称。目前仅DataWorks V2.0 华东2区支持经典网络类型的调度资源添加，对于其他区域，无论您使用的是经典网络还是专有网络类型，在添加调度资源组时都请选择专有网络类型。

        -   ECS UUID：登录EMR集群的master节点，执行 `dmidecode | grep UUID`，取返回值。

        -   机器 IP/机器CPU（核）/机器内存（GB）：您master节点的公网IP/CPU/内存。您可以在master节点的ECS控制台上单击实例名称，在配置信息模块，找到相关信息。

    3.  完成**添加服务器**后，需保证master node与DataWorks网络可达。如果您使用的是ECS服务器，需设置服务器安全组。如果您使用的内网IP互通，可参考[添加安全组](../../../../intl.zh-CN/使用指南/数据集成/常见配置/添加安全组.md#)。如果您使用的是公网IP，可直接设置安全组公网出入方向规则。由于本文档的EMR集群使用的是VPC网络，且与DataWorks在同一区域下，因此不需要进行安全组设置。

    4.  按照提示安装自定义资源组Agent，观察到当前状态为**可用**，说明新增自定义资源组成功。

**说明：** 本文使用的是VPC网络类型，不需开通8000端口。

        如果状态为不可用，您可以登录master node，使用`tail –f/home/admin/alisatasknode/logs/heartbeat.log`命令查看DataWorks与master node之间心跳报文是否超时，如下图所示。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155963099440076_zh-CN.png)

3.  新建数据源

    1.  在DataWorks的数据集成页面，单击**数据源** \> **新增数据源**，在弹框中选择**HDFS**类型的数据源。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155963099440077_zh-CN.png)

    2.  在新增HDFS数据源页面中，填写**数据源名称**和**defaultFS**。

**说明：** 对于EMR Hadoop集群而言，如果Hadoop集群为非HA集群，则此处地址为`hdfs://emr-header-1的IP:9000`。如果Hadoop集群为HA集群，则此处地址为`hdfs://emr-header-1的IP:8020`。在本文中，emr-header-1与DataWorks通过VPC网络连接，因此此处填写内网IP，且不支持连通性测试。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155963099440078_zh-CN.png)

4.  配置数据同步任务

    1.  在DataWorks数据集成页面，单击左侧菜单栏的**同步任务**，选择**新建** \> **脚本模式**。

    2.  在导入模板对话框中，选择数据源类型如下。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155963099440079_zh-CN.png)

    3.  完成导入模板后，同步任务会转入脚本模式，本文中配置脚本如下，相关解释请参见[脚本模式配置](../../../../intl.zh-CN/使用指南/数据集成/作业配置/配置Reader插件/脚本模式配置.md#)，Elasticsearch 的配置规则请参考[配置Elasticsearch Writer](../../../../intl.zh-CN/使用指南/数据集成/作业配置/配置Writer插件/配置Elasticsearch Writer.md#)。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155963099440080_zh-CN.png)

    -   同步脚本的配置分为三个部分，Reader用来配置您上游数据源（待同步数据的云产品）的config，Writer用来配置 Elasticsearch的config，setting用来配置同步中的一些丢包和最大并发等。
    -   path为数据在Hadoop集群中存放的位置，您可以在登录master node后，使用`hdfs dfs –ls /user/hive/warehouse/hive_esdoc_good_sale`命令确认。对于分区表，您可以不指定分区，DataWorks数据同步会自动递归到分区路径。
    -   由于Elasticsearch不支持timestamp类型，本文档将creat\_time字段的类型设置为string。
    -   endpoint为Elasticsearch 的内网或外网地址。如果您使用的是内网地址，请在Elasticsearch的集群配置页面，配置Elasticsearch的系统白名单。如果您是用的是外网地址，请在Elasticsearch的网络配置页面，配置 Elasticsearch的公网地址访问白名单（包括DataWorks服务器的IP地址和您所使用的资源组的IP地址）。
    -   Elasticsearch Writer中accessId和accessKey需要配置您的Elasticsearch的访问用户名（默认为elastic）和密码。
    -   index为Elasticsearch实例的索引，您需要使用该索引名称访问Elasticsearch的数据。
    -   在创建同步任务时，DataWorks的默认配置脚本中，errorLimit的record字段值为0，您需要将其修改为大一些的数值，比如1000。
5.  完成配置后，单击页面右侧的**配置任务资源组**，选择您创建的资源组名称，完成后单击**运行**。

    如果提示**任务运行成功**，则说明同步任务已完成。如果运行失败，可通过复制日志进行进一步排查。


## 结果验证 {#section_zcg_1jr_zgb .section}

1.  进入Elasticsearch控制台，单击右上角的**kibana控制台**，选择**Dev Tools**。

2.  执行如下命令，查看已经同步过来的数据。

    ``` {#codeblock_e2l_ds1_uhy}
    POST /hive_doc_esgood_sale/_search?pretty
    {
    "query": { "match_all": {}}
    }
    ```

    `hive_doc_esgood_sale`为您同步数据时，设置的index字段的值。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155963099440081_zh-CN.png)


## 数据搜索与分析 {#section_kbc_3jr_zgb .section}

1.  如下示例返回品牌为A的所有文档。

    ``` {#codeblock_kow_zpn_5qr}
    POST /hive_doc_esgood_sale/_search?pretty
    {
      "query": { "match_phrase": { "brand":"品牌A" } }
    }
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155963099440082_zh-CN.png)

2.  如下示例将各文档按照**点击次数**进行排序，判断各品牌产品的热度。

    ``` {#codeblock_54h_qte_545}
    POST /hive_doc_esgood_sale/_search?pretty
    {
    "query": { "match_all": {} },
    "sort": { "click_cnt": { "order": "desc" } },
    "_source": ["category", "brand","click_cnt"]
    }
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134311/155963099440083_zh-CN.png)

    更多命令和访问方式，请参考[阿里云Elasticsearch官方文档](https://www.alibabacloud.com/help/product/57736.htm)和[Elastic.co官方帮助中心](https://cloud.elastic.co/?spm=a2c4g.11186623.2.33.4e81422aVNpLYG#help/)。


