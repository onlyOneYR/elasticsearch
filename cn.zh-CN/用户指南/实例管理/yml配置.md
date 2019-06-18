# yml配置 {#concept_qxp_rzl_zgb .concept}

## 自定义CORS访问（跨域） {#section_q4q_4xs_zgb .section}

 **配置信息** 

**说明：** 

-   表格中的配置信息是阿里云Elasticsearch为支持HTTP协议开放的自定义配置。
-   表格中的字段配置仅支持**静态配置**。如果您想使配置生效，请将配置信息写入elasticsearch.yml文件中。
-   以下配置依赖于集群网络设定（ [Network settings](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/modules-network.html)）。

|配置项|描述|
|---|--|
|`http.cors.enabled`|跨域资源共享配置项，即配置Elasticsearch是否允许其他域资源下的浏览器向其发送请求。可设置为`true`或`false`。 -   设置为`true`，表示启用跨域资源访问。即可使Elasticsearch处理`OPTIONS` CORS请求。如果发送请求中的域信息已在`http.cors.allow-origin`中声明，那么Elasticsearch会在头信息中附加 `Access-Control-Allow-Origin`以响应跨域请求。
-   设置为`false`（默认），表示禁止跨域资源访问。即可使Elasticsearch忽略请求头中的域信息，Elasticsearch将不会以`Access-Control-Allow-Origin`信息头应答，以达到禁用CORS目的。如果客户端不支持发送附加域信息头的 `pre-flight`请求，或者不校验从服务端返回的报文的头信息中的`Access-Control-Allow-Origin`信息，那么跨域安全访问将受到影响。如果Elasticsearch关闭CORS支持, 则客户端只能尝试通过发送`OPTIONS`请求，以了解此响应信息是否存在。

 |
|`http.cors.allow-origin`| 域资源配置项，可设置接受来自哪些域名的请求。默认不允许且无配置。

 如果在该配置值前后添加`/` ，则此配置信息会被识别为正则表达式。允许您使用正则方式兼容支持`HTTP`和`HTTPs`的域请求信息。例如`/https?:\/\/localhost(:[0-9]+)?/`，表示可响应符合此正则的请求信息。

 **说明：** `*`被认定为合法配置，可被识别为使集群支持来自任意域名的跨域请求，这将给Elasticsearch集群带来安全风险，不建议使用。

 |
|`http.cors.max-age`|浏览器可发送`OPTIONS`请求以获取CORS配置信息，此配置项可设置获取的信息在浏览器中的缓存时间，默认为`1728000`秒 \(20 天\)。|
|`http.cors.allow-methods`|请求方法配置项，默认为`OPTIONS, HEAD, GET, POST, PUT, DELETE`。|
|`http.cors.allow-headers`|请求头信息配置项，默认为`X-Requested-With, Content-Type, Content-Length`。|
|`http.cors.allow-credentials`|凭证信息配置项目，即是否允许响应头中返回`Access-Control-Allow-Credentials`信息。默认为`false`，表示不允许返回此信息。设置为`true`表示允许返回此信息。|

自定义跨域访问配置示例如下。

``` {#codeblock_x4o_ncc_gvj}
http.cors.enabled: true
http.cors.allow-origin: "*"
http.cors.allow-headers: "X-Requested-With, Content-Type, Content-Length, Authorization"
```

如果需要了解更多设置，请参见Elasticsearch官方网站，查看[HTTP](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/modules-http.html)相关信息。

## 自定义远程索引重建（白名单） {#section_qrw_4xs_zgb .section}

索引重建组件支持用户在远程的Elasticsearch集群中重建数据索引，适用于您所能找到的任意版本的远程Elasticsearch服务。

您可以使用自定义远程索引重建功能，将旧版本的Elasticsearch服务中的数据索引到当前发布版本中。使用示例如下，详细说明请参见[Reindex API](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/docs-reindex.html)。

``` {#codeblock_9pd_xib_rth}
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

-   `host`：远程主机的地址，必须包含支持协议、域名和端口等信息，例如`https://otherhost:9200`。

    **说明：** 远程主机地址需要在elasticsearch.yaml中使用`reindex.remote.whitelist`属性进行声明，才可以在远程使用此API功能。允许以`host`和`port`组合，并使用逗号分隔多个主机配置（例如`otherhost:9200, another:9200, 127.0.10.**:9200,localhost:**`）。白名单不识别协议信息，只使用主机和端口信息用于实现安全策略设定。

-   `username`和`password`为可选参数，如果您所请求的远程Elasticsearch服务需要使用`Basic Authorization`，请在请求中一并提供此参数信息。通过`Basic Authorization`鉴权需要使用https协议，否则密码信息将以文本形式进行传输。

**说明：** 

-   如果机器地址信息已在白名单中设定，将不会验证和修改`query`，而是直接发送请求至远端服务。
-   从远端集群索引数据，不支持**手动切片**或**自动切片**。详情请参见[手动切片](https://github.com/elastic/elasticsearch/blob/5.6/docs/reference/docs/reindex.asciidoc#docs-reindex-manual-slice)或 [自动切片](https://github.com/elastic/elasticsearch/blob/5.6/docs/reference/docs/reindex.asciidoc#docs-reindex-automatic-slice)。
-   
 批量设定 

远端服务使用堆缓存索引数据，默认最大设定值为100（MB）。如果远端索引中包含大文档，请您将批量设定值设置为较小值。

以下示例中，设置批量数值为10。

``` {#codeblock_h05_48g_2ap}
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

 超时时间 

-   使用`socket_timeout`设置`socket`读取超时时间，默认超时为`30s`。
-   使用`connect_timeout`设置连接超时时间，默认超时为`1s`。

以下示例中，设置`socket`读取超时为1分钟，连接超时为10秒。

``` {#codeblock_913_pjn_28k}
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

 开启Accesslog索引 

阿里云Elasticsearch实例不支持查看存盘请求的相关log文件，因此如果您想了解阿里云Elasticsearch实例请求的相关信息（例如access\_log），那么需要在控制台中开启阿里云Elasticsearch实例对应的Accesslog索引功能。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134292/156085268440144_zh-CN.png)

修改生效后，Accesslog将输出到阿里云Elasticsearch实例中，并且使用`.security_audit_log-*`开头的索引名称。

 配置Accesslog索引 

**说明：** 

-   您无法实现对Accesslog索引过程中的信息进行过滤。当您的Accesslog事件中包含`request body`信息时，有可能在文本中暴露敏感信息。
-   当您将Accesslog日志计入索引中时，该日志将占用您阿里云Elasticsearch实例的存储空间。因为阿里云Elasticsearch不支持自动过期清除策略，需要您手动触发清除陈旧的Accesslog索引。

Accesslog索引配置项如下。

``` {#codeblock_mdq_chj_x34}
xpack.security.audit.index.bulk_size: 5000
xpack.security.audit.index.events.emit_request_body: false
xpack.security.audit.index.events.exclude: run_as_denied,anonymous_access_denied,realm_authentication_failed,access_denied,connection_denied
xpack.security.audit.index.events.include: authentication_failed,access_granted,tampered_request,connection_granted,run_as_granted
xpack.security.audit.index.flush_interval: 180s
xpack.security.audit.index.rollover: hourly
xpack.security.audit.index.settings.index.number_of_replicas: 1
xpack.security.audit.index.settings.index.number_of_shards: 10
```

|特性|默认设置|描述|
|--|----|--|
|`xpack.security.audit.index.bulk_size`|`1000`|控制Accesslog事件完成一次写入动作，并归档到文件的文档数量。|
|`xpack.security.audit.index.flush_interval`|`1s`|控制缓冲事件刷新到索引的频率。|
|`xpack.security.audit.index.rollover`|`daily`|控制滚动构建到新索引的频率，可以设置为`hourly`、`daily`、`weekly`或者`monthly`。|
|`xpack.security.audit.index.events.include`|`access_denied, access_granted, anonymous_access_denied, authentication_failed, connection_denied, tampered_request, run_as_denied, run_as_granted`|控制何种Accesslog事件可以被计入到索引中。完整列单请参见[Accesslog事件类型](https://www.elastic.co/guide/en/x-pack/5.5/auditing.html#audit-event-types)|
|`xpack.security.audit.index.events.exclude`| |构建索引过程中排除的Accesslog事件。|
|`xpack.security.audit.index.events.emit_request_body`|`false`|当触发明确的事件类型（比如 `authentication_failed`），是否忽略或包含以REST发送的请求体。|

您也可以对存储Accesslog的索引进行配置，将以`xpack.security.audit.index.settings`为命名空间，配置在`elasticsearch.yml`文件中。

以下设置构建Accesslog索引的分片和副本均为`1`。

``` {#codeblock_n95_buk_bmw}
xpack.security.audit.index.settings:
  index:
    number_of_shards: 1
    number_of_replicas: 1
```

**说明：** 如果您希望通过传入参数配置生成Accesslog索引，请在开启Accesslog索引的同时传入此配置。当阿里云Elasticsearch实例完成变更后，Accesslog索引将会出现在您的阿里云Elasticsearch实例中。否则阿里云Elasticsearch实例的Accesslog索引将以默认的`number_of_shards: 5``number_of_replicas: 1`进行设置。

更多详细信息请参见[Auditing Security Settings](https://www.elastic.co/guide/en/elasticsearch/reference/6.3/auditing-settings.html#auditing-settings)。

## 自定义queue大小 {#section_km3_pxs_zgb .section}

可以通过自定义`thread_pool.bulk.queue_size`、`thread_pool.write.queue_size`和`thread_pool.search.queue_size`，分别调整文档写入和搜索queue大小。

以下设置文档写入和搜索queue大小为`500`，需要您根据实际业务情况自行调整合适参数值。

**说明：** 以下示例默认兼容阿里云Elasticsearch 5.5.3和6.3.2版本。

``` {#codeblock_r07_9kv_p2s}
thread_pool.bulk.queue_size: 500（只适用于 Elasticsearch 5.5.3 with X-Pack版本）
thread_pool.write.queue_size: 500（只适用于 Elasticsearch 6.3.2 with X-Pack版本）
thread_pool.search.queue_size: 500
```

## 参数调优 {#section_r34_pxs_zgb .section}

|配置项|描述|
|---|--|
|index.codec| Elasticsearch的数据压缩算法默认为LZ4。当您使用高效云盘的warm或cold集群时，通常将该参数设置为best\_compression，就可使用更高压缩率的DEFLATE算法。在更改压缩算法后，segment merge时会使用新的压缩算法。

**说明：** 使用best\_compression会导致写性能降低。

 |

 REST API设置 

可以通过REST API设置`index.codec`参数，示例如下。

``` {#codeblock_0h0_yxz_chw}
PUT $index_name/_settings
{
  "index": {
    "codec": "best_compression"
  }
}
```

**说明：** 

-   执行命令前需要先关闭对应索引，否则会报错。
-   `$index_name`：替换为需要设置的索引名。

