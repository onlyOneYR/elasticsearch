# Logstash deployment {#concept_kbr_tbt_zgb .concept}

## Prepare the environment {#section_lbm_5ct_zgb .section}

1.  Buy Alibaba Cloud ES instances and ECS instances that can access self-built clusters and Alibaba Cloud ES. If you already have ECS instances that meet the requirements, there is no need to purchase additional ECS instances. Prepare the JDK of version 1.8 or later.

    The ECS instance on a classic network can be used as long as the ECS instance can access the Alibaba Cloud ES service within VPC through [Classic network errors](../../../../../intl.en-US/FAQ/Classic network errors.md#).

2.  Download Logstash v5.5.3.

    Download the Logstash of the version matching Elasticsearch on the [Elastic website](https://www.elastic.co/downloads/past-releases) \(v5.5.3 is recommended\).

3.  Decompress the downloaded Logstash package.

    ```
    tar -xzvf logstash-5.5.3.tar.gz
    # A stringent configuration file checking feature is added to Elasticsearch later than version 5.x.
    ```


## Test cases {#section_y35_ddt_zgb .section}

1.  Create the user name and password for data access.
    -   Creates a role.

        ```
        curl -XPOST -H "Content-Type: application/json" -u elastic:es-password http://***instanceId***.elasticsearch.aliyuncs.com:9200/_xpack/security/role/***role-name*** -d '{"cluster": ["manage_index_templates", "monitor"],"indices": [{"names": [ "logstash-*" ], "privileges":["write","delete","create_index"]}]}'
        # es-password is the Kibana logon password.
        # ***instanceId*** is the ES instance ID.
        # ***role-name*** is the role name.
        # The default index name of Logstash is in the format of logstash-current date. Therefore, the read and write permissions on the Logstash-* index must be assigned when you add a user role.
        ```

    -   Create a user

        ```
        curl -XPOST -H "Content-Type: application/json" -u elastic:es-password http://***instanceId***.elasticsearch.aliyuncs.com:9200/_xpack/security/user/***user-name*** -d '{"password" : "***logstash-password***","roles" : ["***role-name***"],"full_name" : "***your full name***"}'
        # es-password is the Kibana logon password.
        # ***instanceId*** is the ES instance ID.
        # ***user-name*** is the user name for data access.
        # ***logstash-password*** is the password for data access.
        # ***role-name*** is the role name you created earlier.
        # ***your full name*** is the full name of the current user.
        ```

        **Note:** The role and user can also be created on the Kibana page.

    -   Add a role

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134317/155358863840177_en-US.png)

    -   Add a user

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134317/155358863840178_en-US.png)

2.  Prepare the conf file.

    For more information, see [Configuration file structure](https://www.elastic.co/guide/en/logstash/5.5/configuration-file-structure.html).

    **Example:**

    Create the test.conf file on the ECS instance and add the following configurations:

    ```
    input {
        file {
            path => "/your/file/path/xxx"
            }
    }
    filter {
    }
    output {
      elasticsearch {
        hosts => ["http://***instanceId***.elasticsearch.aliyuncs.com:9200"]
        user => "***user-name***"
        password => "***logstash-password***"
      }
    }
    # ***instanceId*** is the ES instance ID.
    # ***user-name*** is the user name for data access.
    # ***logstash-password*** is the password for data access.
    # Place the user name and password in quotation marks to prevent errors in Logstash startup caused by special characters.
    ```

    **Run**

    Run Logstash according to the conf file:

    ```
    bin/logstash -f path/to/your/test.conf
    # Logstash provides many input, filter, and output plugins. Only simple configurations are required for data transfer.
    This example shows how to obtain file changes through Logstash and submit the changed data to the Elasticsearch cluster. All the new contents in the monitored file can be automatically indexed to the Elasticsearch cluster by Logstash.
    ```


## FAQ {#section_cdf_d2t_zgb .section}

**How to configure the index automatically created by the cluster?**

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134317/155358863840179_en-US.png)

To ensure security during users' data operations, Alibaba Cloud Elasticsearch does not allow automatic creation of indexes by default.

Logstash creates indexes by submitting data in data upload, instead of using the create index API. Therefore, before using Logstash to upload data, allow the automatic creation of indexes.

**Note:** After the setting is changed and confirmed, the Alibaba ES cluster restarts.

**No permissions to create indexes**

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134317/155358863840180_en-US.png)

Check whether the role you created for data access has the write, delete, and create\_index permissions.

**Insufficient memory**

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134317/155358863940181_en-US.png)

By default, Logstash has a **1 GB** memory. If your requested ECS memory becomes insufficient, reduce the memory usage of Logstash by changing the memory settings in config/jvm.options.

**No quotation marks added to the user name and password in test.conf configuration**

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134317/155358863940182_en-US.png)

If the user name or password containing special characters in the test.conf file are not added to quotation marks, the previous error message is displayed.

## Additional instructions {#section_ivq_n2t_zgb .section}

To monitor the Logstash node and collect logs:

-   Install the X-Pack plugin for Logstash. For more information, see [download link](https://artifacts.elastic.co/downloads/packs/x-pack/x-pack-5.5.3.zip).
-   Deploy the X-Pack after download.
-   ```
bin/logstash-plugin install
        file:///path/to/file/x-pack-5.5.3.zip
```

-   Add a Logstash monitor user. Alibaba Cloud Elasticsearch cluster disables the **logstash\_system** user by default. You need to create a user with the role name **logstash\_system**. The user name cannot be **logstash\_system**. The user name can be changed. In this example, the user name is **logstash\_system\_monitor**. The following two methods are recommended for creating users:

-   Create a monitor user through the Kibana module.
    1.  Log on to the Kibana management page, and perform the operations according to the following figure:

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134317/155358863940183_en-US.png)

    2.  Click the **Create User** button.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134317/155358863940184_en-US.png)

    3.  Enter the required information. Save and submit the information.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134317/155358863940185_en-US.png)

-   Add a user through commands

    ```
    curl -u elastic:es-password -XPOST http://***instanceId***.elasticsearch.aliyuncs.com:9200/_xpack/security/user/logstash_system_monitor -d '{"password" : "***logstash-monitor-password***","roles" : ["logstash_system"],"full_name" : "your full name"}'
    # es-password is the Kibana logon password.
    # ***instanceId*** is the ES instance ID.
    # ***logstash-monitor-password*** is the password of logstash_system_monitor.
    ```


