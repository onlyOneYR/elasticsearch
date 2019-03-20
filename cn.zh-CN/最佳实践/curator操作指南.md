# curator操作指南 {#concept_f1f_5bt_zgb .concept}

## 安装 {#section_rpf_rjt_zgb .section}

1.  在阿里云ES实例对应VPC内购买一台阿里云ECS实例（此处以CentOS 7.3 64位ECS为例）。

2.  执行如下命令：

    1.  安装elasticsearch-curator：

        ```
        pip install elasticsearch-curator
        ```

        **说明：** 

        -   建议安装最新的5.6.0版本curator，可以支持阿里云ES 5.5.3和6.3.2版本。
        -   [curator版本与ES版本兼容性官方参考文档](https://www.elastic.co/guide/en/elasticsearch/client/curator/5.6/version-compatibility.html)。
    2.  查看当前已安装curator版本：

        ```
        curator --version
        ```

        返回版本信息：

        ```
        curator, version 5.6.0
        ```


## 单命令行执行 {#section_avy_vjt_zgb .section}

-   用户可以使用`curator_cli`执行单个action。

-   [单命令行使用方式官方参考文档](https://www.elastic.co/guide/en/elasticsearch/client/curator/5.6/singleton-cli.html)。

    **说明：** 

    -   只能执行一个action。
    -   并不是所有的action都可以使用curator执行，例如 Alias and Restore。

## crontab定时执行 {#section_pl1_yjt_zgb .section}

通过crontab和curator命令实现定时执行一系列action操作。

curator命令格式：

```
curator [OPTIONS] ACTION_FILE
Options:
  --config PATH  Path to configuration file. Default: ~/.curator/curator.yml
  --dry-run      Do not perform any changes.
  --version      Show the version and exit.
  --help         Show this message and exit.
```

-   执行curator命令时需要指定[config.yml文件（官方参考文档）](https://www.elastic.co/guide/en/elasticsearch/client/curator/current/configfile.html)。

-   执行curator命令时需要指定[action.yml文件（官方参考文档）](https://www.elastic.co/guide/en/elasticsearch/client/curator/current/actionfile.html)。


## 冷热数据分离实践 {#section_jml_1kt_zgb .section}

[使用curator进行冷热数据迁移（官方参考文档）](https://www.elastic.co/blog/hot-warm-architecture-in-elasticsearch-5-x)。

## 将索引从hot节点迁移到warm节点 {#section_tk2_bkt_zgb .section}

1.  在/usr/curator/路径下创建**config.yml**文件，配置内容参考如下：

    **说明：** 

    -   hosts：将该参数值替换为需要访问的阿里云ES实例地址（此处以ES内网地址为例）。
    -   http\_auth：将该参数值替换为对应阿里云ES实例访问账号和密码。
    ```
    client:
      hosts:
        - http://es-cn-0pp0z9p2v00031234.elasticsearch.aliyuncs.com
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

2.  在/usr/curator/路径下创建**action.yml**文件，配置内容参考如下：

    **说明：** 

    -   按照索引创建时间，将30分钟前创建在**hot**节点以logstash-开头的索引迁移到 **warm**节点。
    -   以下配置文件内容可以根据用户实际场景需要进行自定义配置。
    ```
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

3.  效验curator命令能否正常执行：

    ```
    curator --config /usr/curator/config.yml /usr/curator/action.yml
    ```

    正常执行返回信息参考如下：

    ```
    2019-02-12 20:11:30,607 INFO      Preparing Action ID: 1, "allocation"
    2019-02-12 20:11:30,612 INFO      Trying Action ID: 1, "allocation": Apply shard allocation filtering rules to the specified indices
    2019-02-12 20:11:30,693 INFO      Updating index setting {'index.routing.allocation.require.box_type': 'warm'}
    2019-02-12 20:12:57,925 INFO      Health Check for all provided keys passed.
    2019-02-12 20:12:57,925 INFO      Action ID: 1, "allocation" completed.
    2019-02-12 20:12:57,925 INFO      Job completed.
    ```

4.  使用crontab实现每隔15分钟定时执行curator命令：

    ```
    */15 * * * * curator --config /usr/curator/config.yml /usr/curator/action.yml
    ```


