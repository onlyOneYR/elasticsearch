# 使用Canal迁移数据至阿里云Elasticsearch {#task_2054659 .task}

本文档为您介绍如何使用Canal将阿里云RDS for MySQL中的增量数据迁移至阿里云Elasticsearch中。

在使用Canal进行数据库迁移前，您需要准备以下的依赖环境。

**说明：** 开通服务时，请确保RDS for MySQL、Elasticsearch和ECS在相同的**区域**、**可用区**、**VPC**、**子网**和**安全组**下。

-   阿里云RDS for MySQL。

    用来存放源数据和增量数据，开通方式请参见[创建RDS for MySQL实例](../../../../cn.zh-CN/RDS for MySQL 快速入门/创建RDS for MySQL实例.md#)。本文使用的配置如下图所示。

    ![RDS for MySQL配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1630415/156816764059221_zh-CN.png)

-   canal.adapter-1.1.4.tar.gz和canal.deployer-1.1.4.tar.gz。

    进行数据库日志解析，获取增量变更进行同步，是Github开源ETL软件，详情请参见[canal](https://github.com/alibaba/canal/tree/canal-1.1.4)。

-   阿里云Elasticsearch。

    用来接收增量数据，开通方式请参见[创建阿里云Elasticsearch实例](../../../../cn.zh-CN//创建阿里云Elasticsearch实例.md#)。本文使用的配置如下图所示。

    ![ES配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1630415/156816764059222_zh-CN.png)

-   阿里云ECS。

    用来连接RDS for MySQL，以及部署canalDeployer和canalAdapter，开通方式请参见[创建ECS实例](../../../../cn.zh-CN/个人版快速入门/创建ECS实例.md#)。本文使用的配置如下图所示。

    ![ECS配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1630415/156816764059223_zh-CN.png)


## 创建表和字段 {#section_tt7_ab3_ll2 .section}

1.  在RDS for MySQL数据库中，创建表和字段。 

    本文创建的表名称为**es\_test**，包含的字段如下图所示。

    ![es_test字段及索引](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1630415/156816764159264_zh-CN.png)

2.  在ES实例中，创建索引和mapping。 [登录Kibana控制台](../../../../cn.zh-CN/用户指南/可视化控制/Kibana/登录Kibana控制台.md#)，在开发工具页面的Console中执行以下命令创建索引和mapping。

    **说明：** 创建索引和mapping需要与上一步RDS for MySQL中创建的表名称和字段（名称和类型）保持一致。

    ``` {#codeblock_53m_5pf_hk7}
    PUT es_test?include_type_name=true
    {
    
        "settings" : {
          "index" : {
            "number_of_shards" : "5",
            "number_of_replicas" : "1"
          }
        },
        "mappings" : {
            "_doc" : {
                "properties" : {
                  "count": {          
                       "type": "text"       
                   },
                  "id": {
                       "type": "integer"
                   },
                    "name" : {
                        "type" : "text"                    
                    },
                    "color" : {
                        "type" : "text"                    
                    }
                }
            }
        }
    }
    ```

    创建成功后，返回如下结果。

    ``` {#codeblock_6so_flv_4ni}
    {
      "acknowledged" : true,
      "shards_acknowledged" : true,
      "index" : "es_test"
    }
    ```


## 安装MySQL {#section_l0q_mv4_4p8 .section}

1.  连接阿里云ECS实例。
2.  下载MySQL源安装包。 

    ``` {#codeblock_dtf_rd5_z9o}
    wget http://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
    ```

3.  安装MySQL源。 

    ``` {#codeblock_tzh_eao_gfk}
    yum -y install mysql57-community-release-el7-11.noarch.rpm
    ```

4.  检查MySQL源是否安装成功。 

    ``` {#codeblock_tx8_128_yy3}
    yum repolist enabled | grep mysql.*
    ```

    安装成功会返回如下结果。

    ![MySQL源安装成功](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1630415/156816764159224_zh-CN.png)

5.  安装MySQL服务器。 

    ``` {#codeblock_bgz_sqb_56z}
    yum install mysql-community-server
    ```

6.  启动MySQL服务并查看MySQL服务的状态。 

    ``` {#codeblock_7ay_nru_gai}
    systemctl start mysqld.service
    systemctl status mysqld.service
    ```

    启动成功后会返回如下结果。

    ![启动MySQL并查看状态](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1630415/156816764159227_zh-CN.png)

7.  连接RDS for MySQL数据库。 

    **说明：** 

    -   在使用以下命令连接RDS for MySQl数据库之前，您需要将ECS的**私有IP**添加到RDS for MySQL的白名单中，详情请参见[设置白名单](../../../../cn.zh-CN/RDS for MySQL 快速入门/初始化配置/设置白名单.md#)。
    -   使用Canal需要开启MySQL binlog模式，阿里云RDS for MySQL默认开启，也可以通过如下方式检查binlog是否正确启动。

        ``` {#codeblock_96f_87n_633}
        show variables like '%log_bin%';
        ```

        开启时，显示如下结果。

        ![开启MySQL binlog模式](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1630415/156816764159233_zh-CN.png)

    ``` {#codeblock_poc_tfr_4oe}
    mysql -h<主机名> -P<端口> -u<用户名> -p<密码> -D<数据库>
    ```

    |变量|说明|
    |--|--|
    |<主机名\>|RDS for MySQL实例的**内网地址**。可在实例的基本信息页面获取。|
    |<端口\>|RDS for MySQL实例的**内网端口**，一般为**3306**。可在实例的基本信息页面获取。|
    |<用户名\>|RDS for MySQL数据库的账号，可在实例的账号管理页面查看。如果还没有账号，需要首先创建一个数据库账号，详情请参见[创建账号和数据库](../../../../cn.zh-CN/RDS for MySQL 快速入门/初始化配置/创建账号和数据库.md#)。|
    |<数据库\>|RDS for MySQL数据库的名称，可在实例的数据库管理页面查看。如果还没有数据库，需要首先创建一个数据库，详情请参见[创建账号和数据库](../../../../cn.zh-CN/RDS for MySQL 快速入门/初始化配置/创建账号和数据库.md#)。|
    |<密码\>|登录数据库的密码。|

    命令示例：

    ``` {#codeblock_l6c_6k3_nr3}
    mysql -hrm-bp1u1xxxxxxxxx6ph.mysql.rds.aliyuncs.com -P3306 -ues -pmima -Delasticsearch
    ```

    连接成功后的结果如下所示。

    ![连接RDS MySQL](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1630415/156816764159232_zh-CN.png)


## 安装JDK {#section_1uk_499_551 .section}

1.  连接阿里云ECS实例，查看可用的JDK软件包列表。 

    ``` {#codeblock_x3l_8ye_q7l}
    yum search java | grep -i --color JDK
    ```

2.  选择合适的版本，安装JDK。本文选择**java-1.8.0-openjdk-devel.x86\_64**。 

    ``` {#codeblock_evo_mcv_fg6}
    yum install java-1.8.0-openjdk-devel.x86_64
    ```

3.  配置环境变量。 
    1.  打开etc文件夹下的profile文件。 

        ``` {#codeblock_cxg_i9i_147}
        vi /etc/profile
        ```

    2.  在文件内添加如下的环境变量。 

        ``` {#codeblock_wpu_zhj_592}
        export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.71-2.b15.el7_2.x86_64
        export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
        export PATH=$PATH:$JAVA_HOME/bin
        ```

    3.  使用`:wq`保存文件并退出vi模式，执行以下命令使配置生效。 

        ``` {#codeblock_59m_x99_zg0}
        source /etc/profile
        ```

4.  分别输入以下命令，验证JDK是否安装成功。 
    -   `java`
    -   `javac`
    -   `java -version` 

        显示以下结果说明JDK安装成功。

        ![jdk安装成功](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1630415/156816764159240_zh-CN.png)


## 安装并启动Canal-server {#section_8jx_g9s_pto .section}

1.  连接阿里云ECS实例，下载并解压Canal-deployer。本文使用Canal-deployer 1.1.4版本。 

    ``` {#codeblock_ocz_s04_vqh}
    wget https://github.com/alibaba/canal/releases/download/canal-1.1.4/canal.deployer-1.1.4.tar.gz
    ```

2.  解压canal.deployer-1.1.4.tar.gz。 

    ``` {#codeblock_gre_sbj_243}
    tar -zxvf canal.deployer-1.1.4.tar.gz
    ```

3.  修改conf/example/instance.properties文件。 

    ``` {#codeblock_62p_ts9_rku}
    vi conf/example/instance.properties
    ```

    ![修改conf/example/instance.properties文件](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1630415/156816764159251_zh-CN.png)

    |配置项|说明|
    |---|--|
    |canal.instance.master.address|`<RDS for MySQL数据库的内网地址>:<内网端口>`，相关信息可在RDS for MySQL实例的基本信息页面获取。例如`rm-bp1u1xxxxxxxxx6ph.mysql.rds.aliyuncs.com：3306`。|
    |canal.instance.dbUsername|RDS for MySQL数据库的账号名称，可在实例的账号管理页面获取。|
    |canal.instance.dbPassword|RDS for MySQL数据库的密码。|

4.  使用:wq命令保存文件并退出vi模式。
5.  启动Canal-server，并查看日志。 

    ``` {#codeblock_00e_oyr_tun}
    ./bin/startup.sh
    cat logs/canal/canal.log
    ```

    ![启动canal-server](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1630415/156816764159254_zh-CN.png)


## 安装并启动Canal-adapter {#section_2tp_qfz_qw5 .section}

1.  连接阿里云ECS实例，下载并解压Canal-adapter。本文使用Canal-adapter1.1.4版本。 

    ``` {#codeblock_9f5_zmc_w7y}
    wget https://github.com/alibaba/canal/releases/download/canal-1.1.4/canal.adapter-1.1.4.tar.gz
    ```

2.  解压canal.adapter-1.1.4.tar.gz。 

    ``` {#codeblock_kj4_wyt_8xa}
    tar -zxvf canal.adapter-1.1.4.tar.gz
    ```

3.  修改conf/application.yml文件。 

    ``` {#codeblock_1z7_8di_q11}
    vi conf/application.yml
    ```

    ![修改conf/application.yml文件](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1630415/156816764159278_zh-CN.png)

    |配置项|说明|
    |---|--|
    |canal.conf.canalServerHost|canalDeployer访问地址。保持默认（`127.0.0.1:11111`）即可。|
    |canal.conf.srcDataSources.defaultDS.url|`jdbc:mysql://<RDS for MySQL内网地址>:<内网端口>/<数据库名称>?useUnicode=true`，相关信息可在RDS for MySQL实例的基本信息页面获取。例如`jdbc:mysql://rm-bp1xxxxxxxxxnd6ph.mysql.rds.aliyuncs.com：3306/elasticsearch?useUnicode=true`。|
    |canal.conf.srcDataSources.defaultDS.username|RDS for MySQL数据库的账号名称，可在RDS for MySQL实例的账号管理页面获取。|
    |canal.conf.srcDataSources.defaultDS.password|RDS for MySQL数据库的密码。|
    |canal.conf.canalAdapters.groups.outerAdapters.hosts|定位到`name:es`的位置，将hosts替换为`<阿里云ES实例的内网地址>:<内网端口>`，相关信息可在ES实例的[基本信息](../../../../cn.zh-CN/实例/基本信息/基本信息.md#)页面获取。例如`es-cn-v64xxxxxxxxx3medp.elasticsearch.aliyuncs.com:9200`。|
    |canal.conf.canalAdapters.groups.outerAdapters.mode|必须设置为`rest`。|
    |canal.conf.canalAdapters.groups.outerAdapters.properties.security.auth|`<阿里云ES实例的账号>:<密码>`。例如`elastic:es_password`。|
    |canal.conf.canalAdapters.groups.outerAdapters.properties.cluster.name|阿里云ES实例的ID，可在实例的[基本信息](../../../../cn.zh-CN/实例/基本信息/基本信息.md#)页面获取。例如`es-cn-v64xxxxxxxxx3medp`。|

4.  使用:wq命令保存文件并退出vi模式。
5.  同样的方式，修改conf/es/\*.yml文件，定义MySQL数据到ES数据的映射字段。 

    ![修改conf/es/*.yml文件](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1630415/156816764159280_zh-CN.png)

    |配置项|说明|
    |---|--|
    |esMapping.\_index|[创建表和字段](#)章节中，在ES实例中所创建的索引的名称。本文使用**es\_test**。|
    |esMapping.\_type|[创建表和字段](#)章节中，在ES实例中所创建的索引的类型。本文使用**\_doc**。|
    |esMapping.\_id|需要同步到ES实例的文档的id，可自定义。本文使用**\_id**。|
    |esMapping.sql|SQL语句，用来查询需要同步到ES中的字段。本文使用`select t.id as _id,t.id,t.count,t.name,t.color from es_test t;`。|

6.  启动Canal-adapter服务，并查看日志。 

    ``` {#codeblock_eue_3u2_fpk}
    ./bin/startup.sh
    cat logs/adapter/adapter.log
    ```

    服务启动正常时，结果如下所示。

    ![Canal-adapter服务日志](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1630415/156816764159329_zh-CN.png)


## 验证Canal自动导出增量数据 {#section_fxm_zna_2n1 .section}

1.  在RDS for MySQL数据库中，新增/修改/删除数据库中**es\_test**表的数据。 

    ``` {#codeblock_65d_y71_ixz}
    insert `elasticsearch`.`es_test`(`count`,`id`,`name`,`color`) values('11',2,'canal_test2','red');
    ```

2.  在阿里云ES控制台中，[登录Kibana控制台](../../../../cn.zh-CN/用户指南/可视化控制/Kibana/登录Kibana控制台.md#)。
3.  在Kibana控制台开发工具页面的Console中，执行以下命令查询同步过来的数据。 

    ``` {#codeblock_jtj_b9x_boz}
    GET /es_test/_search
    ```

    数据同步成功后，返回如下结果。

    ![数据同步成功结果](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1630415/156816764159328_zh-CN.png)


