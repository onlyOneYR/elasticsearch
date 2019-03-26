# Snapshot and recovery {#concept_b3z_3ts_zgb .concept}

You can use the snapshot API to back up your Alibaba Cloud Elasticsearch cluster. The API obtains the current status and data of your cluster and saves them to a shared repository. The backup process is "intelligent".

The first snapshot is a complete copy of data, and all subsequent snapshots only save the difference between existing snapshots and the new data. As you create snapshots for data from time to time, the backups are incrementally added and deleted. It means that the number of subsequent backups increases quite fast because only a very small data volume is transmitted.

**Note:** Tags <1\>, <2\>, and <3\> in the code in this article are used to mark the positions for convenient description on the code at the specified position. Remove these tags when running the code.

## Create a repository {#section_kfz_3my_zgb .section}

-   OSS data sources of the standard storage type are recommended. OSS data sources of the archive storage type are not supported.
-   <1\> The OSS data source must be in the same region of your Alibaba Cloud Elasticsearch cluster. Enter the intranet address of the region in the `endpoint` field. For details, see the **intranet endpoint for ECS access** column in [Access domain name and data center](../../../../../intl.en-US/Developer Guide/Endpoint/Regions and endpoints.md#).
-   <2\> An OSS bucket must exist.

```
PUT _snapshot/my_backup
{
    "type": "oss",
    "settings": {
        "endpoint": "http://oss-cn-hangzhou-internal.aliyuncs.com", <1>
        "access_key_id": "xxxx",
        "secret_access_key": "xxxxxx",
        "bucket": "xxxxxx", <2>
        "compress": true
    }
}
```

## Define the shard size {#section_ft5_mmy_zgb .section}

If the size of the data to be uploaded is very large, we can define the shard size during snapshot. If the shard size is exceeded, the data is divided into multiple shards and then uploaded to the OSS instance.

-   <1\> Note that the POST instead of PUT method is set. In this way, the exiting repository settings are updated.
-   <2\> The start position of base\_path to set the repository is the root directory by default.

```
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

## List the repository information {#section_bf5_4my_zgb .section}

```
GET _snapshot
```

You can use `GET _snapshot/my_backup` to obtain information of the specified repository.

## Migrate backup snapshots {#section_jzv_rmy_zgb .section}

To migrate a snapshot to another cluster, you just need to back up the snapshot to the OSS instance register a snapshot respiratory \(in the same OSS instance\) on the new cluster, set the base\_path to the position where the backup file is saved, and then execute the backup restoration command.

## All opened indexes of a snapshot {#section_yg3_smy_zgb .section}

A respiratory can contain multiple snapshots and each snapshot is related to a series of indexes, for example, all indexes, shard of indexes, or a single index. When creating a snapshot, specify an index for the snapshot and create a unique name for the snapshot.

## Snapshot command {#section_zvw_smy_zgb .section}

1.  The following is a most basic snapshot command:

    ```
    PUT _snapshot/my_backup/snapshot_1
    ```

    This command is used to back up all opened indexes to the snapshot named snapshot\_1 in the respiratory my\_backup. The call request is immediately returned and the snapshot is executed at the background.

2.  If you want to wait until the execution finishes, add the tag wait\_for\_completion in the script.

    ```
    PUT _snapshot/my_backup/snapshot_1? wait_for_completion=true
    ```

    Then, the call is blocked until the snapshot execution finishes. If the snapshot size is large, it takes a long time to return the call request.


## Specified indexes of a snapshot {#section_osn_xmy_zgb .section}

All opened indexes are backed up by default. If Kibana is used and you do not want to back up all .kibana indexes related to diagnosis for disk space consideration,

you can back up only the specified indexes when creating snapshots for your cluster:

```
PUT _snapshot/my_backup/snapshot_2
{
    "indices": "index_1,index_2"
}
```

When this snapshot command is run, only index1 and index2 are backed up.

## List the snapshot information {#section_u3k_cny_zgb .section}

Sometimes you may forget details about snapshots in the respiratory, especially when the snapshots are named according to the creation time, for example, `backup_2014_10_28`.

1.  You can initialize a GET request on a respiratory and snapshot name to obtain the information of a single snapshot:

    ```
    GET _snapshot/my_backup/snapshot_2
    ```

    The response contains all the details about the snapshot:

    ```
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

2.  You can use the placeholder \_all to replace the specific snapshot name to obtain a complete list of all snapshots in the respiratory:

    ```
    GET _snapshot/my_backup/_all
    ```


## Delete a snapshot {#section_t34_cpy_zgb .section}

You can initialize an HTTP-based call request on a respiratory/snapshot name through the DELETE API to delete all snapshots that are no longer used:

```
DELETE _snapshot/my_backup/snapshot_2
```

It is important to delete a snapshot through the APIs. Other methods, such as manual deletion, are not supported. Snapshots increase incrementally and many snapshots may depend on historical snapshots. The DELETE API knows which data is still used by recent snapshots and thus only deletes snapshots that are no longer used.

**Note:** If you delete backups manually, there is a risk that the backups may be seriously damaged because the deleted backups may contain data that is still being used.

## Monitor the snapshot task progress {#section_aqk_jpy_zgb .section}

The wait\_for\_completion tag provides the basic monitoring mode. If you want to restore the snapshots of a medium-sized cluster, this mode may be insufficient. The following two APIs provide more details about the snapshot status.

1.  You can run a GET command for a snapshot ID to obtain the information of a specific snapshot:

    ```
    GET _snapshot/my_backup/snapshot_3
    ```

    If this command is run when the snapshot task is undergoing, you can view the start time, running time, and other information about the task.

    **Note:** This API uses the thread pool same as that of the snapshot mechanism. If the request is in a very large shard of the snapshot, the status update interval is large because the API is competing for resources in the same thread pool.

2.  A better way is to drag the data through the \_status API:

    ```
    GET _snapshot/my_backup/snapshot_3/_status
    ```

    The following are detailed statistics returned by the \_status API:

    ```
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
             "failed" : 0
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

    -   <1\> The status of a running snapshot is **IN\_PROGRESS**.
    -   <2\> This specific snapshot still has a shard which is being uploaded. The other four shards have been uploaded.

        The response contains the overall information of the snapshot and statistics on each drilled-down index and shard. The following is a detailed figure about the snapshot task progress. Different shards of the snapshot can be in different states.

        INITIALIZING: The shard is checking the cluster status to see whether a snapshot can be created for it. This process is generally very fast.

        STARTED: The data is being transmitted to the repository.

        FINALIZING: Data transmission finishes and the shard is sending the snapshot metadata.

        DONE: The snapshot task is finished.

        FAILED: An error occurs when the snapshot is being processed, and the shard/index/snapshot task cannot be finished. You can check the log for more information.


## Cancel a snapshot task {#section_g11_2qy_zgb .section}

To cancel a snapshot task, you can run the following command when the task is running:

```
DELETE _snapshot/my_backup/snapshot_3
```

The snapshot process is interrupted. The half-done snapshots in the respiratory are deleted.

## Restoration from a snapshot {#section_rl2_3qy_zgb .section}

1.  If you have backed up the data, you just need to add \_restore to the ID of the snapshot which you want to restore to the cluster:

    ```
    POST _snapshot/my_backup/snapshot_1/_restore
    ```

    All indexes saved in the snapshot are restored by default. If snapshot\_1 contains five indexes, all the five indexes are restored to the cluster. Like the snapshot API, you can select a specific index to be restored.

2.  You can use an additional option to rename the indexes. The option allows you to use a mode to match the index name and rename the index through the restoration process. If you want to restore historical data to verify the content or perform other operations without replacing existing data, this option is very useful. The following is an example about how to restore a single index from a snapshot and rename the index:

    ```
    POST /_snapshot/my_backup/snapshot_1/_restore
    {
     "Indices": "index_1", <1>
     "rename_pattern": "index_(.+)", <2>
     "rename_replacement": "restored_index_$1" <3>
    }
    ```

    The index\_1 is restored to your cluster but is renamed to restored\_index\_1.

    -   <1\> Only index\_1 is restored. Other indexes in the snapshot are neglected.
    -   <2\> Search for indexes being restored that can match the provided mode.
    -   <3\> Rename the indexes to the alternative ones.
3.  Like the snapshot, the restore command is immediately returned and the restoration process runs in the background. If you want your HTTP call request to be blocked until the restoration process is finished, add the `wait_for_completion` tag:

```
POST _snapshot/my_backup/snapshot_1/_restore? wait_for_completion=true
```


## Monitor the restoration operation {#section_z5z_tqy_zgb .section}

The existing restoration mechanism of Elasticsearch is referenced for restoring data from the respiratory. As for internal realization, shard restoration from a respiratory is equivalent to restoration from another node.

To monitor the restoration progress, call the recovery API. This API is for general purpose and is used to display the status of moving shards in your cluster.

1.  This API can be used to independently call a specified restored index:

    ```
    GET restored_index_3/_recovery
    ```

2.  It can also be used to view all indexes in your cluster, including moving indexes that are unrelated to the restoration process:

    ```
    GET /_recovery/
    ```

    The following is an output example. Note that a large quantity of content may be output if your cluster is highly active

    ```
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

    -   <1\> The type field indicates the restoration type. The shard is restored from a snapshot.
    -   <2\> The source field indicates the snapshot and repository from which the shard is restored.
    -   <3\> The percent field indicates the restoration progress. The specified shard is restored by 94% until now. The restoration task will soon be finished. The specified shard is restored by 94% until now. The restoration task will soon be finished.

All indexes under restoration and all shards in these indexes are listed in the output. Statistics on the start/end time, duration, restoration progress in percentage, and number of transmitted bytes, of each shard are displayed.

## Cancel a restoration task {#section_m2m_2ry_zgb .section}

You can delete an index being restored to cancel a restoration task. You can modify the cluster status by calling the DeletIndex API to stop a restoration process. For example:

```
DELETE /restored_index_3
```

If restored\_index\_3 is being restored, after this deletion command is run, the restoration process stops and all the data that has been restored to the cluster is deleted.

