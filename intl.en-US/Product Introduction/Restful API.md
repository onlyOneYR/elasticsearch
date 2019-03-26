# Restful API {#concept_k52_kbf_zgb .concept}

## Introduction {#section_d3n_ljf_zgb .section}

Elasticsearch provides a RESTful Web API, which allows you to perform operations including addition, deletion, modification, search, and alias configuration.

For more information about the official Elasticsearch REST API, see [Elasticsearch Restful API](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs.html).

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

## Use REST clients to communicate with clusters {#section_q3t_4jf_zgb .section}

You can use REST clients to access Elasticsearch clusters through HTTP or TCP. We recommend that you use the Elasticsearch official [Java REST Client](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/index.html?spm=a2c4g.11186623.2.4.GSrVva).

## Use Java APIs to communicate with clusters {#section_pys_vjf_zgb .section}

Elasticsearch provides a default client for Java users. For more information, see [Java API](http://www.elasticsearch.org/guide/).

**Transport client**

A transport client forwards requests to nodes in a cluster. However, it is not a part of a cluster.

A transport client uses the Elasticsearch Transport Protocol to communicate with clusters over port `9300`.

Nodes in a cluster also use port `9300` to communicate with each other. You must open port 9300 for your nodes before grouping them into a cluster.

**Note:** Your Java clients and nodes must use the same Elasticsearch version for them to recognize each other.

## RESTful API \(HTTP\) {#section_nvc_bkf_zgb .section}

All other languages can communicate with Elasticsearch over port `9200` using a RESTful API, with your favorite web client. You can use the curl command to communicate with Elasticsearch at the CLI.

**Note:** 

Elasticsearch provides official clients for several languages, including `Groovy`,`Javascript`,`.NET`, `PHP`, `Perl`, `Python`, and`Ruby`.

For more clients and plug-ins provided by communities, see [Document](http://www.elasticsearch.org/guide/).

**Structure of a curl request over HTTP**

`Curl-X <VERB> '<PROTOCOL>: // <HOST >:< PORT>/<PATH>? <QUERY_STRING> '-d' <BODY>'`

-   VERB: an HTTP method: `GET`,`POST`,`PUT`,`HEAD`,`DELETE`
-   PROTOCOL: `http` or `https`. Use `https` only if HTTPS is enabled for Elasticsearch.
-   HOST: the hostname of any node in your Elasticsearch cluster or `localhost` for a local node.
-   PORT: the port that runs the Elasticsearch HTTP service. The default port number is `9200`.
-   PATH: API endpoint, for example, `_count` returns the number of documents in a cluster. PATH may contain multiple components, such as `_cluster/stats` and `_nodes/stats/jvm`.
-   QUERY\_STRING: optional query parameters. For example, the `?pretty` parameter makes the JSON response much easier to read.
-   BODY: JSON-encoded request body \(only if the request requires one\).

**Example**

Count the number of documents in an Elasticsearch cluster:

```
curl -XGET 'http://localhost:9200/_count?pretty' -d '
{ 
  "query": { 
    "match_all": {} 
  }
}'
```

The body of the response to the curl request:

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

Use the curl `-i` command to display the HTTP header:

`curl -i -XGET 'localhost:9200/'`

Full request:

```
Curl-XGET 'localhost: 9200/_ count? pretty '-d'
{ 
    "query": { 
        "match_all": {} 
    }
}'
```

Shorthand for the request:

```
GET /_count
{ 
    "query": { 
        "match_all": {} 
    }
}
```

