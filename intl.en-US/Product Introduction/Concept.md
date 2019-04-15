# Concept {#concept_nfk_kt2_zgb .concept}

## Cluster {#section_lmc_wbf_zgb .section}

A cluster consists of one or more nodes. Each cluster has a master node, which is automatically polled by the cluster. Master and subordinate nodes are in the scope of a cluster. Elasticsearch applies a decentralized model. It does not have a central node. Therefore, communicating with a node in a cluster is the same as communicating with the cluster.

## Shards {#section_mq5_xbf_zgb .section}

Elasticsearch divides an index into multiple shards and distributes these shards across nodes, allowing you to search by using the index on any of the nodes. The number of shards for an index must be specified before the index has been created. After an index has been created, you can no longer change the number of shards for the index.

## Replicas {#section_hfc_zbf_zgb .section}

You can create multiple index replicas to enhance the fault tolerance of the system. A replica can be restored to a shard when the shard has been damaged or lost. Using replicas also improves the search performance. Search requests can be load balanced by Elasticsearch among these replicas.

## Recovery {#section_h4l_zbf_zgb .section}

Data recovery \(or data redistribution\) is the process of redistributing shards for a node to guarantee the integrity of data when the node joins or leaves a cluster, or when the node recovers from a failure.

## Gateway {#section_qq5_zbf_zgb .section}

A gateway is used to store snapshots of indexes. By default, an Elasticsearch node stores all the indexes in memory. When the node memory is full, the node saves the indexes to local disks for persistent storage. Index snapshots stored on a gateway can be restored after a cluster restarts for fault recovery, which is faster than reading indexes from local disks. Elasticsearch supports multiple types of gateways, including local file system, distributed file system, Hadoop HDFS, and Alibaba Cloud Object Storage Service \(OSS\).

## discovery.zen {#section_e5z_zbf_zgb .section}

discovery.zen is an automatic node discovery mechanism. Elasticsearch is a peer to peer \(P2P\) system that broadcasts to discover nodes. Nodes communicate with each other through multicast and P2P.

## Transport {#section_ozk_dcf_zgb .section}

Transport is a method used for communication between nodes within a cluster, or between clusters and clients. By default, nodes communicate with each other over TCP. Elasticsearch also supports multiple transmission protocol plug-ins, including HTTP \(JSON format\), Thrift, Servlet, Memcached, and ZeroMQ.

