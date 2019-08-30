# YML configuration {#concept_qxp_rzl_zgb .concept}

## Customize CORS requests {#section_q4q_4xs_zgb .section}

For more configurations, visit the Elasticsearch official website and view the [HTTP](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/modules-http.html) information.

 **Configuration information** 

-   Configurations in the table below are custom HTTP-based configurations provided by Alibaba Cloud Elasticsearch.
-   For the following configurations, only **static configuration** is supported. Dynamic configuration is not supported. Note that for the following configurations to take effect, you must add the configurations to the `elasticsearch.yml` file.
-   Cluster network settings are used for the following configurations. \([Network settings](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/modules-network.html)\)

|Configuration item|Description|
|------------------|-----------|
|`http.cors.enabled`|A CORS \(Cross-Origin Resource Sharing\) configuration item, which can be used to **enable** or **disable** CORS resource accesses. In other words, this setting is used to determine whether to allow Elasticsearch to receive requests sent by browsers to access resources in different domains. If the parameter is set to `true`, Elasticsearch can process `OPTIONS` CORS requests. If the domain information in the sent request is already declared in `http.cors.allow-origin`, Elasticsearch adds `Access-Control-Allow-Origin` in the header to respond to the CORS request. If the parameter is set to `false` \(which is the default value\), Elasticsearch ignores the domain information in the request header, not adding the `Access-Control-Allow-Origin` to the header, disabling CORS access. If the client neither supports `pre-flight` requests that add the domain information header, nor checks `Access-Control-Allow-Origin` in the header of the packet returned from the server, then the secured CORS access will be affected. If Elasticsearch disables CORS access, then the client can only check whether a response is returned by sending the `OPTIONS` request.|
|`http.cors.allow-origin`|A CORS resource configuration item, which can be used to specify requests from which domains are accepted. The parameter is left blank, by default, with no domain is allowed. If `/` is added before the parameter value, then the configuration is identified as a regular expression, which means that `HTTP` and `HTTPS` domain requests that follow the regular expression are supported. For example`/Https? : \/Localhost (: [0-9] + )? /` means requests follow the regular expression can be responded to. `*` means that a configuration is valid and can be identified as enabling the cluster to support CORS requests from any **domain**, resulting in **security risks** to the Elasticsearch cluster.|
|`http.cors.max-age`|The browser can send an `OPTIONS` request to get the CORS configuration. `max-age` can be used to set how long the browser can retain the output result cache. The default value is `1728000` seconds \(20 days\).|
|`http.cors.allow-methods`|A request method configuration item. The optional values are `OPTIONS`, `HEAD`, `GET`, `POST`, `PUT`, and `DELETE`.|
|`http.cors.allow-headers`|A request header configuration item. The default value is `X-Requested-With, Content-Type, Content-Length`.|
|`http.cors.allow-credentials`|A credential configuration item, which is used to specify whether to return `Access-Control-Allow-Credentials` in the response header. If the parameter is set to `true`, Access-Control-Allow-Credentials is returned. The default value is `false`.|

An example of custom cross-origin access configuration is as follows:

``` {#codeblock_x4o_ncc_gvj}
http.cors.enabled: true
http.cors.allow-origin: "*"
http.cors.allow-headers: "X-Requested-With, Content-Type, Content-Length, Authorization"
```

## Customize remote re-indexing \(whitelist\) {#section_qrw_4xs_zgb .section}

The re-indexing component allows you to reconstruct the data index on the target remote Elasticsearch cluster. This function can work for all of the remote Elasticsearch versions available, allowing you to index the data of earlier versions to the current version.

``` {#codeblock_mk6_xml_wez}
POST _ reindex
{
  "source": {
    "remote": {
      "Host": "http: // otherhost: 9200 ",
      "username": "username",
      "password": "password",
    },
    "index": "source",
    "query": {
      "match": {
        "test": "data"
      }
    }
  },
  "dest": {
    "index": "test-1",
  }
}
```

-   `host` must contain the **protocol supported**, **domain name**, **port**, for example, `Https: // otherhost: 9200`.

-   `username` and `password` are optional. If the remote Elasticsearch server requires **Basic Authorization**, enter the username and password in the request. When use `Basic Authorization`, also use the `https` protocol, otherwise the password will be transmitted as a text.

-   The remote host address must be declared in `elasticsearch.yml` by using the `reindex.remote.whitelist` attribute for the API to be called remotely. The combination of host and port is allowed. The combination of `host` and `port` is allowed. However, note that multiple host configurations must be separated by commas \(,\), for example,

``` {#codeblock_tdi_zzy_6w7}
otherhost: 9200, another: 9200,127.0 .10. **: 9200,
          localhost:**
```

\). The whitelist does not identify the protocol and only uses the host and port information for the security policy configuration.

-   If the host address is already listed in the whitelist, the `query` request will not be verified or modified. Rather, the request will be directly sent to the remote server.


**Note:** 

-   Indexing data from a remote cluster is not supported**Manual Slicing**Or**Automatic Slicing**. For more information, see [Manual slicing](https://github.com/elastic/elasticsearch/blob/5.6/docs/reference/docs/reindex.asciidoc#docs-reindex-manual-slice) or [Automatic slicing](https://github.com/elastic/elasticsearch/blob/5.6/docs/reference/docs/reindex.asciidoc#docs-reindex-automatic-slice).

 **Multiple indexes settings** 

The remote service uses a stack to cache indexed data. The default maximum size is `100 MB`. If the remote index contains a large document, set the size of batch settings to a small value.

In the example below, the size of multiple index settings is 10, which is the minimum value:

``` {#codeblock_d7n_r1x_wzb}
POST _ reindex
{
  "source": {
    "remote": {
      "host": "http://otherhost:9200"
    },
    "index": "source",
    "size": 10,
    "query": {
      "match": {
        "test": "data"
      }
    }
  },
  "dest": {
    "index": "test-1",
  }
}
```

 **Timeout period** 

-   Use `socket_timeout` to set the read timeout period of `socket`. The default value is `30s`.
-   Use `connect_timeout` to set the connection timeout period. The default value is `1s`.

In the example below, the read timeout period of `socket` is one minute, and the connection timeout period is 10 seconds.

``` {#codeblock_j9l_90b_6jd}
POST _ reindex
{
  "source": {
    "remote": {
      "host": "http://otherhost:9200",
      "socket_timeout": "1m",
      "connect_timeout": "10s"
    },
    "index": "source",
    "query": {
      "match": {
        "test": "data"
      }
    }
  },
  "dest": {
    "index": "test-1",
  }
}
```

## Customize the access log {#section_xqc_pxs_zgb .section}

 **Enable auditing** 

The index auditing configuration is as follows.

``` {#codeblock_4re_65w_vw8}
xpack.security.audit.index.bulk_size: 5000
xpack.security.audit.index.events.emit_request_body: false
xpack.security.audit.index.events.exclude: run_as_denied,anonymous_access_denied,realm_authentication_failed,access_denied,connection_denied
xpack.security.audit.index.events.include: authentication_failed,access_granted,tampered_request,connection_granted,run_as_granted
xpack.security.audit.index.flush_interval: 180s
xpack.security.audit.index.rollover: hourly
xpack.security.audit.index.settings.index.number_of_replicas: 1
xpack.security.audit.index.settings.index.number_of_shards: 10
```

 **Index auditing output** 

Alibaba Cloud Elasticsearch instances do not support displaying request-related log files. Therefore, to view information about the Elasticsearch instance requests, such as the access\_log, you must log in to the Elasticsearch console and enable the access log index function.

After this function is enabled, the access log is output to indexes on the Elasticsearch instance. The name of indexes starts with `.security_audit_log-*`.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134292/156714370940144_en-US.png)

 **Audit log indexing configuration** 

**Note:** 

-   Filtering is not supported during audits because sensitive data may be audited in plain text when the `request body` is included in audit events.

-   Audit log indexing occupies Alibaba Cloud Elasticsearch instance storage space. You must manually clear old audit log indexes because no policy is available for clearing expired indexes.


|Feature|Default value|\[DO NOT TRANSLATE\]|
|-------|-------------|--------------------|
|`xpack.security.audit.index.bulk_size`|`1,000`|Indicates how many audit events are batched into a single write file.|
|`xpack.security.audit.index.flush_interval`|`1 s`|Indicates how often buffered events are flushed to the index.|
|`xpack.security.audit.index.rollover`|`daily`|Indicates how often to roll over to a new index. Options include `hourly`, `daily`, `weekly`, or `monthly`.|
|`Xpack. security. audit. index. events. include`|`anonymous_access_denied`， `authentication_failed`， `realm_authentication_failed`，`access_granted`，`access_denied`，`tampered_request`，`connection_granted`，`connection_denied`，`run_as_granted`，`run_as_denied`|Specifies the audit events to be indexed. For more information about audit event types, see [Audit event types](https://www.elastic.co/guide/en/x-pack/5.5/auditing.html#audit-event-types).|
|`xpack. security. audit. index. events. exclude`| |Excludes the specified auditing events from indexing.|
|`xpack. security. audit. index. events. emit_request_body`|`false`|Indicates whether to include the request body in REST requests in certain event types, such as `authentication_failed`.|

 **Audit indexing settings** 

The configuration item `xpack.security.audit.index.settings` in the `elasticsearch.yml` file specifies the settings for the indexes in which the events are stored.

The following example sets both the number of **shards** and the number of **replicas** to `1` for the audit indexes.

``` {#codeblock_dkf_v8w_usn}
xpack. security. audit. index. settings:
  index:
    number_of_shards: 1
    number_of_replicas: 1
```

**Note:** You can pass custom settings to xpack.security.audit.index.settings when enabling audit indexing. Once you apply the change to the Elasticsearch instance, audit indexes will be available on the Elasticsearch instance. Otherwise, the elasticsearch instance audit log is set to the default`Number_of_shards: 5`, and `Number_of_replicas: 1`.

 **Remote audit log indexing settings** 

Indexing settings for remote audit logs are currently unavailable.

## Customize thread pool queue size {#section_km3_pxs_zgb .section}

You can set `Thread_pool.bulk.queue_size`,`Thread_pool.write.queue_size`, and`Thread_pool.search.queue_size` to customize the queue size of the write and search thread pools, respectively..

In the following example, both the write and search queue size are set to `500`.

**Note:** The following parameters are not specifically identified for an ES version and by default are compatible with ES version 5.5.3 and 6.3.2.

``` {#codeblock_2za_9yr_oig}
thread_pool.bulk.queue_size: 500 (Only applicable to the Elasticsearch 5.5.3 with X-Pack version)
thread_pool.write.queue_size: 500 (Only applicable to the Elasticsearch 6.3.2 with X-Pack version)
thread_pool.search.queue_size: 500
```

## Parameter optimization {#section_r34_pxs_zgb .section}

|Configuration Item|Description|
|------------------|-----------|
|Index. codec|The ES data compression algorithm defaults to LZ4. Usually, by setting LZ4 to best\_compression in a warm or cold cluster using a high-speed cloud disk, a higher compression ratio DEFLATE algorithm can be used. After the algorithm is changed, segment merges will use the newest version of the algorithm. **Note that using best\_compression will result in reduced write performance**.|

 **REST API settings** 

You can set the `index.codec` parameter by using REST API.

**Note:** 

-   `close` the corresponding index before running the command.
-   $index\_name: Replace with the index name you need to set.

``` {#codeblock_7zs_1ru_lpg}
PUT $ index_name/_ settings
{
  "index": {
    "codec": "best_compression"
  }
}
```

