# View backup information {#concept_i5d_gts_zgb .concept}

## View automatic backup information {#section_glv_slt_zgb .section}

After enabling automatic backup, you can log on to the Kibana console that has been integrated into Alibaba Cloud Elasticsearch and run the Elasticsearch `snapshot` command in Dev Tools to view snapshots.

## View all snapshots {#section_tdz_slt_zgb .section}

Run the following command to view all the snapshots that are located in the **aliyun\_auto\_snapshot** repository.

`GET _ snapshot/aliyun_auto_snapshot/_ all`

Response:

```
{
  "snapshots": [
    {
      "snapshot": "es-cn-abcdefghijklmn_20180628092236",
      "uuid": "n7YIayyZTm2hwg8BeWbydA",
      "version_id": 5050399,
      "version": "2.0.0",
      "indices": [
        ".kibana"
      ],
      "state": "SUCCESS",
      "start_time": "2018-06-28T01:22:39.609Z",
      "start_time_in_millis": 1530148959609,
      "end_time": "2018-06-28T01:22:39.923Z",
      "end_time_in_millis": 1530148959923,
      "duration_in_millis": 314,
      "failures": [],
      "_shards" : {
        "total":1
        "failed" : 0
        "successful": 1,
      }
    },
    {
      "snapshot": "es-cn-abcdefghijklmn_20180628092500",
      "uuid": "frdl1YFzQ5Cn5xN9ZWuKLA",
      "version_id": 5050399,
      "version": "2.0.0",
      "indices": [
        ".kibana"
      ],
      "state": "SUCCESS",
      "start_time": "2018-06-28T01:25:00.764Z",
      "start_time_in_millis": 1530149100764,
      "end_time": "2018-06-28T01:25:01.482Z",
      "end_time_in_millis": 1530149101482,
      "duration_in_millis": 718,
      "failures": [],
      "_shards" : {
        "total":1
        "failed" : 0
        "successful": 1,
      }
    }
  ]
}
```

-   state: Specifies the status of a snapshot. The snapshot status includes the following:
    -   `IN_PROGRESS`: The snapshot is being restored.
    -   `SUCCESS`: The snapshot has been restored and all shards have been successfully stored.
    -   `FAILED`: The snapshot has been restored with an error. Some data cannot be stored.
    -   `PARTIAL`: The snapshot has been successfully restored to an instance. However, one or more shards cannot be stored.
    -   `INCOMPATIBLE`: The snapshot version is incompatible with the current instance version.

## View specified snapshot {#section_db2_tlt_zgb .section}

Run the following command to view detailed information about the specified snapshot in the **aliyun\_auto\_snapshot** repository.

`GET _ snapshot/aliyun_auto_snapshot/<snapshot>/_ status`

-   `<Snapshot>`: Specifies the name of the snapshot, for example, `Es-cn-abcdefghijklmn_20180628092236`.

Response:

```
{
  "Snapshots": {
    {
      "snapshot": "es-cn-abcdefghijklmn_20180628092236",
      "repository": "aliyun_auto_snapshot",
      "uuid": "n7YIayyZTm2hwg8BeWbydA",
      "state": "SUCCESS",
      "shards_stats": {
        "initializing": 0,
        "started": 0,
        "finalizing": 0,
        "done": 1,
        "failed" : 0
        "total": 2
      },
      "stats": {
        "number_of_files": 4,
        "processed_files": 4,
        "total_size_in_bytes": 3296,
        "processed_size_in_bytes": 3296,
        "start_time_in_millis": 1530148959688,
        "time_in_millis": 77
      },
      "indices": {
        ".kibana": {
          "shards_stats": {
            "initializing": 0,
            "started": 0,
            "finalizing": 0,
            "done": 1,
            "failed" : 0
            "total": 2
          },
          "stats": {
            "number_of_files": 4,
            "processed_files": 4,
            "total_size_in_bytes": 3296,
            "processed_size_in_bytes": 3296,
            "start_time_in_millis": 1530148959688,
            "time_in_millis": 77
          },
          "shards": {
            "0": {
              "stage": "DONE",
              "stats": {
                "number_of_files": 4,
                "processed_files": 4,
                "total_size_in_bytes": 3296,
                "processed_size_in_bytes": 3296,
                "start_time_in_millis": 1530148959688,
                "time_in_millis": 77
              }
            }
          }
        }
      }
    }
  ]
}
```

