# AliNLP分词器最佳实践 {#concept_1664077 .concept}

AliNLP分词器即analysis-aliws，是阿里云Elasticsearch自带的一个系统默认插件。通过analysis-aliws插件，您可以在Elasticsearch中集成对应的分析器和分词器，完成文档的分析和检索。

## 安装AliNLP分词器 {#section_19x_pwz_omz .section}

登录[阿里云Elasticsearch控制台](https://elasticsearch-cn-hangzhou.console.aliyun.com/)，单击**实例ID** \> **插件配置** \> **系统默认插件列表** 。在**系统默认插件列表**列表中安装**analysis-aliws**插件，详情请参见[卸载/安装系统默认插件](../cn.zh-CN/用户指南/实例管理/插件配置/系统默认插件列表.md#section_d0y_kyx_fu0)。

![analysis-aliws插件](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1318926/156698171455100_zh-CN.png)

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

    以上代码创建了名称为`index`的索引，类型为`fulltext`，属性为`content`，并添加了`aliws`分析器。

2.  添加文档。

    ``` {#codeblock_w82_3v5_suk}
    POST /index/fulltext/1
    {
      "content": "中华人民共和国国歌。"
    }
    ```

    以上代码创建了名称为`1`的文档，并设置了文档中的`content`字段的内容。

3.  查询。

    ``` {#codeblock_dsj_kcy_601}
    POST /index/fulltext/_search
    {
      "query": {
        "match": {
          "content": "共和国"
        }
      }
    }
    ```

    以上代码在所有`fulltext`类型的文档中，使用`aliws`分析器，搜索`content`字段中包含`共和国`的文档。


**说明：** 如果您在使用analysis-aliws插件时，得到的结果不符合预期，可通过下文的[分析器测试](#section_iwo_xqf_x3z)和[分词器测试](#section_zy5_iac_evf)进行排查调试。

## 分析器测试 {#section_iwo_xqf_x3z .section}

``` {#codeblock_pty_046_z30}
GET _analyze
{
  "text": "中华人民共和国国歌。",
  "analyzer": "aliws"
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

