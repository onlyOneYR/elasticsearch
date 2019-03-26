# ES self-built migration {#concept_orx_tbt_zgb .concept}

## Prerequisites {#section_q1d_cht_zgb .section}

You must meet the following requirements to migrate data from a user-created Elasticsearch instance to an Alibaba Cloud Elasticsearch instance.

-   The ECS instance that hosts the user-created Elasticsearch instance must be connected to a VPC network. **ECS instances connected to a VPC network through a ClassicLink are not supported**. The ECS instance and your Alibaba Cloud Elasticsearch instance must be connected to the same VPC network.
-   You can use an ECS instance to run the reindex.sh script. To perform this task, you must make sure that the ECS instance can access port `9200` on the user-created and Alibaba Cloud Elasticsearch instances.
-   **The VPC security group must allow all IP addresses in the IP whitelist to access the ECS instance and port 9200 must be open.**
-   **The VPC security group must allow the IP addresses of all Elasticsearch instance nodes to access the ECS instance. You can view these IP addresses in the Kibana console.**
-   To check whether the ECS instance that runs the script can access port 9200 on the source and target Elasticsearch instances, run the `curl -XGET http://<host>:9200` command on the ECS instance.

## Procedure {#section_pwg_2ht_zgb .section}

1.  Create indexes.
2.  Migrate data.

## Create indexes {#section_q5f_fht_zgb .section}

You must create indexes on the target Elasticsearch instance based on the indexes on the source cluster. You can also choose to enable dynamic index creation and dynamic mapping \(not recommended\) to create indexes on the target cluster. You must enable auto index creation before you enable dynamic index creation.

The following section provides a Python script \(`indiceCreate.py`\). You can copy all the indexes from the source cluster to the target cluster. Only the `number of shards` and `zero replica` are configured. You need to configure the remaining settings.

**Note:** If the following error occurs when you run the cURL command, add the `-H "Content-Type: application/json"` parameter to the command and run the command again.

``{"error":"Content-Type header [application/x-www-form-urlencoded] is not supported","status":406}``

```
// Obtain all the indexes on the source cluster. If you do not have the required permissions, remove the "-u user:pass" parameter. Make sure that you have replaced oldClusterHost with the name of the ECS instance that hosts the source cluster.
  curl -u user:pass -XGET http://oldClusterHost/_cat/indices | awk '{print $3}'
  // Based on the returned indexes, obtain the setting and mapping of the index that you need to migrate for the specified user. Make sure that you have replaced indexName with the index name that you need to query.
  curl -u user:pass -XGET http://oldClusterHost/indexName/_settings,_mapping?pretty=true
  // Create a new index in the target cluster according to the _settings and _mapping settings that you have obtained from the preceding step. You can set the number of index replicas to zero to accelerate the data synchronization process, and change the number to one after the migration has completed.
  // ewClusterHost indicates the ECS instance that hosts the target cluster, testindex indicates the name of the index that you have created, and testtype indicates the type of the index.
  curl -u user:pass -XPUT http://<newClusterHost>/<testindex> -d '{
    "testindex" : {
        "settings" : {
            "number_of_shards" : "5", //Set the number of shards for the corresponding index on the source cluster, for example, 5
            "number_of_replicas" : "0" //Set the number of index replicas to zero
          }
        },
        "mappings" : { //Set the mapping for the index on the source cluster. For example, you can set the mapping as follows
            "testtype" : {
                "properties" : {
                    "uid" : {
                        "type" : "long"
                    },
                    "name" : {
                        "type" : "text"
                    },
                    "create_time" : {
                      "type": "long"
                    }
                }
           }
       }
   }
}'
```

## Accelerate the synchronization process {#section_mzz_kht_zgb .section}

**Note:** If the index is too large, you can set the number of replicas to **0** and the refresh interval to **-1** before migration. After the data has been migrated, set the replicas and refresh settings to the previous values. This accelerates the synchronization process.

```
// You can set the number of index replicas to zero and disable refresh, to accelerate the migration process.
curl -u user:password -XPUT 'http://<host:port>/indexName/_settings' -d' {
        "number_of_replicas" : 0,
        "refresh_interval" : "-1"
}'
// After the data has been migrated, set the number of index replicas to `1` and the refresh interval to `1` (default value, which means 1 second).
curl -u user:password -XPUT 'http://<host:port>/indexName/_settings' -d' {
        "number_of_replicas" : 1,
        "refresh_interval" : "1s"
}'
```

## Data migration {#section_w5r_l3t_zgb .section}

To ensure data consistency after the migration, you must **stop the write operation on the source cluster**. You do not need to stop the read operation. After the migration process has been completed, switch the read and write operations to the target cluster. Data inconsistency may occur if you do not stop the write operation on the source cluster.

**Note:** 

-   When using the following method to migrate data, if you access the source cluster using `an IP address and a port`, you must configure a `reindex` whitelist in the `YML` file of the target cluster, and add the IP address of the source cluster to the whitelist: `reindex.remote.whitelist: 1.1.1.1:9200,1.2. *. *:* *. *:*`
-   If you access the source cluster using a domain name, do not use the `http://host:port/path` format. The domain name must not contain the path.

-   Migrate small amounts of data

    Run the `reindex.sh` script.

    ```
    #! /bin/bash
    # file:reindex.sh
    indexName="The name of the index"
    newClusterUser="The username that is used to log on to the target cluster"
    Newclusterpass = "The password that is used to log on to the target cluster"
    newClusterHost="The ECS instance that hosts the target cluster"
    Oldclusteruser = "The username that is used to log on to the source cluster"
    Oldclusterpass = "The password that is used to log on to the source cluster"
    # The address of the ECS instance that hosts the source cluster must be in this format: [scheme]://[host]:[port]. Example: http://10.37.1.1:9200.
    Oldclusterhost = "The ECS instance that hosts the source cluster"
    curl -u ${newClusterUser}:${newClusterPass} -XPOST "http://${newClusterHost}/_reindex? pretty" -H "Content-Type: application/json" -d'{
        "source": {
            "remote": {
                "host": "'${oldClusterHost}'",
                "username": "'${oldClusterUser}'",
                "password": "'${oldClusterPass}'"
            },
            "index": "'${indexName}'",
            "query": {
                "match_all": {}
            }
        },
        "dest": {
           "index": "'${indexName}'"
        }
    }'
    ```

-   Migrate large amounts of data without delete operations and with update time

    If the amount of data is large without deletion operations, you can use rolling migration to minimize the time period during which your write operation is suspended. Rolling migration requires that your data schema has a time-series attribute that indicates the update time. You can stop the write operation after the data has been migrated, then migrate the incremental data. Switch the read and write operations to the target cluster.

    ```
    #! /bin/bash
    # file: circleReindex.sh
    # CONTROLLING STARTUP:
    # This script is used to remotely rebuild the index using the reindex operation. Requirements:
    #1. You have created the index on the target cluster, or the target cluster supports automatic index creation and dynamic mapping.
    # 2 You must configure an IP whitelist in the YML file of the target cluster: reindex.remote.whitelist: 172.16.123. *:9200
    #3. You need to specify the ECS instance address in the following format: [scheme]://[host]:[port].
    USAGE="Usage: sh circleReindex.sh <count>
           count: The number of executions. A negative number indicates loop execution. You can set this parameter to perform the reindex operation only once or multiple times.
    For example:
            sh circleReindex.sh 1
            sh circleReindex.sh 5
            sh circleReindex.sh -1"
    indexName="The name of the index"
    newClusterUser="The username that is used to log on to the target cluster"
    newClusterPass="The password that is used to log on to the target cluster"
    oldClusterUser="The username that is used to log on to the source cluster"
    oldClusterPass="The password that is used to log on to the source cluster"
    ## http://myescluster.com
    newClusterHost="The host of the target cluster"
    # You need to address of the ECS instance that hosts the source cluster in the following format: [scheme]://[host]:[port]. Example: http://10.37.1.1:9200
    oldClusterHost="The ECS instance that hosts the source cluster"
    timeField="The field that specifies the time window during which the incremental data is migrated"
    reindexTimes=0
    lastTimestamp=0
    curTimestamp=`date +%s`
    hasError=false
    function reIndexOP() {
        reindexTimes=$[${reindexTimes} + 1]
        curTimestamp=`date +%s`
        ret=`curl -u ${newClusterUser}:${newClusterPass} -XPOST "${newClusterHost}/_reindex? pretty" -H "Content-Type: application/json" -d '{
            "source": {
                "remote": {
                    "host": "'${oldClusterHost}'",
                    "username": "'${oldClusterUser}'",
                    "password": "'${oldClusterPass}'"
                },
                "index": "'${indexName}'",
                "query": {
                    "range" : {
                        "'${timeField}'" : {
                            "gte" : '${lastTimestamp}',
                            "lt" : '${curTimestamp}'
                        }
                    }
                }
            },
            "dest": {
                "index": "'${indexName}'"
            }
        }'`
        lastTimestamp=${curTimestamp}
        echo "${reindexTimes} reindex operations have been performed. The last reindex operation is completed at ${lastTimestamp} Result：${ret}"
        if [[ ${ret} == *error* ]]; then
            hasError=true
            echo "An unknown error occurred while performing this operation. All subsequent operations have been suspended."
        fi
    }
    function start() {
        ## A negative number indicates loop execution.
        if [[ $1 -lt 0 ]]; then
            while :
            do
                reIndexOP
            done
        elif [[ $1 -gt 0 ]]; then
            k=0
            while [[ k -lt $1 ]] && [[ ${hasError} == false ]]; do
                reIndexOP
                let ++k
            done
        fi
    }
    ## main 
    if [ $# -lt 1 ]; then
        echo "$USAGE"
        exit 1
    fi
    echo "Start the reindex operation for index ${indexName}"
    start $1
    echo "You have performed ${reindexTimes} reindex operations"
    ```

-   Migrate large amounts of data without deletion operations or update time

    When you need to migrate large amounts of data and no update time field is defined in the mapping, you must add a update time field to the code that is used to access the source cluster. After the field has been added, you can migrate the existing data, and then use rolling migration described in the preceding data migration plan to migrate the incremental data.

    The following script shows how to migrate the existing data without the update time field.

    ```
    #! /bin/bash
    # file:miss.sh
    indexName="The name of the index"
    newClusterUser="The username that is used to log on to the target cluster"
    Newclusterpass = "The password that is used to log on to the target cluster"
    newClusterHost="The ECS instance that hosts the target cluster"
    Oldclusteruser = "The username that is used to log on to the source cluster"
    Oldclusterpass = "The password that is used to log on to the source cluster"
    # The address of the ECS instance that hosts the source cluster must be in this format: [scheme]://[host]:[port]. Example: http://10.37.1.1:9200.
    oldClusterHost="The ECS instance that hosts the source cluster"
    timeField="updatetime"
    curl -u ${newClusterUser}:${newClusterPass} -XPOST "http://${newClusterHost}/_reindex? pretty" -H "Content-Type: application/json" -d '{
        "source": {
            "remote": {
                "host": "'${oldClusterHost}'",
                "username": "'${oldClusterUser}'",
                "password": "'${oldClusterPass}'"
            },
            "index": "'${indexName}'",
            "query": {
                "bool": {
                    "must_not": {
                        "exists": {
                            "field": "'${timeField}'"
                        }
                    }
                }
            }
        },
        "dest": {
           "index": "'${indexName}'"
        }
    }'
    ```

-   Migrate data without suspending the write operation

    This feature will soon be available.


## Use the batch creation operation to replicate indexes from the source cluster {#section_qc2_cjt_zgb .section}

The following Python script shows how to replicate indexes from the source cluster to the target cluster. The default number of newly created index replicas is **0**.

```
#! /usr/bin/env python
# -*- coding: UTF-8 -*-
# File name：indiceCreate.py
import sys
import base64
import time
Import httplib
import json
## The ECS instance that hosts the source cluster (ip+port)
oldClusterHost = "old-cluster.com"
# The username that is used to log on to the source cluster. The username field can be left empty
oldClusterUserName = "old-username"
## The password that is used to log on to the source cluster. The password field can be left empty
oldClusterPassword = "old-password"
## The ECS instance that hosts the target cluster (ip+port)
newClusterHost = "new-cluster.com"
## The username that is used to log on to the target cluster. The username field can be left empty
newClusterUser = "new-username"
## The password that is used to log on to the target cluster. The password field can be left empty
newClusterPassword = "new-password"
DEFAULT_REPLICAS = 0
def httpRequest(method, host, endpoint, params="", username="", password=""):
    conn = httplib.HTTPConnection(host)
    headers = {}
    if (username ! = "") :
        'Hello {name}, your age is {age} !'.format(name = 'Tom', age = '20')
        base64string = base64.encodestring('{username}:{password}'.format(username = username, password = password)).replace('\n', '')
        headers["Authorization"] = "Basic %s" % base64string;
    if "GET" == method:
        Content-Type: application/x-www-form-urlencoded
        conn.request(method=method, url=endpoint, headers=headers)
    else :
        Headers ["Content-Type"] = "application/JSON"
        conn.request(method=method, url=endpoint, body=params, headers=headers)
    response = conn.getresponse()
    res = response.read()
    return res
def httpGet(host, endpoint, username="", password=""):
    return httpRequest("GET", host, endpoint, "", username, password)
def httpPost(host, endpoint, params, username="", password=""):
    return httpRequest("POST", host, endpoint, params, username, password)
def httpPut(host, endpoint, params, username="", password=""):
    return httpRequest("PUT", host, endpoint, params, username, password)
def getIndices(host, username="", password=""):
    endpoint = "/_cat/indices"
    indicesResult = httpGet(oldClusterHost, endpoint, oldClusterUserName, oldClusterPassword)
    indicesList = indicesResult.split("\n")
    indexList = []
    for indices in indicesList:
        if (indices.find("open") > 0):
            indexList.append(indices.split()[2])
    return indexList
def getSettings(index, host, username="", password=""):
    endpoint = "/" + index + "/_settings"
    indexSettings = httpGet(host, endpoint, username, password)
    print index + "  The original settings: \n" + indexSettings
    settingsDict = json.loads(indexSettings)
    ## The number of shards equals the number of indexes on the source cluster by default
    number_of_shards = settingsDict[index]["settings"]["index"]["number_of_shards"]
    ## The default number of replicas is 0
    number_of_replicas = DEFAULT_REPLICAS
    newSetting = "\"settings\": {\"number_of_shards\": %s, \"number_of_replicas\": %s}" % (number_of_shards, number_of_replicas)
    return newSetting
def getMapping(index, host, username="", password=""):
    endpoint = "/" + index + "/_mapping"
    indexMapping = httpGet(host, endpoint, username, password)
    print index + "The original mappings: \n" + indexMapping
    mappingDict = json.loads(indexMapping)
    mappings = json.dumps(mappingDict[index]["mappings"])
    newMapping = "\"mappings\" : " + mappings
    return newMapping
def createIndexStatement(oldIndexName):
    settingStr = getSettings(oldIndexName, oldClusterHost, oldClusterUserName, oldClusterPassword)
    mappingStr = getMapping(oldIndexName, oldClusterHost, oldClusterUserName, oldClusterPassword)
    createstatement = "{\n" + str(settingStr) + ",\n" + str(mappingStr) + "\n}"
    return createstatement
def createIndex(oldIndexName, newIndexName=""):
    if (newIndexName == "") :
        newIndexName = oldIndexName
    createstatement = createIndexStatement(oldIndexName)
    print "new index" + newIndexName + "settings and mappings: \n" + createstatement
    endpoint = "/" + newIndexName
    createResult = httpPut(newClusterHost, endpoint, createstatement, newClusterUser, newClusterPassword)
    print "new index" + newIndexName + "creation result:" + createResult
## main
indexList = getIndices(oldClusterHost, oldClusterUserName, oldClusterPassword)
systemIndex = []
for index in indexList:
    if (index.startswith(".")):
        systemIndex.append(index)
    else :
        createIndex(index, index)
if (len(systemIndex) > 0) :
    for index in systemIndex:
        print index + "It may be a system index that will not be recreated. Create the index based on your needs."
```

