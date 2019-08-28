# Elasticsearch index migration from AWS to Alibaba Cloud {#concept_wjm_dcp_fhb .concept}

This document describes Elasticsearch \(ES\) index migration from AWS to Alibaba Cloud.

A reference architecture diagram is shown in the following figure:

![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527686872268/01.png)

## Introduction {#section_x4j_php_fhb .section}

 **Concepts** 

-   **Elasticsearch**: A distributed, RESTful search and analytics engine capable of solving a growing number of use cases. As the heart of the Elastic Stack, it centrally stores your data so you can discover the expected and uncover the unexpected.
-   **Kibana**: Lets you visualize your Elasticsearch data and navigate the Elastic Stack.
-   **Amazon Elasticsearch Service**: Amazon Elasticsearch Service is a fully managed service that delivers Elasticsearch’s easy-to-use APIs and real-time analytics capabilities alongside the availability, scalability, and security that production workloads require. It’s easy to deploy, secure, operate, and scale Elasticsearch for log analytics, full text search, application monitoring, and more.
-   **Alibaba Elasticsearch Service**: Alibaba Cloud’s Elasticsearch service. In this guide, we explain how to use Elasticsearch through our Alibaba Cloud China site. Have not onboard on International site.
-   **Snapshot and Restore**: You can store snapshots of individual indexes or an entire cluster in a remote repository like a shared file system, S3, or HDFS. These snapshots can be restored relatively quickly, so they are ideal for backup. However, snapshots can only be restored to versions of Elasticsearch that can read the indexes:

    -   A snapshot of an index created in 5.x can be restored to 6.x.
    -   A snapshot of an index created in 2.x can be restored to 5.x.
    -   A snapshot of an index created in 1.x can be restored to 2.x.
    **Note:** 

    Conversely, snapshots of indexes created in 1.x cannot be restored to 5.x or 6.x. Snapshots of indexes created in 2.x cannot be restored to 6.x.

    Snapshots are incremental and can contain indexes created in various versions of Elasticsearch. If any indexes in a snapshot were created in an incompatible version, you will not be able restore the snapshot.


 **Solution overview** 

Elasticsearch \(ES\) indexes can be migrated with following steps:

1.  Create baseline indexes
    1.  Create a snapshot repository and associate it to an AWS S3 Bucket.
    2.  Create the first snapshot of the indexes to be migrated, which is a full snapshot.

        The snapshot will be automatically stored in the AWS S3 bucket created in the first step.

    3.  Create an OSS Bucket on Alibaba Cloud, and register it to a snapshot repository of an Alibaba Cloud ES instance.
    4.  Use the OSSImport tool to pull the data from the AWS S3 bucket into the Alibaba Cloud OSS bucket.
    5.  Restore this full snapshot to the Alibaba Cloud ES instance.
2.  Periodic incremental snapshots

    Repeat serval incremental snapshot and restore.

3.  Final snapshot and service switchover
    1.  Stop services which can modify index data.
    2.  Create a final incremental snapshot of the AWS ES instance.
    3.  Transfer and restore the final incremental snapshot to an Alibaba Cloud ES instance.
    4.  Perform service switchover to the Alibaba Cloud ES instance.

## Prerequisites {#section_vsd_f3p_fhb .section}

-   **Elasticsearch service** 
    -   The version number of AWS ES is 5.5.2, located in the Singapore region.
    -   The version number of Alibaba Cloud ES is 5.5.3, located in Hangzhou.
    -   The demo index name is movies.
-   **Manual Snapshot Prerequisites on AWS** 

    Amazon ES takes daily automated snapshots of the primary index shards in a domain, and stores these automated snapshots in a preconfigured Amazon S3 bucket for 14 days at no additional charge to you. You can use these snapshots to restore the domain.

    You cannot, however, use automated snapshots to migrate to new domains. Automated snapshots are read-only from within a given domain. **To migrate data, you must use manual snapshots stored in your own bucket \(S3 bucket\).**. Standard S3 charges apply for manual snapshots.

    To create and restore index snapshots manually, you must work with IAM and Amazon S3. Verify that you have met the following prerequisites before you attempt to take a snapshot.

    |Prerequisites|Description|
    |-------------|-----------|
    |Create an S3 bucket|Stores manual snapshots for your Amazon ES domain.|
    |Create an IAM role|Delegates permissions to Amazon Elasticsearch Service. The trust relationship for the role must specify Amazon Elasticsearch Service in the Principal statement. The IAM role also is required to register your snapshot repository with Amazon ES. Only IAM users with access to this role may register the snapshot repository.|
    |Create an IAM policy|Specifies the actions that Amazon S3 may perform with your S3 bucket. The policy must be attached to the IAM role that delegates permissions to Amazon Elasticsearch Service. The policy must specify an S3 bucket in a Resource statement.|

    -   **Create an S3 bucket**.

        You need an S3 bucket to store manual snapshots. Make a note of its Amazon Resource Name \(ARN\). You need it for the following:

        -   Resource statement of the IAM policy that is attached to your IAM role.
        -   Python client that is used to register a snapshot repository.
        The following example shows an ARN for an S3 bucket:

        ``` {#codeblock_cok_x11_zxw}
        arn:aws:s3:::eric-es-index-backups
        ```

    -   **Create an IAM role**.

        You must have a role that specifies Amazon Elasticsearch Service, es.amazonaws.com, in a Service statement in its trust relationship, as shown in the following example:

        ``` {#codeblock_xx0_q1a_3rm}
        {
          "Version": "2012-10-17",
          "Statement": [
            {
              "Sid": "",
              "Effect": "Allow",
              "Principal": {
                "Service": "es.amazonaws.com"
              },
              "Action": "sts:AssumeRole"
            }
          ]
        }
        ```

        In the AWS IAM Console, you can find Trust Relationship details here:

        ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527691898319/02.png)

        ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527691909482/03.png)

        When you create an AWS service role by using the IAM Console, Amazon ES is not included in the **Select role type** list. However, you can still create the role by choosing **Amazon EC2**, following the steps to create the role, and then editing the role’s trust relationships to es.amazonaws.com instead of ec2.amazonaws.com.

    -   Create an IAM policy.

        You must attach an IAM policy to the IAM role. The policy specifies the S3 bucket that is used to store manual snapshots for your Amazon ES domain. The following example specifies the ARN of the eric-es-index-backups bucket:

        ``` {#codeblock_9dk_qc7_c5z}
        {
            "Version": "2012-10-17",
            "Statement": [
                {
                    "Action": [
                        "s3:ListBucket"
                    ],
                    "Effect": "Allow",
                    "Resource": [
                        "arn:aws:s3:::eric-es-index-backups"
                    ]
                },
                {
                    "Action": [
                        "s3:GetObject",
                        "s3:PutObject",
                        "s3:DeleteObject"
                    ],
                    "Effect": "Allow",
                    "Resource": [
                        "arn:aws:s3:::eric-es-index-backups/*"
                    ]
                }
            ]
        }
        ```

        You need to paste it in here:

        ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527693813919/04.png)

        You can make sure the policy is correct by looking at the **Policies** \> **Summary**, as follows:![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527693868929/05.png)

    -   **Add an IAM policy for an IAM role**.

        ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527693915659/06.png)


## Registering a manual snapshot directory {#section_cv3_djp_fhb .section}

You must register the snapshot directory with Amazon Elasticsearch Service before you can take manual index snapshots. This one-time operation requires that you sign your AWS request with credentials for one of the users or roles specified in the IAM role’s trust relationship, as described in Section Manual snapshot prerequisites on AWS.

You can’t use curl to perform this operation because it doesn’t support AWS request signing. Instead, use the [Sample Python Client](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/72927/intl_en/1527694314531/Sample%20Python%20Client.docx) to register your snapshot directory.

1.  **Modify the Python client example**.

    Download a copy of the file [Sample Python Client](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/72927/intl_en/1527694314531/Sample%20Python%20Client.docx), then modify the values in yellow in that document to match your real values. Copy the contents of Sample Python Client into a Python file called “snapshot.py” after you have finished editing.

    Sample Python Client:

    |Variable Name|Description|
    |-------------|-----------|
    |region|AWS Region where you created the snapshot repository|
    |host|Endpoint for your Amazon ES domain|
    |aws\_access\_key\_id|IAM credential|
    |aws\_secret\_access\_key|IAM credential|
    |path|Name of the snapshot repository|
    |data: bucket; region;role\_arn|Must include the name of the S3 bucket and the ARN for the IAM role that you created in Section Manual snapshot prerequisites on AWS . To enable server-side encryption with S3-managed keys for the snapshot repository, add `"server_side_encryption": true` to the settings JSON. Important

 If the S3 bucket is in the us-east-1 region, you need to use `"endpoint": "s3.amazonaws.com"` in place of `"region": "us-east-1"`.|

2.  **Install Amazon Web Services Library boto-2.48.0**.

    This sample Python client requires that you install version 2.x of the boto package on the computer where you register your snapshot repository.

    ``` {#codeblock_7ao_qwu_hk3}
    # wget https://pypi.python.org/packages/66/e7/fe1db6a5ed53831b53b8a6695a8f134a58833cadb5f2740802bc3730ac15/boto-2.48.0.tar.gz#md5=ce4589dd9c1d7f5d347363223ae1b970 
    # tar zxvf boto-2.48.0.tar.gz
    # cd boto-2.48.0
    # python setup.py install
    ```

3.  **Run the Python client to register the snapshot repository.**.

    ``` {#codeblock_yw3_qjj_6ch}
    # pyth
    on snapshot.py
    ```

    Registering Snapshot Repository

    Check result in **Kibana** \> **Dev Tools** with request:

    ``` {#codeblock_v37_k77_406}
    GET _snapshot
    ```

    ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527695392939/07.png)


## Snapshot and restore for the first time {#section_ic4_3jp_fhb .section}

1.  **Manually take snapshots on AWS ES**.

    The following commands are all performed on **Kibana** \> **Dev Tools**, you can also perform them using curl from the Linux or Mac OSX command line.

    -   Take a snapshot with the name snapshot\_movies\_1 only for the index movies in the repository eric-snapshot-repository.

        ``` {#codeblock_18b_h2z_m2u}
        PUT _snapshot/eric-snapshot-repository/snapshot_movies_1
        {
        "indexes": "movies"
        }
        ```

    -   Check snapshot status.

        ``` {#codeblock_40y_3w5_8fr}
        GET _snapshot/ eric-snapshot-repository/snapshot_movies_1
        ```

        ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527696490126/08.png)

    -   Check snapshot files on the AWS S3 console.

        ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527696560586/09.png)

2.  **Extract snapshot data from AWS S3 to Alibaba Cloud OSS**.

    In this step, you need to pull snapshot data from your AWS S3 bucket into Alibaba Cloud OSS. For more information, see [Migrate data from Amazon S3 to Alibaba Cloud OSS](../../../../reseller.en-US/Best Practices/Migrate data to OSS/Migrate data from Amazon S3 to Alibaba Cloud OSS.md#).

    After data transfer, check stored snapshot data from the OSS console.

    ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1550653856005/%E5%90%88%E6%88%90.PNG)

3.  **Restore a snapshot to an Elasticsearch instance**.

    Create a snapshot repository

    Perform the following request on **Kibana** \> **Dev Tools** to create a snapshot repository with the same name. Modify values as follows to match your real values.

    ``` {#codeblock_g88_r57_m9f}
    PUT _snapshot/eric-snapshot-repository
    {
    "type": "oss",
    "settings": {
                "endpoint": "http://oss-cn-hangzhou-internal.aliyuncs.com",  
                "access_key_id": "Put your AccessKey id here.",
                "secret_access_key": "Put your secret AccessKey here.",
                "bucket": "eric-oss-aws-es-snapshot-s3",
                "compress": true
          }
    }
    ```

     ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527696940099/011.png)

    After creating the snapshot directory, check the snapshot status for the snapshot named snapshot\_movies\_1, which was assigned in AWS ES manual snapshot step.

    ``` {#codeblock_6tc_xlu_3l4}
    GET _snapshot/eric-snapshot-repository/snapshot_movies_1
    ```

    **Note:** Please record the start time and end time of this snapshot operation: It will be used when you transfer incremental snapshot data with the Alibaba Cloud OSSimport tool. For example:

    “start\_time\_in\_millis”: 1519786844591

    “end\_time\_in\_millis”: 1519786846236

    ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1550565009094/012.png)

4.  **Restore snapshots**.

    Perform the following request on **Kibana** \> **Dev Tools**.

    ``` {#codeblock_31e_efq_8w0}
    POST _snapshot/eric-snapshot-repository/snapshot_movies_1/_restore
    {
        "Indexes": "movies"
    }
    GET movies/_recovery
    ```

    ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527698135558/013.png)

    Check the availability of index movies on **Kibana** \> **Dev Tools**. You can see there exist three records in the index movies, the number of records on your AWS ES instance.![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527698191058/014.png)


## Snapshot and restore for the last time {#section_qkl_wjp_fhb .section}

1.  **Create instance data in aws es movies index**.

    In the previous steps, you know that there are only three records in the index movies, so you insert another two records.

    ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1550564871227/Image%203.png)

    You could also see the number of indexes using this request: `GET movies/_count`.

    ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527698440332/016.png)

2.  **Manually take another snapshot**.

    See the Section Take a snapshot manually on AWS ES, then check the snapshot status:

    ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527698440332/016.png)

    Check the files listed in the S3 bucket:

    ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527698555787/017.png)

    If you check the folder indices, you can also find some differences.

3.  **Extract incremental snapshot data from AWS S3 to Alibaba Cloud OSS**.

    You can use the OSSImport tool to migrate data from S3 to OSS. Because there are 2 snapshot files stored in our S3 bucket now, we try to migrate only new files by modifying the value of isSkipExistFile in the configuration file local\_job.cfg.

    |Filed|Meaning|Description|
    |-----|-------|-----------|
    |isSkipExistFile|Whether to skip the existing objects during data migration, a Boolean value.| If it is set to true, the objects are skipped according to the size and LastModifiedTime.

 If it is set to false, the existing objects are overwritten. The default value is false. This option is invalid when jobType is set to audit.

 |

    After the OSS Import migration job completes, you can see only ‘new’ files are migrated to OSS.

    Alibaba Cloud OSS bucket:

    ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1550653926451/%E5%90%88%E6%88%9002.PNG)

    AWS S3 bucket:

    ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527699274814/020.png)

4.  **Restore an incremental snapshot**.

    You can follow along with the steps from Section Restore snapshots , but the index movies needs to be closed firstly, then you have to restore the snapshot, and open the index movies again after restoration.

    ``` {#codeblock_tgq_npx_u0h}
    POST /movies/_close
    GET movies/_stats
    POST _snapshot/eric-snapshot-repository/snapshot_movies_2/_restore
    {
        "indexes": "movies"
    }
    POST /movies/_open
    ```

    After the restore procedure completes, you can see the count 5 of documents in the index movies is the same as it is in our AWS ES instance.

    ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527699430579/021.png)


## Conclusion {#section_y5x_jwo_e7c .section}

It is possible to migrate AWS Elasticsearch service data to Alibaba Cloud’s Elasticsearch service by the snapshot and restore method.

This solution requires that the AWS ES instance is stopped first to prevent writes and requests during migration

Further reading:

-   [https://www.elastic.co/products/elasticsearch](https://www.elastic.co/products/elasticsearch)
-   [https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html)
-   [https://docs.aws.amazon.com/elasticsearch-service/latest/developerguide/es-managedomains-snapshots.html](https://docs.aws.amazon.com/elasticsearch-service/latest/developerguide/es-managedomains-snapshots.html)
-   [Migrate data from Amazon S3 to Alibaba Cloud OSS](../../../../reseller.en-US/Best Practices/Migrate data to OSS/Migrate data from Amazon S3 to Alibaba Cloud OSS.md#)
-   [Architecture and configuration](../../../../reseller.en-US/Tools/ossimport/Architecture and configuration.md#)

