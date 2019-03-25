# Use Curator {#concept_f1f_5bt_zgb .concept}

## Install Elasticsearch Curator {#section_rpf_rjt_zgb .section}

1.  Purchase an Alibaba Cloud ECS instance in the same VPC network as your Alibaba Cloud Elasticsearch instance. This example uses an ECS instance that runs CentOS 7.3 64-bit.

2.  Run the following command:

    1.  Install Elasticsearch Curator:

        ```
        pip install elasticsearch-curator
        ```

        **Note:** 

        -   We recommend that you install Elasticsearch Curator 5.6.0. This version supports Alibaba Cloud Elasticsearch 5.5.3 and 6.3.2.
        -   [Curator and Elasticsearch version compatibility](https://www.elastic.co/guide/en/elasticsearch/client/curator/5.6/version-compatibility.html).
    2.  View the version of the Curator:

        ```
        curator --version
        ```

        Returned version information:

        ```
        curator, version 5.6.0
        ```


## Singleton command line interface {#section_avy_vjt_zgb .section}

-   You can use `curator_cli` to perform an action.

-   [Singleton command line interface](https://www.elastic.co/guide/en/elasticsearch/client/curator/5.6/singleton-cli.html).

    **Note:** 

    -   You can perform only one action each time.
    -   Not all of the actions can be performed by using Curator, for example, Alias and Restore.

## Schedule tasks using Crontab {#section_pl1_yjt_zgb .section}

You can use the crontab and curator commands to schedule a task to perform multiple actions.

Curator command:

```
curator [OPTIONS] ACTION_FILE
Options:
  --config PATH  Path to configuration file.  Default: ~/.curator/curator.yml
  --dry-run      Do not perform any changes. 
  --version      Show the version and exit. 
  --help         Show this message and exit. 
```

-   When you run the curator command, you must specify the [config.yml file \(official reference\)](https://www.elastic.co/guide/en/elasticsearch/client/curator/current/configfile.html).

-   When you run the curator command, you must specify the [action.yml file \(official reference\)](https://www.elastic.co/guide/en/elasticsearch/client/curator/current/actionfile.html).


## Hot-warm architecture practice {#section_jml_1kt_zgb .section}

[Use Curator to migrate indexes from hot nodes to warm nodes \(official reference\)](https://www.elastic.co/blog/hot-warm-architecture-in-elasticsearch-5-x).

## Migrate indexes from hot nodes to warm nodes {#section_tk2_bkt_zgb .section}

1.  Create the **config.yml** file in the /usr/curator/ path as follows:

    **Note:** 

    -   hosts: Replace hosts with the address of the Alibaba Cloud Elasticsearch instance that you need to access. In this example, the private address of the Elasticsearch instance is used.
    -   http\_auth: Replace http\_auth with the username and password that are used to log on to the Alibaba Cloud Elasticsearch instance.
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

2.  Create the **action.yml** file in the /usr/curator/ path as follows:

    **Note:** 

    -   The following content migrates indexes created 30 minutes ago and starting with logstash- from **hot** nodes to **warm** nodes.
    -   You can customize the following content based on your business needs.
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

3.  Check whether the curator command can run normally:

    ```
    curator --config /usr/curator/config.yml /usr/curator/action.yml
    ```

    The following information is returned when the command runs normally:

    ```
    2019-02-12 20:11:30,607 INFO      Preparing Action ID: 1, "allocation" 
    2019-02-12 20:11:30,612 INFO      Trying Action ID: 1, "allocation": Apply shard allocation filtering rules to the specified indices 
    2019-02-12 20:11:30,693 INFO      Updating index setting {'index.routing.allocation.require.box_type': 'warm'} 
    2019-02-12 20:12:57,925 INFO      Health Check for all provided keys passed. 
    2019-02-12 20:12:57,925 INFO      Action ID: 1, "allocation" completed. 
    2019-02-12 20:12:57,925 INFO      Job completed.
    ```

4.  Run the crontab command to run the curator command at an interval of 15 minutes:

    ```
    */15 * * * * curator --config /usr/curator/config.yml /usr/curator/action.yml
    ```


