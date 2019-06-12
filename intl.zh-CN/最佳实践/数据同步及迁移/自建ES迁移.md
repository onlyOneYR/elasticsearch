# 自建ES迁移 {#concept_orx_tbt_zgb .concept}

## 前提条件 {#section_q1d_cht_zgb .section}

本文档主要用于将基于阿里云ECS自建的ES迁移至阿里云ES，参考本文档做迁移必须先满足以下条件，如果不满足以下条件需要通过其它迁移方案做迁移，详情请参见[logstash部署](intl.zh-CN/最佳实践/logstash部署.md)：

-   自建ES所在ECS必需是VPC网络（**不支持Classiclink方式打通的ECS**），并且自建ES所在ECS对应VPC必须与阿里云ES对应VPC相同。
-   可以通过中控机器（或者任意一台机器）执行该脚本，前提是该中控机器可以同时访问新老ES集群的`9200`端口。
-   **VPC安全组不能限制 IP白名单，并且需要开启9200端口。**
-   **VPC安全组不能限制阿里云ES实例各节点IP（kibana控制台可查看阿里云ES实例各节点IP）。**
-   可以在执行脚本的机器上通过 `curl -XGET http://<host>:9200` 验证。

## 执行流程 {#section_pwg_2ht_zgb .section}

1.  创建索引。
2.  数据迁移。

## 创建索引 {#section_q5f_fht_zgb .section}

需要参考老集群中需要迁移的索引配置提前在新集群中创建索引，或者新集群可以使用动态创建索引（需开启自动创建索引）和动态映射（不建议）功能。

附件中提供了python脚本（`indiceCreate.py`），可以拉取所有的索引并创建，只设置`分片数`和`0副本`，其余需要单独调整。

**说明：** 如果执行curl命令提示以下报错，可以在curl命令中再添加`-H "Content-Type: application/json"`参数重试。

`{"error":"Content-Type header [application/x-www-form-urlencoded] is not supported","status":406}`

```
// 获取老集群中所有索引信息，如果没有权限可去掉"-u user:pass"参数，oldClusterHost为老集群的host，注意替换。
  curl -u user:pass -XGET http://oldClusterHost/_cat/indices | awk '{print $3}'
  // 参考上面返回的索引列表，获取需要迁移的指定用户索引的setting和mapping，注意替换indexName为要查询的用户索引名。
  curl -u user:pass -XGET http://oldClusterHost/indexName/_settings,_mapping?pretty=true
  // 参考上面获取到的对应索引的_settings,_mapping信息，在新集群中创建对应索引，索引副本数可以先设置为0，用于加快数据同步速度，数据迁移完成后再重置副本数为1。
  //其中newClusterHost是新集群的host，testindex是已经创建的索引名，testtype是对应索引的type。
  curl -u user:pass -XPUT http://<newClusterHost>/<testindex> -d '{
    "testindex" : {
        "settings" : {
            "number_of_shards" : "5", //假设老集群中对应索引的shard数是5个
            "number_of_replicas" : "0" //设置索引副本为0
          }
        },
        "mappings" : { //假设老集群中对应索引的mappings配置如下
            "testtype" : {
                "properties" : {
                    "uid" : {
                        "type" : "long"
                    },
                    "name" : {
                        "type" : "text"
                    },
                    "create_time" : {
                      "type" : "long"
                    }
                }
           }
       }
   }
}'
```

## 同步速度优化 {#section_mzz_kht_zgb .section}

**说明：** 如果单索引数据量比较大，可以在迁移前将目的索引的副本数设置为**0**，刷新时间为**-1**。待数据迁移完成后，再更改回来，这样可以加快数据同步速度。

```
// 迁移索引数据前可以先将索引副本数设为0，不刷新，用于加快数据迁移速度。
curl -u user:password -XPUT 'http://<host:port>/indexName/_settings' -d' {
        "number_of_replicas" : 0,
        "refresh_interval" : "-1"
}'
// 索引数据迁移完成后，可以重置索引副本数为1，刷新时间1s（1s是默认值）。
curl -u user:password -XPUT 'http://<host:port>/indexName/_settings' -d' {
        "number_of_replicas" : 1,
        "refresh_interval" : "1s"
}'
```

## 数据迁移 {#section_w5r_l3t_zgb .section}

为保证数据迁移结束后一致性，需要上游业务 **停止老集群的写操作**，读服务可以正常进行。迁移完毕后，直接切换到新集群进行读写。如不停止写操作可能会存在数据最终的不一致！

**说明：** 

-   使用下述方案迁移时，如果是通过 `IP + Port`访问老集群，则必须在新集群的 `yml`中配置 `reindex` 白名单（为老集群的 IP 地址），例如：`reindex.remote.whitelist: 1.1.1.1:9200,1.2.*.*:*`
-   如果使用域名访问，则不允许通过 `http://host:port/path` 这种（带path）形式访问。

-   数据量小

    使用`reindex.sh`脚本。

    ```
    #!/bin/bash
    # file:reindex.sh
    indexName="你的索引名"
    newClusterUser="新集群用户名"
    newClusterPass="新集群密码"
    newClusterHost="新集群host"
    oldClusterUser="老集群用户名"
    oldClusterPass="老集群密码"
    # 老集群host必须是[scheme]://[host]:[port]，例如http://10.37.1.1:9200
    oldClusterHost="老集群host"
    curl -u ${newClusterUser}:${newClusterPass} -XPOST "http://${newClusterHost}/_reindex?pretty" -H "Content-Type: application/json" -d'{
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

-   数据量大、无删除操作、有更新时间

    数据量较大且无删除操作时，可以使用滚动迁移的方式，减小停止写服务的时间。滚动迁移需要有一个类似于更新时间的字段代表新数据的写时序。可以在数据迁移完成后，再停止写服务，快速更新一次。即可切换到新集群，恢复读写。

    ```
    #!/bin/bash
    # file: circleReindex.sh
    # CONTROLLING STARTUP:
    # 这是通过reindex操作远程重建索引的脚本，要求：
    # 1. 新集群已经创建完索引，或者支持自动创建和动态映射。
    # 2. 新集群必须在yml里配置IP白名单 reindex.remote.whitelist: 172.16.123.*:9200
    # 3. host必须是[scheme]://[host]:[port]
    USAGE="Usage: sh circleReindex.sh <count>
           count: 执行次数，多次（负数为循环）增量执行或者单次执行
    Example:
            sh circleReindex.sh 1
            sh circleReindex.sh 5
            sh circleReindex.sh -1"
    indexName="你的索引名"
    newClusterUser="新集群用户名"
    newClusterPass="新集群密码"
    oldClusterUser="老集群用户名"
    oldClusterPass="老集群密码"
    ## http://myescluster.com
    newClusterHost="新集群host"
    # 老集群host必须是[scheme]://[host]:[port]，例如http://10.37.1.1:9200
    oldClusterHost="老集群host"
    timeField="更新时间字段"
    reindexTimes=0
    lastTimestamp=0
    curTimestamp=`date +%s`
    hasError=false
    function reIndexOP() {
        reindexTimes=$[${reindexTimes} + 1]
        curTimestamp=`date +%s`
        ret=`curl -u ${newClusterUser}:${newClusterPass} -XPOST "${newClusterHost}/_reindex?pretty" -H "Content-Type: application/json" -d '{
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
        echo "第${reindexTimes}次reIndex，本次更新截止时间 ${lastTimestamp} 结果：${ret}"
        if [[ ${ret} == *error* ]]; then
            hasError=true
            echo "本次执行异常，中断后续执行操作～～，请检查"
        fi
    }
    function start() {
        ## 负数就不停循环执行
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
    echo "开始执行索引 ${indexName} 的 ReIndex操作"
    start $1
    echo "总共执行 ${reindexTimes} 次 reIndex 操作"
    ```

-   数据量大、无删除操作、无更新时间

    当数据量较大，且索引的 mapping 中没有定义更新时间字段时，需要由上游业务修改代码添加更新时间字段。添加完成后可以先将历史数据迁移完，然后再使用上述的第2种方案。

    下面是迁移没有更新时间字段的老数据脚本。

    ```
    #!/bin/bash
    # file:miss.sh
    indexName="你的索引名"
    newClusterUser="新集群用户名"
    newClusterPass="新集群密码"
    newClusterHost="新集群host"
    oldClusterUser="老集群用户名"
    oldClusterPass="老集群密码"
    # 老集群host必须是[scheme]://[host]:[port]，例如http://10.37.1.1:9200
    oldClusterHost="老集群host"
    timeField="updatetime"
    curl -u ${newClusterUser}:${newClusterPass} -XPOST "http://${newClusterHost}/_reindex?pretty" -H "Content-Type: application/json" -d '{
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

-   不停止写服务

    敬请期待。


## 批量创建老集群索引 {#section_qc2_cjt_zgb .section}

下面是一个在新集群中，批量创建老集群索引的python脚本。该脚本默认新创建的索引副本数为**0**。

```
#!/usr/bin/python
# -*- coding: UTF-8 -*-
# 文件名：indiceCreate.py
import sys
import base64
import time
import httplib
import json
## 老集群host（ip+port）
oldClusterHost = "old-cluster.com"
## 老集群用户名，可为空
oldClusterUserName = "old-username"
## 老集群密码，可为空
oldClusterPassword = "old-password"
## 新集群host（ip+port）
newClusterHost = "new-cluster.com"
## 新集群用户名，可为空
newClusterUser = "new-username"
## 新集群密码，可为空
newClusterPassword = "new-password"
DEFAULT_REPLICAS = 0
def httpRequest(method, host, endpoint, params="", username="", password=""):
    conn = httplib.HTTPConnection(host)
    headers = {}
    if (username != "") :
        'Hello {name}, your age is {age} !'.format(name = 'Tom', age = '20')
        base64string = base64.encodestring('{username}:{password}'.format(username = username, password = password)).replace('\n', '')
        headers["Authorization"] = "Basic %s" % base64string;
    if "GET" == method:
        headers["Content-Type"] = "application/x-www-form-urlencoded"
        conn.request(method=method, url=endpoint, headers=headers)
    else :
        headers["Content-Type"] = "application/json"
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
    print index + "  原始settings如下：\n" + indexSettings
    settingsDict = json.loads(indexSettings)
    ## 分片数默认和老集群索引保持一致
    number_of_shards = settingsDict[index]["settings"]["index"]["number_of_shards"]
    ## 副本数默认为0
    number_of_replicas = DEFAULT_REPLICAS
    newSetting = "\"settings\": {\"number_of_shards\": %s, \"number_of_replicas\": %s}" % (number_of_shards, number_of_replicas)
    return newSetting
def getMapping(index, host, username="", password=""):
    endpoint = "/" + index + "/_mapping"
    indexMapping = httpGet(host, endpoint, username, password)
    print index + " 原始mapping如下：\n" + indexMapping
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
    print "新索引 " + newIndexName + " 的setting和mapping如下：\n" + createstatement
    endpoint = "/" + newIndexName
    createResult = httpPut(newClusterHost, endpoint, createstatement, newClusterUser, newClusterPassword)
    print "新索引 " + newIndexName + " 创建结果：" + createResult
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
        print index + "  或许是系统索引，不会重新创建，如有需要，请单独处理～"
```

