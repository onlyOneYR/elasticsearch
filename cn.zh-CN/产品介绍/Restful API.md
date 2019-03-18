# Restful API {#concept_k52_kbf_zgb .concept}

## 介绍 {#section_d3n_ljf_zgb .section}

ElasticSearch 采用 REST API， 所有的操作都可通过 Http API 完成交互。例如，增删改查、别名配置。

官方文档参考 [Elasticsearch Restful API](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs.html)。

## Elasticsearch Reference \[5.5\] {#section_uft_fkf_zgb .section}

**Single document APIs**

-   [Index API](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-index_.html)
-   [Get API](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-get.html)
-   [Delete API](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-delete.html)
-   [Update API](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-update.html)

**Multi-document APIs**

-   [Multi Get API](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-multi-get.html)
-   [Bulk API](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-bulk.html)
-   [Delete By Query API](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-delete-by-query.html)
-   [Update By Query API](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-update-by-query.html)
-   [Reindex API](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-reindex.html)

## 使用 REST Client 交互 {#section_q3t_4jf_zgb .section}

客户端访问仅支持 HTTP / TCP 方式，建议您采用 Elasticsearch 官方[Java REST Client](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/index.html?spm=a2c4g.11186623.2.4.GSrVva)。

## 使用 Java API 交互 {#section_pys_vjf_zgb .section}

Elasticsearch 为 Java用户提供了内置客户端。关于Java API的更多信息，请查看相关章节：[Java API](http://www.elasticsearch.org/guide/) 。

**传输客户端（Transport client）**

传输客户端能够发送请求到远程集群，它自己不加入集群，只是简单转发请求给集群中的节点。

传输客户端通过`9300`端口与集群交互，使用Elasticsearch传输协议（Elasticsearch Transport Protocol）。

集群中的节点之间也通过`9300`端口进行通信。如果此端口未开放，您的节点将不能组成集群。

**说明：** Java客户端所在的Elasticsearch版本必须与集群中其他节点一致，否则它们可能互相无法识别。

## RESTful API（HTTP） {#section_nvc_bkf_zgb .section}

其他所有程序语言都可以使用RESTful API，通过`9200`端口与Elasticsearch进行通信。可使用您喜欢的WEB客户端，或通过curl命令与Elasticsearch通信。

**说明：** 

Elasticsearch官方提供了多种程序语言的客户端，例如`Groovy`，`Javascript`， `.NET`，`PHP`，`Perl`，`Python`，以及 `Ruby`。

还有很多由社区提供的客户端和插件，所有这些可以在[文档](http://www.elasticsearch.org/guide/)中找到。

**curl 请求组成（HTTP）**

`curl -X<VERB> '<PROTOCOL>://<HOST>:<PORT>/<PATH>?<QUERY_STRING>' -d '<BODY>'`

-   VERB： HTTP方法：`GET`，`POST`，`PUT`，`HEAD`，`DELETE`
-   PROTOCOL： `http` 或者 `https` 协议（只有在Elasticsearch前面有 `https` 代理的时候可用）
-   HOST： Elasticsearch集群中的任何一个节点的主机名，如果是在本地的节点，那么就叫 `localhost`
-   PORT： Elasticsearch HTTP服务所在的端口，默认为 `9200`
-   PATH： API路径（例如 `_count` 将返回集群中文档的数量），PATH可以包含多个组件，例如 `_cluster/stats` 或者 `_nodes/stats/jvm`
-   QUERY\_STRING： 一些可选的查询请求参数，例如 `?pretty` 参数可使请求返回的JSON数据，更加美观易读
-   BODY： 一个JSON格式的请求主体（如果请求需要的话）

**举例说明**

统计Elasticserach集群中文档数命令：

```
curl -XGET 'http://localhost:9200/_count?pretty' -d '
{ 
  "query": { 
    "match_all": {} 
  }
}'
```

响应如下：

```
{ 
    "count" : 0, 
    "_shards" : { 
        "total" : 5, 
        "successful" : 5, 
        "failed" : 0 
     }
}
```

使用 curl`-i`参数显示HTTP头：

`curl -i -XGET 'localhost:9200/'`

完整请求示例：

```
curl -XGET 'localhost:9200/_count?pretty' -d '
{ 
    "query": { 
        "match_all": {} 
    }
}'
```

简写请求示例：

```
GET /_count
{ 
    "query": { 
        "match_all": {} 
    }
}
```

