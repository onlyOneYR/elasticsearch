# Snapshots and restore {#concept_b3z_3ts_zgb .concept}

You can call the snapshot operation to back up your Alibaba Cloud Elasticsearch cluster. The snapshot operation retrieves the current status and data of the cluster, and then saves them to a shared repository. The backup process is intelligent.

The first snapshot is a full copy of the cluster. Subsequent snapshots only save the difference between the existing snapshots and the new data. Therefore, when you create new snapshots, Elasticsearch only needs to add data to or delete data from the backups. This means that it will be much faster to create subsequent snapshots than creating the first snapshot.

**Note:** The <1\>, <2\>, and <3\> tags in this topic are markers used for code description purposes. Remove these tags when you run the code.

## Prerequisites {#section_3jz_shu_dhn .section}

Before you create a snapshot for an Alibaba Cloud Elasticsearch cluster, you must first [Activate OSS](../../../../reseller.en-US/Quick Start/Activate OSS.md#) and [create an OSS bucket](../../../../reseller.en-US/Quick Start/Create a bucket.md#). The OSS bucket must be Standard because Archive type OSS buckets are not supported. You must create the OSS bucket and Elasticsearch instance in the same **region**.

![Create an OSS bucket](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134303/156524532649122_en-US.png)

## Create a repository {#section_kfz_3my_zgb .section}

``` {#codeblock_6wp_vp8_59a}
PUT _snapshot/my_backup
{
   "type": "oss",
    "settings": {
        "endpoint": "http://oss-cn-hangzhou-internal.aliyuncs.com", <1>
        "access_key_id": "xxxx",
        "secret_access_key": "xxxxxx",
        "bucket": "xxxxxx", <2>
        "compress": "true",
        "base_path": "snapshot/" <3>
    }
}
```

-   <1\>: `endpoint` specifies the intranet endpoint of the OSS bucket. For more information, see **Intranet endpoint for ECS access** in [Regions and endpoints](../../../../reseller.en-US/Developer Guide/Endpoint/Regions and endpoints.md#).
-   <2\>: the name of the OSS bucket. The OSS bucket must exist.
-   <3\>: the base\_path field specifies the path of the repository. The default is the root directory.

## Set the shard size {#section_ft5_mmy_zgb .section}

When you need to upload a large amount of data to an OSS bucket, you can set the shard size to divide the data into multiple shards and then upload them to the OSS bucket.

``` {#codeblock_u0q_smi_gax}
POST _snapshot/my_backup/ <1>
{
    "type": "oss",
    "settings": {
        "endpoint": "http://oss-cn-hangzhou-internal.aliyuncs.com",
        "access_key_id": "xxxx",
        "secret_access_key": "xxxxxx",
        "bucket": "xxxxxx",
        "chunk_size": "500mb",
        "base_path": "snapshot/" <2>
    }
}
```

-   <1\>: call the POST method instead of the PUT method. The POST method updates the repository settings.
-   <2\>: the base\_path field specifies the path of the repository. The default is the root directory.

## Query repository information {#section_bf5_4my_zgb .section}

``` {#codeblock_1wt_nbw_9kj}
GET _snapshot
```

You can call `GET _snapshot/my_backup` to query information of a specified repository.

## Migrate a snapshot to an Elasticsearch cluster {#section_jzv_rmy_zgb .section}

Follow these steps to migrate a snapshot to an Elasticsearch cluster.

1.  Back up a snapshot to OSS.
2.  Create a snapshot repository on the target cluster. The repository must use the OSS bucket that stores the snapshot.
3.  Set the `base_path` field to the path of the snapshot.
4.  Call the restore operation.

## Create a snapshot for all open indexes {#section_yg3_smy_zgb .section}

A repository stores multiple snapshots. Each snapshot is a copy of the indexes on the cluster. You can create a snapshot for one or more specified indexes, or all indexes. When you create a snapshot, make sure that the snapshot name is unique.

## Snapshot operations {#section_zvw_smy_zgb .section}

The following is a basic snapshot operation:

``` {#codeblock_r0q_ob9_l81}
PUT _snapshot/my_backup/snapshot_1
```

This operation creates the `snapshot_1` snapshot for all open indexes. The snapshot is saved to the `my_backup` repository. After you call this operation, the result is returned immediately. The snapshot creation process is running in the background.

If you want Elasticsearch to return the result after it creates the snapshot, add the `wait_for_completion` parameter as follows:

``` {#codeblock_m5r_fw1_08z}
PUT _snapshot/my_backup/snapshot_1? wait_for_completion=true
```

This operation does not return the result until the snapshot is created. This process can be time-consuming when you create a snapshot for large indexes.

## Create a snapshot for the specified indexes {#section_osn_xmy_zgb .section}

By default, a snapshot contains all open indexes. For Kibana, due to the disk space limit, you may want to ignore all diagnosis indexes \(the `.kibana` indexes\) when you create a snapshot. To perform this task, create a snapshot for the specified indexes as follows:

``` {#codeblock_9ka_gb6_h5k}
PUT _snapshot/my_backup/snapshot_2
{
    "indices": "index_1,index_2"
}
```

In this example, only the `index1` and `index2` indexes are backed up.

## Query snapshot information {#section_u3k_cny_zgb .section}

In some cases, you may need to query the snapshot information. For example, a snapshot name containing a date is hard to remember, such as `backup_2014_10_28`.

To query the information of a snapshot, send a `GET` request that contains the repository name and snapshot ID.

``` {#codeblock_cea_or0_use}
GET _snapshot/my_backup/snapshot_2
```

The response contains detailed information of the snapshot:

``` {#codeblock_vvu_z16_xby}
{
"snapshots": [
   {
      "snapshot": "snapshot_2",
      "indices": [
         ".marvel_2014_28_10",
         "index1",
         "index2"
      ],
      "state": "SUCCESS",
      "start_time": "2014-09-02T13:01:43.115Z",
      "start_time_in_millis": 1409662903115,
      "end_time": "2014-09-02T13:01:43.439Z",
      "end_time_in_millis": 1409662903439,
      "duration_in_millis": 324,
      "failures": [],
      "shards": {
         "total": 10,
         "failed": 0,
         "successful": 10
      }
   }
]
}
```

You can replace the snapshot ID in the operation with `_all` to query all snapshots in the repository:

``` {#codeblock_rki_dme_c2k}
GET _snapshot/my_backup/_all
```

## Delete a snapshot {#section_t34_cpy_zgb .section}

You can specify a repository name and snapshot ID, and send a `DELETE` request to delete the specified snapshot as follows:

``` {#codeblock_rgf_05t_bco}
DELETE _snapshot/my_backup/snapshot_2
```

**Note:** 

-   You must use only the delete operation to delete snapshots. Do not manually or use other methods to delete snapshots. A snapshot is associated with other backup files. Some of the files are also used by other snapshots. The delete operation does not delete files that are still used by other snapshots. It only deletes files that are associated with the deleted snapshot and are not used by other snapshots.
-   If you choose to manually delete a snapshot, you may delete all files that are associated with the snapshot by mistake. This may cause data loss.

## Monitor snapshot progress {#section_aqk_jpy_zgb .section}

The `wait_for_completion` parameter provides the simplest method for you to monitor the progress of a snapshot process. However, this parameter is not suitable for snapshot processes running for medium-size Elasticsearch clusters. You can call the following operations to query detailed information about a snapshot:

-   Specify a snapshot ID and send a `GET` request.

    ``` {#codeblock_tsm_mpb_odu}
    GET _snapshot/my_backup/snapshot_3
    ```

    If Elasticsearch is still creating the snapshot when you call this operation, the operation returns the progress information, such as the time when the snapshot creation process started and the duration.

    **Note:** The monitor snapshot progress operation shares the same thread pool with the snapshot creation operation. Therefore, if a snapshot is being created on large shards, the monitor snapshot progress operation has to wait until the snapshot creation operation releases the resources in the thread pool.

-   Call the \_status operation to query the snapshot status.

    ``` {#codeblock_6ib_l6v_wji}
    {
    "snapshots": [
       {
          "snapshot": "snapshot_3",
          "repository": "my_backup",
          "state": "IN_PROGRESS", <1>
          "shards_stats": {
             "initializing": 0,
             "started": 1, <2>
             "finalizing": 0,
             "done": 4,
             "failed": 0,
             "total": 5
          },
          "stats": {
             "number_of_files": 5,
             "processed_files": 5,
             "total_size_in_bytes": 1792,
             "processed_size_in_bytes": 1792,
             "start_time_in_millis": 1409663054859,
             "time_in_millis": 64
          },
          "indices": {
             "index_3": {
                "shards_stats": {
                   "initializing": 0,
                   "started": 0,
                   "finalizing": 0,
                   "done": 5,
                   "failed": 0,
                   "total": 5
                },
                "stats": {
                   "number_of_files": 5,
                   "processed_files": 5,
                   "total_size_in_bytes": 1792,
                   "processed_size_in_bytes": 1792,
                   "start_time_in_millis": 1409663054859,
                   "time_in_millis": 64
                },
                "shards": {
                   "0": {
                      "stage": "DONE",
                      "stats": {
                         "number_of_files": 1,
                         "processed_files": 1,
                         "total_size_in_bytes": 514,
                         "processed_size_in_bytes": 514,
                         "start_time_in_millis": 1409663054862,
                         "time_in_millis": 22
                      }
                   },
                   ...
    ```

    -   <1\>: the status of the snapshot. If a snapshot is in progress, the field shows `IN_PROGRESS`.
    -   <2\>: indicates the number of shards that are being transmitted. When value 1 is returned, this indicates that a shard of the snapshot is being transmitted. The other four shards have been transmitted.

        The `shards_stats` list contains the status of the snapshot and statistics about each index and shard. This allows you to learn detailed information about the snapshot progress. A shard can be in one of the following states:

        -   `INITIALIZING`: the shard is verifying the status of the cluster to confirm whether the shard can be snapshotted. Typically, this process is fast.
        -   `STARTED`: data is being transmitted to the repository.
        -   `FINALIZING`: the data transmission process is completed. The shard is sending snapshot metadata.
        -   `DONE`: the snapshot is created.
        -   `FAILED`: an error occurred during the snapshot process. The shard, index, or snapshot cannot be completed. You can check the log for more information.

## Cancel a snapshot {#section_g11_2qy_zgb .section}

To cancel a snapshot, you can call the following operation when the snapshot is in progress:

``` {#codeblock_kl8_v63_sro}
DELETE _snapshot/my_backup/snapshot_3
```

This operation stops the snapshot process and then deletes the snapshot in progress from the repository.

## Restore from a snapshot {#section_rl2_3qy_zgb .section}

To restore indexes from a snapshot, call the [Create a repository](#section_kfz_3my_zgb) operation on the Elasticsearch instance that you want to restore the indexes to. You can choose one of the following methods to restore indexes from a snapshot:

-   To restore indexes from a specified snapshot, append the `_restore` parameter to the snapshot ID and call the operation as follows:

    ``` {#codeblock_u46_bpo_8jp}
    POST _snapshot/my_backup/snapshot_1/_restore
    ```

    By default, the operation restores all indexes in the snapshot. For example, if the `snapshot_1` snapshot contains five indexes, all these indexes will be restored to the Elasticsearch cluster. You can also reference [Create a snapshot for the specified indexes](#) and specify the indexes that you want to restore.

-   Restore the specified indexes and rename the indexes. Use this method when you want to restore the former data to verify or process its content without overwriting the existing data.

    ``` {#codeblock_az3_drw_hfo}
    POST /_snapshot/my_backup/snapshot_1/_restore
    {
     "indices": "index_1", <1>
     "rename_pattern": "index_(.+)", <2>
     "rename_replacement": "restored_index_$1" <3>
    }
    ```

    In this example, the `index_1` index is restored to your Elasticsearch cluster and renamed as `restored_index_1`.

    -   <1\>: only restore the `Index_1` index in the snapshot.
    -   <2\>: search for indexes that are being restored and match the provided pattern.
    -   <3\>: rename the matching indexes.
-   If you want the operation to return the result after the restore process is complete, add the `wait_for_completion` parameter as follows:

    ``` {#codeblock_7vc_qh3_lkk}
    POST _snapshot/my_backup/snapshot_1/_restore? wait_for_completion=true
    ```

    The \_restore operation returns the result immediately. The restoration process is running in the background. If you want the operation to return the result after the restore process is complete, add the `wait_for_completion` parameter.


## Monitor restore operations {#section_z5z_tqy_zgb .section}

**Note:** Restoring data from a repository applies the existing restoration mechanism in Elasticsearch. Restoring shards from a repository is the same as restoring data from a node.

You can call the recovery operation to monitor the restore operations.

-   Monitor a specified index that is being restored.

    ``` {#codeblock_a2w_v5w_7x6}
    GET restored_index_3/_recovery
    ```

    The recovery operation is a general-purpose operation that shows the status of the shards that are being transmitted to your cluster.

-   Monitor all indexes on the cluster. This may include shards that are irrelevant to the restore operation:

    ``` {#codeblock_rmo_2fo_kvo}
    GET /_recovery/
    ```

    The returned result can be verbose depending on the activity of your cluster. The returned result is as follows:

    ``` {#codeblock_6ow_hsd_tqe}
    {
    "restored_index_3" : {
     "shards" : [ {
       "id" : 0,
       "type" : "snapshot", <1>
       "stage" : "index",
       "primary" : true,
       "start_time" : "2014-02-24T12:15:59.716",
       "stop_time" : 0,
       "total_time_in_millis" : 175576,
       "source" : { <2>
         "repository" : "my_backup",
         "snapshot" : "snapshot_3",
         "index" : "restored_index_3"
       },
       "target" : {
         "id" : "ryqJ5lO5S4-lSFbGntkEkg",
         "hostname" : "my.fqdn",
         "ip" : "10.0.1.7",
         "name" : "my_es_node"
       },
       "index" : {
         "files" : {
           "total" : 73,
           "reused" : 0,
           "recovered" : 69,
           "percent" : "94.5%" <3>
         },
         "bytes" : {
           "total" : 79063092,
           "reused" : 0,
           "recovered" : 68891939,
           "percent" : "87.1%"
         },
         "total_time_in_millis" : 0
       },
       "translog" : {
         "recovered" : 0,
         "total_time_in_millis" : 0
       },
       "start" : {
         "check_index_time" : 0,
         "total_time_in_millis" : 0
       }
     } ]
    }
    }
    ```

    -   <1\>: the `type` field indicates the type of the restore operation. The value `snapshot` indicates that the shard is being restored from a snapshot.
    -   <2\>: the `source` field indicates the source snapshot and repository.
    -   <3\>: the `percent` field indicates the progress of the restore operation. The value `94.5%` indicates that 94.5% of the shard files have been restored.
    The output lists all indexes that are being restored and the shards in these indexes. Each shard has statistics about the start or stop time, duration, restoration progress, and bytes transmitted.


## Cancel a restore operation {#section_m2m_2ry_zgb .section}

To cancel a restore operation, you only need to delete the indexes that are being restored. A restore operation is a shard restore process. You can call the DELETE operation to modify the status of the cluster to cancel the restore process.

``` {#codeblock_h42_bqj_mj2}
DELETE /restored_index_3
```

If the `restored_index_3` index is being restored, this operation stops the restore process and deletes the data that has been restored to the cluster.

For more information, see [Snapshot And Restore](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/modules-snapshots.html).

