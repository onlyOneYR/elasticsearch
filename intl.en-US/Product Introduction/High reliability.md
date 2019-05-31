# High reliability {#concept_wwx_lck_zgb .concept}

## Auto snapshots {#section_o4j_j3k_zgb .section}

Alibaba Cloud Elasticsearch support creating auto snapshots. You can enable this feature on the **Snapshots** page in the console. You can adjust the time when the daily snapshot is automatically created based on your business needs. This feature allows you to back up your data for disaster recovery. For more information, see [Snapshots](../../../../reseller.en-US/User Guide/Instance management/Data backup/Snapshots.md#).

 **Restore snapshots** 

To restore a snapshot, log on to the Alibaba Cloud Elasticsearch console and click View Tutorial on the **Snapshots** page. You will be redirected to [Auto snapshot guide](../../../../reseller.en-US/User Guide/Instance management/Data backup/Auto snapshot guide.md). The topic describes how to restore a snapshot.

**Note:** 

-   Alibaba Cloud Elasticsearch only stores auto snapshots that are created within the last five days.
-   An auto snapshot can only be restored to the Alibaba Cloud Elasticsearch instance where the snapshot is created.

## Store snapshots in Object Storage Service {#section_e5q_j3k_zgb .section}

Alibaba Cloud Elasticsearch instance snapshots can be stored in Object Storage Service \(OSS\). You must create an OSS instance in the same region that the Alibaba Cloud Elasticsearch instance is created. Perform the steps described in **Snapshot and recovery** to run the command to create snapshots and create snapshots on the specified indexes.

You can also run the restore command to restore indexes in a specified snapshot that has been created before. This method allows you to back up your data for disaster recovery. For more information, see [Snapshot and recovery](../../../../reseller.en-US/User Guide/Snapshot and recovery.md).

**Note:** 

-   We recommend that you use OSS to store snapshots. However, OSS Archive buckets are not supported.
-   A snapshot stored in OSS can be restored into another Alibaba Cloud Elasticsearch instance. This instance must be created in the same region as the instance where the snapshot is created.
-   You can run the command to create snapshots or restore indexes in a specified snapshot.
-   A data node can restore 40 MB of data per second, by default. You can perform the steps described in [Snapshot and Restore](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html) to modify the `max_restore_bytes_per_sec` parameter that throttles the restore rate.

## Server Load Balancing {#section_pmb_k3k_zgb .section}

Alibaba Cloud Elasticsearch instances allow you to use Server Load Balancer \(SLB\) to balance the load. To use SLB, you need to specify the internal or public IP address of the Alibaba Cloud Elasticsearch instance in your client. The client can then access the Alibaba Cloud Elasticsearch instance to balance the load.

