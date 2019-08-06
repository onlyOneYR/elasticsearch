# High reliability {#concept_wwx_lck_zgb .concept}

This topic introduces the high reliability of Alibaba Cloud Elasticsearch based on auto-creation, restoration, and storage of snapshots and load balancing.

## Auto snapshot {#section_o4j_j3k_zgb .section}

Alibaba Cloud Elasticsearch instances support the auto snapshot feature. You can enable auto snapshot on the Snapshots page in the Alibaba Cloud Elasticsearch console and then set the snapshot creation cycle. Elasticsearch then creates a snapshot daily at the scheduled time. This feature allows you to back up your data for disaster recovery. For more information, see [Snapshots](../../../../reseller.en-US/User Guide/Instance management/Data backup/Snapshots.md#).

 Restore snapshots 

On the Snapshots page of the Alibaba Cloud Elasticsearch console, you can use a specified snapshot to restore data. For more information, see [Auto snapshot guide](../../../../reseller.en-US/User Guide/Instance management/Data backup/Auto snapshot guide.md).

**Note:** 

-   Alibaba Cloud Elasticsearch only stores snapshots that are created within the last three days.
-   A snapshot created by the auto snapshot feature can only be restored to the Alibaba Cloud Elasticsearch instance where the snapshot is created.

## Store snapshots on OSS {#section_e5q_j3k_zgb .section}

Alibaba Cloud Elasticsearch allows you to store the snapshots of your Elasticsearch instance on Alibaba Cloud Object Storage Service \(OSS\). To store snapshots on OSS, you must first purchase the OSS service in the same region as your Elasticsearch instance. You can call the snapshot creation operation to create a snapshot of the specified index data. For more information, see [Snapshots and data restoration](../../../../reseller.en-US/User Guide/Snapshots and data restoration.md#).

 Restore 

Alibaba Cloud Elasticsearch allows you to call the restore operation to restore index data from a specified snapshot. This feature enables support for disaster recovery. For more information, see [Snapshots and data restoration](../../../../reseller.en-US/User Guide/Snapshots and data restoration.md#).

**Note:** 

-   We recommend that you use OSS standard buckets to store snapshots. OSS Archive buckets are not supported.
-   A snapshot stored on OSS can be restored to an Alibaba Cloud Elasticsearch instance in the same region as OSS.
-   You can call the corresponding operation to create a snapshot or restore the index data in a specified snapshot.
-   By default, each Alibaba Cloud Elasticsearch data node can process 40 MB of data per second. You can reference the [Snapshot And Restore](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html) page on the official Elasticsearch site and set the `max_restore_bytes_per_sec` parameter to tune the data processing capability of the data nodes.

## Load balancing {#section_pmb_k3k_zgb .section}

Alibaba Cloud Elasticsearch instances support load balancing. You can specify the public or internal network address of your Elasticsearch instance on your client to access the Elasticsearch instance. Your requests are evenly distributed to all data nodes of the Elasticsearch instance based on load balancing.

**Note:** The load balancing among these data nodes depends on the number and size of index shards. We recommend that you reference [Calculate shard size](../../../../reseller.en-US/Quick Start/Calculate the amount of resources.md#section_tjp_qfl_zgb) and then determine the number and size of the index shards properly when you create indexes.

