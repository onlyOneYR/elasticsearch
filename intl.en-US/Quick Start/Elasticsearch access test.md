# Elasticsearch access test {#concept_wmx_cmk_zgb .concept}

After you have created an Alibaba Cloud Elasticsearch instance, you can log on to the Alibaba Cloud Elasticsearch console, go to the Kibana console, and test the instance on the Dev Tools page, or you can run the `Curl` command on an ECS instance that meets the requirements.

Elasticsearch has provided other Elasticsearch clients to run the test. For more information, see [Elasticsearch client](https://www.elastic.co/guide/en/elasticsearch/client/index.html).

## Username and password {#section_kbf_xrl_zgb .section}

You must specify a username and password to log on to the Alibaba Cloud Elasticsearch instance.

-   Username: The account that is used to access the Alibaba Cloud Elasticsearch instance. we recommend that you do not use the **elastic** account.
-   password: The password that you specified when purchasing an Alibaba Cloud Elasticsearch instance or initialized Kibana.

**Note:** 

-   You can use the **elastic** account to access the Alibaba Cloud Elasticsearch instance. However, it takes time for the new password that you specified when modifying the **Elasticsearch** account to take effect. Services may become unavailable during this period. Therefore, we recommend that you do not use the **elastic** account to access the Alibaba Cloud Elasticsearch instance.
-   If the version of the Alibaba instance that you have created contains information about **with\_X-Pack**, you must specify the username and password before you can access the Alibaba Cloud Elasticsearch instance.

## Prerequisites for using an ECS instance to access Elasticsearch {#section_rvs_tsl_zgb .section}

-   The Alibaba Cloud Elasticsearch instance and the Alibaba Cloud ECS instance must be deployed in the same VPC.
-   Alibaba Cloud ECS instances are deployed in the classic network. If your ECS instance needs to access the Elasticsearch instance that is in a VPC, see [Classic network errors](../../../../../intl.en-US/FAQ/Classic network errors.md).

## curl testing {#section_jwv_5sl_zgb .section}

**Note:** 

If no filebeat index exists on the Alibaba Cloud Elasticsearch instance, run the `PUT filebeat` command to create a corresponding index, or change the YML file to **Allow Automatic Indexing**. Automatic indexing is disabled by default. Otherwise, the **index\_not\_found\_exception** error occurs when you run the following command.

**For Linux**

You can run the cURL command to access port `9200` of the Alibaba Cloud Elasticsearch instance from a Linux environment.

Specify a username and password to access the instance, for example:

`curl -XPOST -u username:password 'http://<HOST>:9200/filebeat/my_type/'?pretty -d '{"title": "One", "tags": ["ruby"]}'`

-   `<HOST>`: The private or public IP address of the Alibaba Cloud Elasticsearch instance. For more information, see the information page of the Alibaba Cloud Elasticsearch instance.

Response:

```
{ 
"_index" : "filebeat", 
"_type" : "my_type", 
"_id" : "AV-bTkaTwdiHxfaSqlAt", 
"_version" : 1, 
"result" : "created", 
"_shards" : { 
    "total" : 2, 
    "successful" : 2, 
    "failed" : 0 
    }, 
"created" : true
}
```

**For Windows**

You can run the cURL command to access port `9200` of the Alibaba Cloud Elasticsearch instance from a Windows environment.

Specify a username and password to access the instance, for example:

`curl -XPOST -u username:password "http://<HOST>:9200/filebeat/my_type/"?pretty -d "{"""title""": """One""","""tags""": ["""ruby"""]}"`

-   `<HOST>`: The private or public IP address of the Alibaba Cloud Elasticsearch instance. For more information, see the information page of the Alibaba Cloud Elasticsearch instance.

Response:

```
{ 
"_index" : "filebeat", 
"_type" : "my_type", 
"_id" : "AWVIU5lY4siSsiAh0Td6", 
"_version" : 1, 
"result" : "created", 
"_shards" : { 
    "total" : 2, 
    "successful" : 2, 
    "failed" : 0 
    }, 
"created" : true
}
```

## Create a document {#section_pxn_vsl_zgb .section}

Use the following `HTTP POST` method to create a document:

`curl http://<HOST>:9200/my_index/my_type -XPOST -d '{"title": "One", "tags": ["ruby"]}'`

-   `my_index`: The name of the index.
-   `<HOST>`: The private or public IP address of the Alibaba Cloud Elasticsearch instance. For more information, see the information page of the Alibaba Cloud Elasticsearch instance.
-   Each document has its own `ID` and `type` that are contained in the response. The system will randomly generate an `ID` and `type` if you do not specify them when you create the document.

**Note:** If you have enabled automatic indexing \(disabled by default\) and the specified `index` name does not exist, the system will automatically create an `index` when you create the document.

Sample response when you successfully create a document:

```
{ 
"_index": "my_index", 
"_type": "my_type", 
"_id": "AV4JIvi15ny3i8DCdK1H", 
"_version": 1, 
"result": "created", 
"_shards" : { 
    "total": 2, 
    "successful": 1, 
    "failed" : 0 
    }, 
"created": true
}
```

## Update a document {#section_jf1_wsl_zgb .section}

You can use the following statements to update an existing document on Easticsearch:

``http://<HOST>:9200/my_index/my_type/<doc_id>``

-   `<HOST>`: The private or public IP address of the Alibaba Cloud Elasticsearch instance. For more information, see the information page of the Alibaba Cloud Elasticsearch instance.
-   `<doc_id>`: The ID of the document.

`$ curl http://<HOST>:9200/my_index/my_type/AV4JIv**i15**ny3**i8**DCdK1H -XPOST -d '{"title": "Four updated", "tags": ["ruby", "php"]}'`

Sample response when you successfully update a document:

```
{ 
"_index": "my_index", 
"_type": "my_type", 
"_id": "AV4JIvi15ny3i8DCdK1H", 
"version": 2, 
"result": "updated", 
"_shards" : { 
    "total": 2, 
    "successful": 1, 
    "failed" : 0 
    }, 

"created": false
}
```

**Note:** You can also use an **API** to perform a batch update operation on the documents.

## Retrieve a document {#section_ucy_wsl_zgb .section}

You can use the following `HTTP GET` method to retrieve a document:

```
$ curl http://<HOST>:9200/my_index/my_type/AV4JIvi15ny3i8DCdK1H
{ 
"_index" : "my_index", 
"_type" : "my_type", 
"_id" : "_b-kbI1MREmi9SeixFNEVw", 
"_version" : 2, 
"exists" : true, 
"_source" : { "title": "Four updated", "tags": ["ruby", "php"] }
}
```

-   `<HOST>`: The private or public IP address of the Alibaba Cloud Elasticsearch instance. For more information, see the information page of the Alibaba Cloud Elasticsearch instance.

## Search for a document {#section_qf4_xsl_zgb .section}

You can use the `HTTP GET` or `HTTP POST` method to search for a document, and use the URI parameter to specify the object that you want to search for, such as:

```
http://:9200/_search
http://:9200/{index_name}/_search
http://:9200/{index_name}/{type_name}/_search
```

-   `<HOST>`: The private or public IP address of the Alibaba Cloud Elasticsearch instance. For more information, see the information page of the Alibaba Cloud Elasticsearch instance.

Example:

`$ curl http://<HOST>:9200/my_index/my_type/_search?q=title:T*`

-   `<HOST>`: The private or public IP address of the Alibaba Cloud Elasticsearch instance. For more information, see the information page of the Alibaba Cloud Elasticsearch instance.

## Complex search {#section_if1_ysl_zgb .section}

You must use the following `HTTP POST` method for complex search:

```
$ curl http://:9200/my_index/my_type/_search?pretty=true -XPOST -d '{
"query": { 
      "query_string": {"query": "*"}
},
"facets": { 
    "tags": { 
        "terms": {"field": "tags"} 
    }
}}'
```

-   `<HOST>`: The internal or public IP address of the Alibaba Cloud Elasticsearch instance. For more information, see the information page of the Alibaba Cloud Elasticsearch instance.

## Delete a document {#section_x3x_ysl_zgb .section}

`$ curl http://<HOST>:9200/{index}/{type}/{id} -XDELETE`

-   `<HOST>`: The private or public IP address of the Alibaba Cloud Elasticsearch instance. For more information, see the information page of the Alibaba Cloud Elasticsearch instance.

## Delete the specified type of documents {#section_elk_zsl_zgb .section}

`$ curl http://<HOST>:9200/{index}/{type} -XDELETE`

-   `<HOST>`: The private or public IP address of the Alibaba Cloud Elasticsearch instance. For more information, see the information page of the Alibaba Cloud Elasticsearch instance.

## Delete an index {#section_nmz_zsl_zgb .section}

`$ curl http://<HOST>:9200/{index} -XDELETE`

-   `<HOST>`: The private or public IP address of the Alibaba Cloud Elasticsearch instance. For more information, see the information page of the Alibaba Cloud Elasticsearch instance.

