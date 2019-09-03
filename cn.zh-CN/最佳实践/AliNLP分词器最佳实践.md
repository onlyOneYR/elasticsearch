# AliNLP分词器最佳实践 {#concept_1664077 .concept}

AliNLP分词器即analysis-aliws，是阿里云Elasticsearch自带的一个系统默认插件。通过analysis-aliws插件，您可以在Elasticsearch中集成对应的分析器和分词器，完成文档的分析和检索。

## 安装AliNLP分词器 {#section_19x_pwz_omz .section}

**说明：** 在安装AliNLP分词器前，请确保您的实例为8G及以上规格。如果不满足，需要首先将**实例规格**升级至8G及以上，详情请参见[集群升配](../cn.zh-CN/用户指南/实例管理/集群升配.md#)。

登录[阿里云Elasticsearch控制台](https://elasticsearch-cn-hangzhou.console.aliyun.com/)，单击**实例ID** \> **插件配置** \> **系统默认插件列表** 。在**系统默认插件列表**列表中安装**analysis-aliws**插件，详情请参见[卸载/安装系统默认插件](../cn.zh-CN/用户指南/实例管理/插件配置/系统默认插件列表.md#section_d0y_kyx_fu0)。

![analysis-aliws插件](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1318926/156751414655100_zh-CN.png)

**说明：** **analysis-aliws**插件默认为**未安装**状态。

## 使用AliNLP分词器 {#section_15b_w7b_i6y .section}

AliNLP分词器安装成功后，阿里云Elasticsearch默认会集成如下的分析器和分词器。

-   分析器：aliws
-   分词器：aliws\_tokenizer

您可以使用上述的分析器和分词器完成文档的查询，具体步骤如下。

1.  创建索引。

    ``` {#codeblock_ys1_b05_fve}
    PUT /index
    {
        "mappings": {
            "fulltext": {
                "properties": {
                    "content": {
                        "type": "text",
                        "analyzer": "aliws"
                    }
                }
            }
        }
    }
    ```

    以上代码创建了名称为`index`的索引，类型为`fulltext`。包含了一个`content`属性，类型为`text`，并添加了`aliws`分析器。

    执行成功后，返回如下结果：

    ``` {#codeblock_y2c_l62_im7}
    {
      "acknowledged": true,
      "shards_acknowledged": true,
      "index": "index"
    }
    ```

2.  添加文档。

    ``` {#codeblock_w82_3v5_suk}
    POST /index/fulltext/1
    {
      "content": "中华人民共和国国歌。"
    }
    ```

    以上代码创建了名称为`1`的文档，并设置了文档中的`content`字段的内容为`中华人民共和国国歌。`。

    执行成功后，返回如下结果：

    ``` {#codeblock_kcz_94l_xn6}
    {
      "_index": "index",
      "_type": "fulltext",
      "_id": "1",
      "_version": 1,
      "result": "created",
      "_shards": {
        "total": 2,
        "successful": 2,
        "failed": 0
      },
      "_seq_no": 0,
      "_primary_term": 1
    }
    ```

3.  查询。

    ``` {#codeblock_dsj_kcy_601}
    GET /index/fulltext/_search
    {
      "query": {
        "match": {
          "content": "共和国"
        }
      }
    }
    ```

    以上代码在所有`fulltext`类型的文档中，使用`aliws`分析器，搜索`content`字段中包含`共和国`的文档。

    执行成功后，返回如下结果：

    ``` {#codeblock_wca_c0o_oit}
    {
      "took": 5,
      "timed_out": false,
      "_shards": {
        "total": 5,
        "successful": 5,
        "skipped": 0,
        "failed": 0
      },
      "hits": {
        "total": 1,
        "max_score": 0.2876821,
        "hits": [
          {
            "_index": "index",
            "_type": "fulltext",
            "_id": "2",
            "_score": 0.2876821,
            "_source": {
              "content": "中华人民共和国国歌。"
            }
          }
        ]
      }
    }
    ```


**说明：** 如果您在使用analysis-aliws插件时，得到的结果不符合预期，可通过下文的[分析器测试](#section_iwo_xqf_x3z)和[分词器测试](#section_zy5_iac_evf)进行排查调试。

## 分析器测试 {#section_iwo_xqf_x3z .section}

``` {#codeblock_pty_046_z30}
GET _analyze
{
  "text": "中华人民共和国国歌。",
  "analyzer": "aliws"
}
```

返回结果如下：

``` {#codeblock_np5_s8f_g60}
{
  "tokens": [
    {
      "token": "中华",
      "start_offset": 0,
      "end_offset": 2,
      "type": "word",
      "position": 0
    },
    {
      "token": "人民",
      "start_offset": 2,
      "end_offset": 4,
      "type": "word",
      "position": 1
    },
    {
      "token": "共和国",
      "start_offset": 4,
      "end_offset": 7,
      "type": "word",
      "position": 2
    },
    {
      "token": "国歌",
      "start_offset": 7,
      "end_offset": 9,
      "type": "word",
      "position": 3
    }
  ]
}
```

## 分词器测试 {#section_zy5_iac_evf .section}

``` {#codeblock_05o_1so_4qh}
GET _analyze
{
  "text": "中华人民共和国国歌。",
  "tokenizer": "aliws_tokenizer"
}
```

返回结果如下：

``` {#codeblock_a76_d84_9v8}
{
  "tokens": [
    {
      "token": "中华",
      "start_offset": 0,
      "end_offset": 2,
      "type": "word",
      "position": 0
    },
    {
      "token": "人民",
      "start_offset": 2,
      "end_offset": 4,
      "type": "word",
      "position": 1
    },
    {
      "token": "共和国",
      "start_offset": 4,
      "end_offset": 7,
      "type": "word",
      "position": 2
    },
    {
      "token": "国歌",
      "start_offset": 7,
      "end_offset": 9,
      "type": "word",
      "position": 3
    },
    {
      "token": "。",
      "start_offset": 9,
      "end_offset": 10,
      "type": "word",
      "position": 4
    }
  ]
}
```

