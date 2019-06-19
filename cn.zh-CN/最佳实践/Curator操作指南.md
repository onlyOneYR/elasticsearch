# Curator操作指南 {#concept_f1f_5bt_zgb .concept}

[Curator](https://www.elastic.co/guide/en/elasticsearch/client/curator/index.html)是Elasticsearch官方的一个索引管理工具，提供了删除、创建、关闭、段合并索引等功能。本文档为您介绍Curator的使用方法，包括安装、单命令行执行、定时执行、冷热数据分离实践、索引跨节点迁移等。

## 概述 {#section_pkr_n24_k9o .section}

本文档为您讲解了如何[安装Elasticsearch Curator](#)，如何使用[单命令行执行](#)，以及如何使用curator命令完成[crontab定时执行](#)、[冷热数据分离实践](#)、[将索引从hot节点迁移到warm节点](#)等操作。

## 准备工作 {#section_ebo_04l_i2l .section}

在安装Curator前，首先要[购买一台阿里云ECS实例](../../../../cn.zh-CN/个人版快速入门/步骤 2：创建ECS实例.md#)（本文档以CentOS 7.3 64位的ECS为例），所购买的实例需要与您的阿里云Elasticsearch实例在同一个VPC下。

## 安装Elasticsearch Curator {#section_rpf_rjt_zgb .section}

在您购买的ECS命令行界面，执行以下命令安装Curator。

``` {#codeblock_10s_qja_j7m}
pip install elasticsearch-curator
```

**说明：** 

-   建议您安装5.6.0版本的Curator，它可以支持阿里云Elasticsearch 5.5.3和6.3.2版本。
-   请参考[官方文档](https://www.elastic.co/guide/en/elasticsearch/client/curator/5.6/version-compatibility.html)查看Curator版本与Elasticsearch版本的兼容性。

安装成功后，执行以下命令查看Curator版本。

``` {#codeblock_mv6_y6g_smg}
curator --version
```

正常情况下的返回结果如下。

``` {#codeblock_g2u_wvl_5i8}
curator, version 5.6.0
```

## 单命令行执行 {#section_avy_vjt_zgb .section}

您可以使用curator\_cli命令执行单个操作，命令行使用方式请参考[官方文档](https://www.elastic.co/guide/en/elasticsearch/client/curator/5.6/singleton-cli.html)。

**说明：** 

-   curator\_cli只能执行一个操作。
-   并不是所有的操作都适用于单命令行执行，例如Alias和Restore操作。

## crontab定时执行 {#section_pl1_yjt_zgb .section}

您可以通过crontab和curator命令实现定时执行一系列操作。

curator命令格式如下。

``` {#codeblock_2ub_rvb_oiu}
curator [OPTIONS] ACTION_FILE
Options:
  --config PATH  Path to configuration file. Default: ~/.curator/curator.yml
  --dry-run      Do not perform any changes.
  --version      Show the version and exit.
  --help         Show this message and exit.
```

执行curator命令时需要指定[config.yml文件（官方参考文档）](https://www.elastic.co/guide/en/elasticsearch/client/curator/current/configfile.html)和[action.yml文件（官方参考文档）](https://www.elastic.co/guide/en/elasticsearch/client/curator/current/actionfile.html)。

## 冷热数据分离实践 {#section_jml_1kt_zgb .section}

详细操作方法请参考[使用Curator进行冷热数据迁移（官方文档）](https://www.elastic.co/blog/hot-warm-architecture-in-elasticsearch-5-x)。

## 将索引从hot节点迁移到warm节点 {#section_tk2_bkt_zgb .section}

1.  在/usr/curator/路径下创建config.yml文件，配置内容参考如下示例。

    ``` {#codeblock_ee9_zu5_edr}
    client:
      hosts:
        - http://es-cn-0pxxxxxxxxxxxx234.elasticsearch.aliyuncs.com
      port: 9200
      url_prefix:
      use_ssl: False
      certificate:
      client_cert:
      client_key:
      ssl_no_validate: False
      http_auth: user:password
      timeout: 30
      master_only: False
    logging:
      loglevel: INFO
      logfile:
      logformat: default
      blacklist: ['elasticsearch', 'urllib3']
    ```

    -   `hosts`：将该参数值替换为需要访问的阿里云Elasticsearch实例地址（此处以Elasticsearch内网地址为例）。
    -   `http_auth`：将该参数值替换为对应阿里云Elasticsearch实例的账号和密码。
2.  在/usr/curator/路径下创建action.yml文件，配置内容参考如下示例。

    ``` {#codeblock_395_t4n_g0i}
    actions:
      1:
        action: allocation
        description: "Apply shard allocation filtering rules to the specified indices"
        options:
          key: box_type
          value: warm
          allocation_type: require
          wait_for_completion: true
          timeout_override:
          continue_if_exception: false
          disable_action: false
        filters:
        - filtertype: pattern
          kind: prefix
          value: logstash-
        - filtertype: age
          source: creation_date
          direction: older
          timestring: '%Y-%m-%dT%H:%M:%S'
          unit: minutes
          unit_count: 30
    ```

    以上示例按照索引创建时间，将30分钟前创建在`hot`节点以`logstash-`开头的索引迁移到`warm`节点中。您也可以根据实际场景自定义配置action.yml文件。

3.  执行以下命令，验证curator命令能否正常执行。

    ``` {#codeblock_wf5_yit_y5b}
    curator --config /usr/curator/config.yml /usr/curator/action.yml
    ```

    正常情况下会返回类似如下所示的信息。

    ``` {#codeblock_ud6_26c_bzn}
    2019-02-12 20:11:30,607 INFO      Preparing Action ID: 1, "allocation"
    2019-02-12 20:11:30,612 INFO      Trying Action ID: 1, "allocation": Apply shard allocation filtering rules to the specified indices
    2019-02-12 20:11:30,693 INFO      Updating index setting {'index.routing.allocation.require.box_type': 'warm'}
    2019-02-12 20:12:57,925 INFO      Health Check for all provided keys passed.
    2019-02-12 20:12:57,925 INFO      Action ID: 1, "allocation" completed.
    2019-02-12 20:12:57,925 INFO      Job completed.
    ```

4.  执行以下命令，使用crontab实现每隔15分钟定时执行curator命令。

    ``` {#codeblock_j9f_f86_48k}
    */15 * * * * curator --config /usr/curator/config.yml /usr/curator/action.yml
    ```


