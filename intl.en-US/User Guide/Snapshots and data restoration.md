# Snapshots and data restoration {#concept_b3z_3ts_zgb .concept}

You can call the snapshot operation to back up your Alibaba Cloud Elasticsearch cluster. The operation retrieves the current status and data of your cluster and saves them to a shared repository. The backup process is intelligent.

The first snapshot is a full copy of the cluster. Subsequent snapshots only save the difference between the existing snapshots and the new data. Therefore, when you create new snapshots, Elasticsearch only needs to add data to or delete data from the backups. This means that it will be much faster to create subsequent snapshots than creating the first snapshot.

**Note:** The <1\>, <2\>, and <3\> tags in this topic are markers used for code description purposes. Remove these tags when you run the code.

## Prerequisites {#section_3jz_shu_dhn .section}

Before you create a snapshot for an Alibaba Cloud Elasticsearch cluster, you must first [Sign up for OSS](../../../../reseller.en-US/Quick Start/Sign up for OSS.md#) and [create an OSS bucket](../../../../reseller.en-US/Quick Start/Create a bucket.md#). The OSS bucket must be Standard because Archive type OSS buckets are not supported. You must make sure that the **region** of the OSS bucket is the same as that of the cluster.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134303/156093512049122_en-US.png)

## Create a repository {#section_kfz_3my_zgb .section}

-   <1\> `endpoint` specifies the intranet endpoint of the OSS bucket. For more information, see **Intranet endpoint for ECS access** in [Regions and endpoints](../../../../reseller.en-US/Developer Guide/Endpoint/Regions and endpoints.md#).
-   <2\> Specify an existing OSS bucket.

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
    }
}
```

## Set the shard size {#section_ft5_mmy_zgb .section}

When you need to upload a large amount of data to the OSS bucket, you can set the shard size to divide the data into multiple shards and then upload them to the OSS bucket.

-   <1\> Call the POST method instead of the PUT method. The POST method updates the repository settings.
-   <2\> base\_path specifies the path of the repository. The default is the root directory.

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

## Query repository information {#section_bf5_4my_zgb .section}

``` {#codeblock_1wt_nbw_9kj}
GET _snapshot
```

You can call `GET _snapshot/my_backup` to query information of a specified repository.

## Restore a snapshot to another cluster {#section_jzv_rmy_zgb .section}

To restore a snapshot to another cluster, you need to save the snapshot to an OSS bucket. Then you need to register a new repository \(same OSS bucket\) on that cluster, set the base\_path parameter to the path of the snapshot, and call the restore operation.

## Create a snapshot for all open indexes {#section_yg3_smy_zgb .section}

A repository stores multiple snapshots. Each snapshot is a copy of the indexes on the cluster. You can create a snapshot for all indexes, specified indexes, or only one index. When you create a snapshot, make sure that the snapshot name is unique.

## Snapshot operations {#section_zvw_smy_zgb .section}

1.  The following is a basic snapshot operation:

``` {#codeblock_tdn_yx5_i70}
PUT _snapshot/my_backup/snapshot_1
```

    This operation creates the snapshot\_1 snapshot for all open indexes. The snapshot is saved to the my\_backup repository. After you call this operation, the result is returned immediately. The snapshot creation process is running in the background.

2.  If you want Elasticsearch to return the result after it creates the snapshot, add the wait\_for\_completion parameter as follows:

``` {#codeblock_gp4_0dh_e2p}
PUT _snapshot/my_backup/snapshot_1? wait_for_completion=true
```

    This operation does not return the result until the snapshot is created. This process can be time-consuming when you create a snapshot for large indexes.


## Create a snapshot for the specified indexes {#section_osn_xmy_zgb .section}

By default, a snapshot contains all open indexes. For Kibana, due to the disk space limit, you may want to ignore all diagnosis indexes \(the .kibana indexes\) when you create a snapshot.

In this case, you can create a snapshot for the specified indexes as follows:

``` {#codeblock_tyn_z6r_fwz}
PUT _snapshot/my_backup/snapshot_2
{
    "indices": "index_1,index_2"
}
```

In this example, only the index1 and index2 indexes are backed up.

## Query snapshot information {#section_u3k_cny_zgb .section}

In some cases, you may need to query the snapshot information. For example, a snapshot name containing a date is hard to remember, such as `backup_2014_10_28`.

1.  You can send a GET request that contains the repository name and snapshot ID to query the information of a snapshot.

``` {#codeblock_fhr_h0y_98y}
GET _snapshot/my_backup/snapshot_2
```

    The response contains detailed information of the snapshot:

    ``` {#codeblock_164_vuz_7z2}
    {
    "snapshots": [
       {
          "snapshot": "snapshot_1",
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

2.  You can replace the snapshot ID in the operation with the placeholder \_all to query all snapshots in the repository:

``` {#codeblock_1qq_0nd_p9i}
GET _snapshot/my_backup/_all
```


## Delete a snapshot {#section_t34_cpy_zgb .section}

You can specify a repository name and snapshot ID, and send a DELETE request to delete the specified snapshot as follows:

``` {#codeblock_aol_oll_se3}
DELETE _snapshot/my_backup/snapshot_2
```

You must only call this operation to delete snapshots. Do not manually or use other methods to delete snapshots. When Elasticsearch deletes a snapshot, it also deletes files that are associated with the deleted snapshot and not used by any other snapshots. The DELETE operation does not delete files that are still used by other snapshots.

**Note:** If you manually delete all files that are associated with the deleted snapshot, data loss may occur.

## Monitor snapshot progress {#section_aqk_jpy_zgb .section}

The wait\_for\_completion parameter provides the simplest method for you to monitor the progress of the snapshot process. However, this parameter is not suitable for snapshot processes running for medium-size Elasticsearch clusters. You can call the following operations to query detailed information of a snapshot.

1.  You can specify a snapshot ID and send a GET request to query the information of a snapshot:

``` {#codeblock_vai_k8i_2hm}
GET _snapshot/my_backup/snapshot_3
```

    If Elasticsearch is still creating the snapshot when you call this operation, the operation returns the progress information, such as the time when the snapshot creation process started and the duration.

    **Note:** This operation uses the same thread pool as the snapshot operation. Therefore, if a snapshot is being created on large shards, this operation must wait a long period of time before returning the result.

2.  To resolve this issue, call the \_status operation:

``` {#codeblock_osg_mc4_ygs}
GET _snapshot/my_backup/snapshot_3/_status
```

    The following shows the detailed snapshot information returned by the \_status operation:

    ``` {#codeblock_a7j_fb3_76v}
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

    -   <1\> If a snapshot is in progress, the field shows **IN\_PROGRESS**.
    -   <2\> Indicates that a shard of the snapshot is still being transmitted. The other four shards have been transmitted.

        The response contains the status of the snapshot and statistics about each index and shard. This allows you to learn detailed information of the snapshot progress. A shard can be in one of the following states:

        INITIALIZING: the shard is verifying the status of the cluster to confirm whether the shard can be snapshotted. In most cases, this process is fast.

        STARTED: data is being transmitted to the repository.

        FINALIZING: the data transmission process is completed. The shard is sending snapshot metadata.

        DONE: the snapshot is created.

        FAILED: an error occurred during the snapshot process. The shard, index, or snapshot cannot be completed. You can check the log for more information.


## Cancel a snapshot {#section_g11_2qy_zgb .section}

To cancel a snapshot, you can call the following operation when the snapshot is in progress:

``` {#codeblock_u7n_ueh_tj2}
DELETE _snapshot/my_backup/snapshot_3
```

This operation stops the snapshot process. This operation then deletes the snapshot in progress from the repository.

## Restore from a snapshot {#section_rl2_3qy_zgb .section}

1.  Snapshots make it easy for you to restore data. You only need to specify a snapshot ID and call the \_restore operation to restore data from a snapshot:

``` {#codeblock_lp6_uop_y04}
POST _snapshot/my_backup/snapshot_1/_restore
```

    By default, the operation restores all indexes in the snapshot. For example, if the snapshot\_1 snapshot contains five indexes, all these indexes will be restored to the Elasticsearch cluster. Same as the snapshot operation, you can specify the indexes that you want to restore.

2.  There is also an additional option for you to rename indexes. This option allows you to set a pattern to match indexes and rename the matching indexes during the restore operation. This option is useful when you want to restore the former data to verify or process its content without overwriting the existing data. The following example shows how to restore an index from a snapshot and rename the index:

``` {#codeblock_zvx_wfx_pbo}
POST /_snapshot/my_backup/snapshot_1/_restore
{
 "indices": "index_1", <1>
 "rename_pattern": "index_(.+)", <2>
 "rename_replacement": "restored_index_$1" <3>
}
```

    In this example, the index\_1 index is restored to your Elasticsearch cluster and renamed as restored\_index\_1.

    -   <1\> Only restore the Index\_1 index in the snapshot.
    -   <2\> Search for indexes that are being restored and match the provided pattern.
    -   <3\> Rename the matching indexes.
3.  Same as the snapshot operation, the restore operation returns the result immediately. The restoration process is running in the background. If you want the operation to return the result after the restore process is complete, add the `wait_for_completion` parameter as follows:

``` {#codeblock_kto_86n_9bs}
POST _snapshot/my_backup/snapshot_1/_restore? wait_for_completion=true
```


## Monitor restore operations {#section_z5z_tqy_zgb .section}

Restoring data from a repository applies the existing restoration mechanism in Elasticsearch. Restoring shards from a repository is the same as restoring data from a node.

If you want to monitor the restore operations, you can call the recovery operation. This is a general-purpose operation that shows the status of the shards that are being transmitted to your cluster.

1.  You can call this operation for a specified index that is being restored.

    ``` {#codeblock_0p6_926_h4x}
    GET restored_index_3/_recovery
    ```

2.  You can also call this operation for all indexes on the cluster. This may include indexes that are not associated with the restore operation:

    ``` {#codeblock_eyz_ofr_e7w}
    GET /_recovery/
    ```

    The returned result can be verbose depending on the activity of your cluster. The returned result is as follows:

    ``` {#codeblock_dgs_71i_87y}
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

    -   <1\> The type field indicates the type of the restore operation. In this example, the shard is restored from a snapshot.
    -   <2\> The source field indicates the source snapshot and repository.
    -   <3\> The percent field indicates the progress of the restore operation. In this example, the progress of the restore operation is 94%. The shard is about to be restored.

The output lists all indexes that are being restored and the shards in these indexes. Each shard has stats about the start or stop time, duration, restoration progress, and bytes transmitted.

## Cancel a restore operation {#section_m2m_2ry_zgb .section}

To cancel a restore operation, you only need to delete the indexes that are being restored. A restore operation is a process for restoring shards. You can call the delete-index operation to modify the status of the cluster to cancel the restore process. Example:

``` {#codeblock_h42_bqj_mj2}
DELETE /restored_index_3
```

If the restored\_index\_3 index is being restored, this operation stops the restore process and deletes the data that has been restored to the cluster.

