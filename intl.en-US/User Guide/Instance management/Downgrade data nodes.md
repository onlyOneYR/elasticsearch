# Downgrade data nodes {#concept_221095 .concept}

You can only downgrade data nodes in an Alibaba Cloud Elasticsearch instance that uses the Pay-As-You-Go billing method and is deployed in one zone. You cannot downgrade data nodes in an instance that uses the Subscription billing method or that is deployed across zones. Currently, Alibaba Cloud Elasicsearch only supports removing data nodes from an Alibaba Cloud Elasticsearch instance. The specification and disk capacity of dedicated master nodes, client nodes, and Kibana nodes cannot be downgraded.

## Procedure {#section_pgc_fzz_51j .section}

1.  Log on to the Alibaba Cloud Elasticsearch console, locate the Elasticsearch instance that you need to downgrade data nodes for, and click the instance ID.
2.  On the **Basic Information** tab page, click **Downgrade Data Nodes**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/188515/155834389845736_en-US.png)

3.  On the **Downgrade Data Nodes** page, select Data Node, and then specify the data nodes to be downgraded.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/188515/155834389845741_en-US.png)

    **Note:** For data security, make sure that no data is stored on these data nodes. If the data nodes still contain data, click **Data Migration Tool** to migrate the data. After the data migration process is complete, no index data is stored on the data nodes. New index data is not written into these data nodes.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/188515/155834389845748_en-US.png)

    You can choose the smart migration or custom migration method to migrate the data:

    -   **Smart migration** 

        The system will automatically select the data nodes to be downgraded for you. You must select the check box to agree to the terms of data migration, and then click OK.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/188515/155834389845779_en-US.png)

    -   **Custom migration** 

        You need to manually specify the data nodes to be downgraded on the **Custom Migration** page, select the check box to agree to the terms of data migration, and click OK.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/188515/155834389845780_en-US.png)


## View the downgrade or data migration progress {#section_ai6_2z9_6iz .section}

You can click the tasks list icon in the upper-right corner of the page to view the progress of the downgrade or data migration process.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/188515/155834389845784_en-US.png)

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/188515/155834389845785_en-US.png)

## Migration rollback {#section_eyb_p4j_fx1 .section}

During the migration process, you can stop the migration task to roll back the migration.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/188515/155834389845787_en-US.png)

## Handle data migration failures {#section_7sf_lwn_nvg .section}

The data migration process is time-consuming. Any cluster status or data changes may result in data migration failures. You can check the **Tasks** list in the upper-right corner to locate the cause. You can perform the following operations when the data migration task fails or after the task is complete:

1.  **Query the IP addresses of the data nodes** 

    You can go to the tasks list or call the Elasticsearch API to query the IP addresses of the data nodes where the data is migrated:

    ``` {#codeblock_gsi_zxe_lpk}
    // Call the following operation to query the cluster configuration
    PUT _cluster/settings
    
    // Sample response
    {
      "transient": {
        "cluster": {
          "routing": {
            "allocation": {
              "exclude": {
                "_ip": "192.168. ***. ***,192.168. ***. ***,192.168. ***. ***"
              }
            }
          }
        }
      }
    }
    						
    ```

2.  **Roll back data nodes** 

    You can call the following operation to roll back data nodes:

    ``` {#codeblock_to6_xni_k3f}
    // To roll back the required data nodes, specify the IP addresses of the data nodes that you do not want to roll back in the API request.
    PUT _cluster/settings
    {
      "transient": {
        "cluster": {
          "routing": {
            "allocation": {
              "exclude": {
                "_ip": "192.168. ***. ***,192.168. ***. ***"
              }
            }
          }
        }
      }
    }
    
    // Roll back all data nodes
    PUT _cluster/settings
    {
      "transient": {
        "cluster": {
          "routing": {
            "allocation": {
              "exclude": {
                "_ip": null
              }
            }
          }
        }
      }
    }
    							
    ```

3.  **Verify the rollback result** 

    You can call the `GET _cluster/settings` operation to confirm the IP addresses of the data nodes. At the same time, you can check whether shards are reallocated to the data nodes to determine the progress of the rollback task.

    To check the status of the data migration or rollback task, call the `GET _cat/shards? v` operation.


## Error messages {#section_tge_6d3_35b .section}

 **Error messages and solutions** 

During the data migration or downgrade process, the system may prompt the following error messages:

-   This operation may cause a shard distribution error or insufficient storage, CPU, or memory resources.

    Cause and solution: after the data migration or downgrade task is complete, the cluster does not have sufficient storage, memory, or CPU resources to store the system data or handle the workload. Call the `GET _cat/indices? v` operation to check whether the number of index replicas in the cluster exceeds the number of data nodes after the cluster is scaled. You also need to check whether the storage, memory, or CPU resources are sufficient to store the existing data or handle the requests.

-   The cluster is running tasks or in an error status. Try again later.

    Cause and solution: call the `GET _cluster/health` operation to check the health status of the cluster or go to the **Intelligent Maintenance** page to verify the cause.

-   The nodes in the cluster contain data. You must migrate the data first.
-   The number of nodes that you reserve must be greater than two and greater than half of the existing nodes.

    Cause and solution: to ensure the reliability of the cluster, the number of reserved nodes must be greater than 2. To ensure the stability of the cluster, the number of data nodes specified for data migration or downgrading must be no greater than half of the existing data nodes.

-   The current Elasticsearch cluster configuration does not support this operation. Check the Elasticsearch cluster configuration first.

    Cause and solution: call the `GET _cluster/settings` operation to query the cluster configuration and check whether the cluster configuration contains settings that forbid data allocation.


 **auto\_expand\_replicas** 

Some users may use the permission management function supported by X-Pack. In former Elasticsearch versions, this function applies the `"index.auto_expand_replicas" : "0-all"` setting to indexes .security and .security-6 by default. This causes data migration or downgrading failures. We recommend that you modify the auto\_expand\_replicas option as follows:

``` {#codeblock_qsu_mmr_z3h}
// Query the index configuration
GET .security/_settings

// Returned results
{
  ".security-6" : {
    "settings" : {
      "index" : {
        "number_of_shards" : "1",
        "auto_expand_replicas" : "0-all",
        "provided_name" : ".security-6",
        "format" : "6",
        "creation_date" : "1555142250367",
        "priority" : "1000",
        "number_of_replicas" : "9",
        "uuid" : "9t2hotc7S5OpPuKEIJ****",
        "version" : {
          "created" : "6070099"
        }
      }
    }
  }
}

// Use one of the following methods to modify the auto_expand_replicas setting
PUT .security/_settings
{
  "index" : {
    "auto_expand_replicas" : "0-1"
  }
}

PUT .security/_settings
{
  "index" : {
    "auto_expand_replicas" : "false",
    "number_of_replicas" : 1,
  }
}

// Set the number of replicas based on the actual needs. The number of replicas must be greater than 1 and less than or equal to the number of the available data nodes.
```

