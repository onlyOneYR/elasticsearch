# yml配置 {#concept_qxp_rzl_zgb .concept}

## 自定义CORS访问（跨域） {#section_q4q_4xs_zgb .section}

如果需了解更多设置，请参见Elasticsearch 官方网站，查看 [HTTP](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/modules-http.html) 相关信息。

 **配置信息** 

-   表格内的设置信息，是阿里云Elasticsearch 为支持 HTTP 协议，开放的自定义配置。
-   以下字段配置仅支持**静态配置**，不支持热部署。如您想生效以下某项配置，请将配置信息写入 `elasticsearch.yml` 文件。
-   以下配置依赖于集群网络设定。（ [Network settings](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/modules-network.html)）

|配置项|描述|
|---|--|
|`http.cors.enabled`|跨域资源共享配置项。可 **启用** 或 **禁用** 跨域资源访问。即Elasticsearch允许接收其他域资源下的浏览器向其发送请求. 设置为 `true` 即可使Elasticsearch处理 `OPTIONS`CORS请求 。如果发送请求中的域信息已在`http.cors.allow-origin`中声明，那Elasticsearch将会在头信息中附加 `Access-Control-Allow-Origin` 以响应跨域请求。设置为`false` \(默认缺省值为false\)即可使Elasticsearch忽略请求头中的域信息，Elasticsearch将不会以`Access-Control-Allow-Origin`信息头应答，以达到禁用CORS目的。如果客户端不支持发送附加域信息头的 `pre-flight` 请求，或者不校验从服务端返回的报文的头信息中的`Access-Control-Allow-Origin` 信息，那跨域安全访问将受到影响。如果Elasticsearch关闭CORS支持, 则客户端只能尝试通过发送 `OPTIONS`请求，以了解此响应信息是否存在。|
|`http.cors.allow-origin`|域资源配置项。可设置接受来自哪些域名的请求。默认不允许且无配置。如果在该配置值前后添加 `/` ，则此配置信息将被识别为正则表达式。允许您使用正则方式兼容支持`HTTP` 和 `HTTPs`类域请求信息。比如 `/https?:\/\/localhost(:[0-9]+)?/` ，则可响应符合此正则的请求信息。 `*` 被认定为合法配置，可被识别为使集群支持来自 **任意域名** 的跨域请求，这将给Elasticsearch 集群带来 **安全风险** 。|
|`http.cors.max-age`|浏览器可发送`OPTIONS` 请求以获取CORS配置信息。`max-age` 设置可设定浏览器对输出结果缓存保持时间。缺省设置为 `1728000` 秒 \(20 天\)。|
|`http.cors.allow-methods`|请求方法配置项。默认设置值为: `OPTIONS`，`HEAD`，`GET`，`POST`，`PUT`，`DELETE` 。|
|`http.cors.allow-headers`|请求头信息配置项。默认设置值为: `X-Requested-With, Content-Type, Content-Length`。|
|`http.cors.allow-credentials`|凭证信息配置项目。是否允许响应头中返回`Access-Control-Allow-Credentials` 信息。设置为 `true` 即可返回此信息。默认设定值为 `false` 。|

自定义跨域访问配置示例如下：

``` {#codeblock_x4o_ncc_gvj}
http.cors.enabled: true
http.cors.allow-origin: "*"
http.cors.allow-headers: "X-Requested-With, Content-Type, Content-Length, Authorization"
```

## 自定义远程索引重建（白名单） {#section_qrw_4xs_zgb .section}

索引重建组件支持用户在远端 Elasticsearch集群，进行重建数据索引。此特性在您所能找到的任意远程Elasticsearch版本的服务中均可工作。此设置将允许您通过从陈旧的版本服务中索引数据到当前发布版本。

```
POST _reindex
{
  "source": {
    "remote": {
      "host": "http://otherhost:9200",
      "username": "user",
      "password": "pass"
    },
    "index": "source",
    "query": {
      "match": {
        "test": "data"
      }
    }
  },
  "dest": {
    "index": "dest"
  }
}
```

-   `host`必须包含 **支持协议**、**域名**、 **端口** 等信息，例如`https://otherhost:9200`。

-   `username` 和 `password` 为可选参数，如您所请求的远端Elasticsearch服务需使用 **Basic Authorization**，请在请求中一并提供此参数信息。使用`Basic Authorization` 鉴权，请使用`https` 协议，否则密码信息将以文本形式进行传输。

-   远程主机地址需在 `elasticsearch.yaml`中使用 `reindex.remote.whitelist` 属性进行声明，方可在远程使用此API功能。允许以 `host` 和 `port` 组合，并使用逗号分隔多个主机配置（例如：

```
otherhost:9200, another:9200, 127.0.10.**:9200,
          localhost:**
```

）。白名单不识别协议信息，只使用主机和端口信息用于实现安全策略设定。

-   如果机器地址信息，已在白名单中设定，将不会验证和修改`query`，而是直接发送请求至远端服务。


**说明：** 

-   从远端集群索引数据，不支持 **手动切片** 或 **自动切片**。详情请参见 [手动切片](https://github.com/elastic/elasticsearch/blob/5.6/docs/reference/docs/reindex.asciidoc#docs-reindex-manual-slice) 或 [自动切片](https://github.com/elastic/elasticsearch/blob/5.6/docs/reference/docs/reindex.asciidoc#docs-reindex-automatic-slice) 。

 **批量设定** 

远端服务使用堆缓存索引数据，默认最大设定值为 `100mb`。如果远端索引中包含大文档，请您将批量设定值设置为较小参数。

以下示例批量数值为10，此为最小值：

```
POST _reindex
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
    "index": "dest"
  }
}
```

 **超时时间** 

-   使用`socket_timeout` ，设定`socket` 读取超时时间，默认超时为 `30s`。
-   使用`connect_timeout` ，设定连接超时时间，默认超时为 `1s`。

以下示例`socket` 读取超时为1分钟，连接超时时间为10秒：

```
POST _reindex
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
    "index": "dest"
  }
}
```

## 自定义Accesslog {#section_xqc_pxs_zgb .section}

 **开启审计** 

审计索引配置项目如下。

```
xpack.security.audit.index.bulk_size: 5000
xpack.security.audit.index.events.emit_request_body: false
xpack.security.audit.index.events.exclude: run_as_denied,anonymous_access_denied,realm_authentication_failed,access_denied,connection_denied
xpack.security.audit.index.events.include: authentication_failed,access_granted,tampered_request,connection_granted,run_as_granted
xpack.security.audit.index.flush_interval: 180s
xpack.security.audit.index.rollover: hourly
xpack.security.audit.index.settings.index.number_of_replicas: 1
xpack.security.audit.index.settings.index.number_of_shards: 10
```

 **索引审计输出** 

阿里云ES实例不支持查看存盘的请求相关log文件，因此如果您想了解阿里云ES实例请求相关信息（例如access\_log），您需要在控制台中开启阿里云ES实例对应accesslog索引功能。

修改生效后， accesslog将输出到阿里云ES实例中以`.security_audit_log-*`开头的索引名称中供您查看。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134292/155617695140144_zh-CN.png)

 **审计日志索引配置详析** 

**说明：** 

-   您无法实现对审计过程中的信息进行过滤，当您的审计事件中包含`request body`信息时，有可能在文本中暴露敏感信息。

-   当设置将审计日志计入索引中，将占用您的阿里云ES实例存储空间，因无自动过期清除策略，需要您手动触发清除陈旧审计日志索引。


|特性|默认设置|描述|
|--|----|--|
|`xpack.security.audit.index.bulk_size`|`1000`|控制审计事件完成一次写入动作并归档到文件的文档数量。|
|`xpack.security.audit.index.flush_interval`|`1s`|控制缓冲事件刷新到索引的频率。|
|`xpack.security.audit.index.rollover`|`daily`|控制滚动构建到新索引的频率：`hourly`， `daily`， `weekly`， 或者 `monthly`。|
|`xpack.security.audit.index.events.include`|`anonymous_access_denied`， `authentication_failed`， `realm_authentication_failed`，`access_granted`，`access_denied`，`tampered_request`，`connection_granted`，`connection_denied`，`run_as_granted`，`run_as_denied`|控制何种审计事件可以被计入到索引中。完整列单请参 [审计事件类型](https://www.elastic.co/guide/en/x-pack/5.5/auditing.html#audit-event-types)|
|`xpack.security.audit.index.events.exclude`| |构建索引过程中排除的审计事件。|
|`xpack.security.audit.index.events.emit_request_body`|`false`|当触发明确的事件类型（比如 `authentication_failed`），是否忽略或包含以REST发送的请求体。|

 **审计索引设置** 

您也可以对存储审计日志的索引进行配置，将以 `xpack.security.audit.index.settings` 为命名空间，配置在`elasticsearch.yml` 文件中。

以下设置构建审计索引的**分片**和**副本**均为`1`。

```
xpack.security.audit.index.settings:
  index:
    number_of_shards: 1
    number_of_replicas: 1
```

**说明：** 如过您希望通过传入参数配置生成审计索引，请在开启审计索引的同时传入此配置，在阿里云ES实例完成变更后，审计索引将会出现在您的阿里云ES实例中。否则阿里云ES实例审计日志将以默认`number_of_shards: 5`,`number_of_replicas: 1`进行设置。

 **审计日志索引至远端集群** 

该配置暂不开放。

## 自定义queue大小 {#section_km3_pxs_zgb .section}

可以通过自定义`thread_pool.bulk.queue_size`、`thread_pool.write.queue_size`、`thread_pool.search.queue_size`配置，分别调整文档写入和搜索queue大小。

以下设置文档写入和搜索queue大小为`500`，需要用户根据实际业务情况自行调整合适参数值。

**说明：** 以下没有标识具体适用于某个ES版本的参数，默认兼容ES 5.5.3 和 6.3.2 版本。

```
thread_pool.bulk.queue_size: 500（只适用于 Elasticsearch 5.5.3 with X-Pack版本）
thread_pool.write.queue_size: 500（只适用于 Elasticsearch 6.3.2 with X-Pack版本）
thread_pool.search.queue_size: 500
```

## 参数调优 {#section_r34_pxs_zgb .section}

|配置项|描述|
|---|--|
|index.codec|ES的数据压缩算法默认为LZ4，在使用高效云盘的warm或cold集群，通常将该参数设置为best\_compression，即可使用更高压缩率的DEFLATE算法。在更改压缩算法后，segment merge时会使用新的压缩算法，**需注意使用best\_compression会导致写性能降低。**|

 **REST API设置** 

可以通过REST API设置`index.codec`参数。

**说明：** 

-   执行命令前需要先`close`对应索引，否则会报错。
-   $index\_name：替换为需要设置的索引名。

```
PUT $index_name/_settings
{
  "index": {
    "codec": "best_compression"
  }
}
```

