# ES访问测试 {#concept_wmx_cmk_zgb .concept}

阿里云ES实例创建完成之后，您可以登录阿里云ES控制台集成的Kibana控制台，在Dev Tools界面测试，也可以在符合条件的ECS实例中通过调用`curl`命令测试。

您也可以参考elastic官方提供的其它[Elasticsearch client](https://www.elastic.co/guide/en/elasticsearch/client/index.html)进行测试。

## 账号密码 {#section_kbf_xrl_zgb .section}

必需要指定阿里云ES实例访问账号密码，才能访问阿里云ES实例服务。

-   username：表示阿里云ES实例访问账号，建议通过非**elastic**账号访问。
-   password：您在购买阿里云ES界面中指定的密码，或初始化Kibana时指定的密码。

**说明：** 

-   支持通过**elastic**账号访问，但因为在修改**elastic**账号对应密码后需要一些时间来生效，在生效密码期间会影响服务访问，因此不建议通过**elastic**来访问。
-   若您创建的阿里云ES实例版本包含**with\_X-Pack**信息，则访问该阿里云ES实例时，必须指定用户名和密码。

## 基于ECS访问前提 {#section_rvs_tsl_zgb .section}

-   阿里云ES与依赖的阿里云ECS必须处于同一个VPC下。
-   阿里云ECS处于经典网络下，期望访问专有网络（VPC）中的阿里云ES，请先参考[经典网络问题](../../../../../cn.zh-CN/常见问题/经典网络问题.md)。

## curl测试 {#section_jwv_5sl_zgb .section}

**说明：** 

如果阿里云ES实例中不存在**filebeat**索引，需要先执行类似`PUT filebeat`命令来创建对应索引，或者修改YML文件配置**允许自动创建索引**（默认不允许），否则执行下面命令会提示**index\_not\_found\_exception**报错。

**Linux环境**

通过Linux环境下的curl命令访问阿里云ES实例的`9200`端口：

指定账号密码访问示例：

`curl -XPOST -u username:password 'http://<HOST>:9200/filebeat/my_type/'?pretty -d '{"title": "One", "tags": ["ruby"]}'`

-   `<HOST>`：表示阿里云ES实例内网/公网地址，详情请参见阿里云ES实例基本信息界面。

响应如下：

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

**Windows环境**

通过Windows环境下的curl命令访问阿里云ES实例的`9200`端口：

指定账号密码访问示例：

`curl -XPOST -u username:password "http://<HOST>:9200/filebeat/my_type/"?pretty -d "{"""title""": """One""","""tags""": ["""ruby"""]}"`

-   `<HOST>`：表示阿里云ES实例内网/公网地址，详情请参见阿里云ES实例基本信息界面。

响应如下：

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

## 创建文档 {#section_pxn_vsl_zgb .section}

使用 `HTTP POST`方式创建：

`curl http://<HOST>:9200/my_index/my_type -XPOST -d '{"title": "One", "tags": ["ruby"]}'`

-   `my_index`：是索引名称。
-   `<HOST>`：表示阿里云ES实例内网/公网地址，详情请参见阿里云ES实例基本信息界面。
-   每个文档都拥有自己的 `ID` 和 `type`，在返回的结果中会显示响应的 `ID` 和 `type`，如果在创建时没有指定，则系统会为其随机生成一个。

**说明：** 如果您已开启自动创建索引（默认关闭），并且指定的 `index` 名称不存在，则在创建 document 时，系统将自动创建 `index` 

创建成功响应示例：

```
{ 
"_index": "my_index", 
"_type": "my_type", 
"_id": "AV4JIvi15ny3i8DCdK1H", 
"_version": 1, 
"result": "created", 
"_shards": { 
    "total": 2, 
    "successful": 1, 
    "failed": 0 
    }, 
"created": true
}
```

## 更新文档 {#section_jf1_wsl_zgb .section}

若Elasticsearch中存在文档，可使用如下语句更新文档。

`http://<HOST>:9200/my_index/my_type/<doc_id>`

-   `<HOST>`：表示阿里云ES实例内网/公网地址，详情请参见阿里云ES实例基本信息界面。
-   `<doc_id>`：表示文档标识ID。

`$ curl http://<HOST>:9200/my_index/my_type/AV4JIv**i15**ny3**i8**DCdK1H -XPOST -d '{"title": "Four updated", "tags": ["ruby", "php"]}'`

更新成功响应示例：

```
{ 
"_index": "my_index", 
"_type": "my_type", 
"_id": "AV4JIvi15ny3i8DCdK1H", 
"_version": 2, 
"result": "updated", 
"_shards": { 
    "total": 2, 
    "successful": 1, 
    "failed": 0 
    }, 

"created": false
}
```

**说明：** 也可以使用批量的**API**进行文档更新。

## 检索文档 {#section_ucy_wsl_zgb .section}

可以通过`HTTP GET`对文档进行检索查询：

```
$ curl http://:9200/my_index/my_type/AV4JIvi15ny3i8DCdK1H
{ 
"_index" : "my_index", 
"_type" : "my_type", 
"_id" : "_b-kbI1MREmi9SeixFNEVw", 
"_version" : 2, 
"exists" : true, 
"_source" : { "title": "Four updated", "tags": ["ruby", "php"] }
}
```

-   `<HOST>`：表示阿里云ES实例内网/公网地址，详情请参见阿里云ES实例基本信息界面。

## 搜索文档 {#section_qf4_xsl_zgb .section}

可以通过`HTTP GET`或`HTTP POST`对文档进行搜索，通过 URI 参数制定搜索对象，目标如下：

```
http://:9200/_search
http://:9200/{index_name}/_search
http://:9200/{index_name}/{type_name}/_search
```

-   `<HOST>`：表示阿里云ES实例内网/公网地址，详情请参见阿里云ES实例基本信息界面。

例如：

`$ curl http://<HOST>:9200/my_index/my_type/_search?q=title:T*`

-   `<HOST>`：表示阿里云ES实例内网/公网地址，详情请参见阿里云ES实例基本信息界面。

## 复杂搜索 {#section_if1_ysl_zgb .section}

必须使用`HTTP POST`对文档进行复杂搜索：

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

-   `<HOST>`：表示阿里云ES实例内网/公网地址，详情请参见阿里云ES实例基本信息界面。

## 删除文档 {#section_x3x_ysl_zgb .section}

`$ curl http://<HOST>:9200/{index}/{type}/{id} -XDELETE`

-   `<HOST>`：表示阿里云ES实例内网/公网地址，详情请参见阿里云ES实例基本信息界面。

## 删除指定类型文档 {#section_elk_zsl_zgb .section}

`$ curl http://<HOST>:9200/{index}/{type} -XDELETE`

-   `<HOST>`：表示阿里云ES实例内网/公网地址，详情请参见阿里云ES实例基本信息界面。

## 删除一个索引 {#section_nmz_zsl_zgb .section}

`$ curl http://<HOST>:9200/{index} -XDELETE`

-   `<HOST>`：表示阿里云ES实例内网/公网地址，详情请参见阿里云ES实例基本信息界面。

