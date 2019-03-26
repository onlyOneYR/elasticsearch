# Online operation and maintenance {#concept_qsc_1sl_zgb .concept}

Elasticsearch cluster provides many statistics, in which the cluster health is the most important index. Cluster health has three statuses: **Red**, **Yellow**, and **Green**.

Cluster health can be checked using the following command:

`curl -u user name:password http://domain:9200/_cluster/health`

## Cluster health {#section_iv1_qvl_zgb .section}

|Color|Status|Remarks|
|-----|------|-------|
|Red|Some major fragments are unavailable.|unavailable The cluster contains unavailable major fragments, meaning that one or more indexes have major fragments `unassigned`.|
|Yellow|All major fragments are available, but some replicated fragments cannot be used.|One or more replicated indexes have major fragments `unassigned`.|
|Green|All major and replicated fragments are available|All indexes of the cluster are healthy and all fragments are `assigned`.|

**Note:** 

To ensure that your Elasticsearch cluster status is **Green**, all **major** and **replicated** fragments must be always available.

## Troubleshooting {#section_oyn_tvl_zgb .section}

**Cluster status is yellow**

If the cluster is in the **Yellow** state, the **password change** or **upgrade** operation will take a long time.

You are suggested to perform the operation when the cluster health is in the **Green** state. The reason why the cluster health status is **Yellow** is that some replicated fragments of indexes are **unassigned**. You need to check the problematic indexes in the cluster.

**Index status query command**

```
curl -u user name:password http://domain:9200/_cat/indices
# Find out the problematic index name. If the reason is that the number_of_replicas is larger than amount_Node – 1,
# change the number_of_replicas of the problematic index.
```

**Index status recovery command**

```
curl -XPUT -u user name:password http://domain:9200/problematic index name/_settings -H 'Content-Type: application/json' -d '{"index":{"number_of_replicas":(amount_Node – 1)}'
# For example, if the number of requested instance nodes is 3 but the number_of_replicas of an index is also 3, the cluster health status is yellow.
# Change the number_of_replicas of the problematic index to 2.
```

**Note:** 

After finishing the operations \(restart/scale-up/custom setting\) on the instance, set the number\_of\_replicas according to the number of instance nodes. This improves the reliability and stability of the Elasticsearch service.

