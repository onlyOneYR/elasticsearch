# Calculate the amount of resources {#concept_dq4_bmk_zgb .concept}

Before you purchase an Alibaba Cloud Elasticsearch instance, you must estimate and calculate the amount of Elasticsearch resources that you need. Based on testing results and user feedback, Alibaba Cloud offers some common methods to estimate and calculate the amount of Elasticsearch resources. These methods are for reference only.

## Supported disk types {#section_kms_pfl_zgb .section}

This topic applies to Alibaba Cloud Elasticsearch instances that use SSD disks.

## Calculate disk capacity {#section_khy_pfl_zgb .section}

The capacity of disks that are used by Alibaba Cloud Elasticsearch instances is determined based on the following factors:

-   The number of replicas. You must store a minimum of one replica.
-   The index overheads are generally **10%** larger than those of the source data. The index overheads of the **\_all** field are not calculated.
-   Reserved space of the operating system. The operating system reserves **5%** of the disk space for critical processes, system recovery, and disk fragments by default.
-   Elasticsearch overheads. Elasticsearch reserves **20%** of the disk space for internal operations, such as segment merging and logging.
-   A minimum of **15%** of the disk space must be reserved as the security threshold.

Minimum disk space = **Size of source data x 3.4**

```
Total disk space = Size of source data × (1 + Number of replicas) × (1 + Indexing overheads) / (1 - Linux reserved space) / (1 - Elasticsearch overheads) / (1 - Threshold overheads)
= Size of source data × (1 + Number of replicas) × 1.7
= Size of source data × 3.4
```

We recommend that you do not set the **\_all** field unless it is required by your business.

Indexes that have this field enabled generate larger overheads. Based on testing results and user feedback, we recommend that you add extra 50% of the estimated space to the final amount of disk space.

```
Total disk space = Size of source data × (1 + Number of replicas) × 1.7 × (1 + 0.5) 
= Size of source data × 5.1
```

## Determine cluster specifications {#section_ksh_qfl_zgb .section}

The performance of an Alibaba Cloud Elasticsearch cluster is determined by the specifications of the individual Elasticsearch instances in the cluster. Based on testing results and user feedback, we recommend that you determine the specification of your cluster as follows:

`Maximum number of nodes in a cluster = Number of vCPUs per node × 5`

The maximum workload of an Elasticsearch node varies depending on different scenarios. Examples:

-   Data acceleration and query aggregation

    `Maximum workload per node = Memory size per node (GB) × 10`

-   Logging and analysis

    `Maximum workload per node = Memory size per node (GB) × 50`

-   Common scenarios

    `Maximum workload per node = Memory size per node (GB) × 30`


**Cluster specifications**

|Specification|Maximum number of cluster nodes|Maximum memory size per node \(query\)|Maximum memory size per node \(log\)|Maximum memory size per node \(common\)|
|-------------|-------------------------------|--------------------------------------|------------------------------------|---------------------------------------|
|2C 4 GB|10|40 GB|200 GB|100 GB|
|2C 8 GB|10|80 GB|400 GB|200 GB|
|4C 16GB|20|160 GB|800 GB|512 GB|
|8C 32 GB|40|320 GB|1.5 TB|1 TB|
|16C 64 GB|50|640 GB|2 TB|2 TB|

## Calculate shard size {#section_tjp_qfl_zgb .section}

Both the number of shards and the size of each shard contribute to the stability and performance of an Alibaba Cloud Elasticsearch cluster. An Elasticsearch cluster creates five shards for each index by default. However, you may need to modify the default settings to improve performance in most cases.

-   For small Elasticsearch nodes, the size of each shard must be smaller than or equal to 30 GB. For large Elasticsearch nodes, the size of each shard must be smaller than or equal to 50 GB.
-   For log analysis or extremely large indexes, the size of each shard must be smaller than or equal to 100 GB.
-   The number of shards, including replicas, must be equal to the number of nodes or equal to an integer multiple of the number of nodes.
-   We recommend that you create a maximum of five shards for one index on a node.

**Note:** 

The calculation methods described in this topic may vary depending on the data structure, query requirements, data size, performance, and data changes. You must determine the number of shards and calculate the size of the shards based on your actual situation.

We recommend that you run tests and determine a best practice that satisfies your requirements based on the actual data and usage scenario. You can use the **elastic scaling** feature of Alibaba Cloud Elasticsearch to make a plan based on this topic. You can **increase disk size**, **add more nodes**, and **upgrade node specifications** based on your actual needs.

