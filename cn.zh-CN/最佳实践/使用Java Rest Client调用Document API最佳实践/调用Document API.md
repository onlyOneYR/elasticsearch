# 调用Document API {#task_525695 .task}

本文档为您介绍使用Java High Level REST Client调用Elasticsearch Document API的方法，提供了连接阿里云Elasticsearch集群、创建索引、创建文档、获取文档、更新文档等操作的示例代码供您参考。

-   安装JDK并配置Java环境变量。

    本案例使用的是jdk1.8.0\_211（其他版本不保证兼容），可单击[此处](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)在官网上下载。

-   准备一个Java程序开发工具。

    本案例使用的开发工具是eclipse，您也可以使用其他工具。


本文档提供的示例代码是基于Java High Level REST Client 6.3.x版本的，其他版本不保证兼容。

1.  创建一个Maven项目。![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/422735/156758163848849_zh-CN.png)

 本案例的项目配置如下。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/422735/156758163848850_zh-CN.png)

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/422735/156758163848851_zh-CN.png)

2.  配置pom.xml文件，添加如下的dependency依赖。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/422735/156758163848919_zh-CN.png)

    ``` {#codeblock_v5u_qub_fyu}
    <dependency>
         <groupId>org.elasticsearch.client</groupId>
         <artifactId>elasticsearch-rest-high-level-client</artifactId>
         <version>6.3.2</version>
    </dependency>
    ```

3.  创建一个Package，并在此Package下创建所需的Java文件。 

    本案例的Package配置如下。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/422735/156758163948914_zh-CN.png)

    本案例的Java文件配置如下。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/422735/156758163948917_zh-CN.png)

    配置完成后，项目的目录如下。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/422735/156758163948918_zh-CN.png)

    -   IndexAPI\_use.java：创建索引和文档。
    -   UpdateAPI\_use.java：更新文档。
    -   GetAPI\_use.java：获取文档。
    -   DeleteAPI\_use.java：删除文档。
    **说明：** 您也可以根据实际需求创建其他Java文件，本案例仅供参考。

4.  在IndexAPI\_use.java文件中，参考如下示例代码，连接您的阿里云Elasticsearch集群，并使用Java IndexRequest API创建索引。 部分示例代码如下：

    ``` {#codeblock_s85_lcv_j5e}
    //连接Elasticsearch集群
    final CredentialsProvider credentialsProvider = new BasicCredentialsProvider();
    
            credentialsProvider.setCredentials(AuthScope.ANY,
                    new UsernamePasswordCredentials("elastic", "替换为密码"));
    
    
            RestClientBuilder builder = RestClient.builder(new HttpHost("替换为ES实例ID.public.elasticsearch.aliyuncs.com", 9200))
                    .setHttpClientConfigCallback(new RestClientBuilder.HttpClientConfigCallback() {
    
                        public HttpAsyncClientBuilder customizeHttpClient(HttpAsyncClientBuilder httpClientBuilder) {
                            return httpClientBuilder.setDefaultCredentialsProvider(credentialsProvider);
                        }
                    });
    ```

    ``` {#codeblock_52x_imt_edo}
    //使用Java IndexRequest API创建索引
    RestHighLevelClient client = new RestHighLevelClient(builder);
            try {
                IndexRequest request = new IndexRequest();
                request.index("apitest_index");
                request.type("apitest_type");
                request.id("1");
                Map<String, Object> source = new HashMap<>();
                source.put("user", "kimchy");
                source.put("post_date", new Date());
                source.put("message", "trying out Elasticsearch");
                request.source(source);
                try {
                    IndexResponse result = client.index(request);
                    System.out.println(result);
                } catch (IOException e) {
                    e.printStackTrace();
                }
            } finally {
                client.close();
            }
    ```

    可单击[此处](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/111308/cn_zh/1560148511073/IndexAPI_use.java)下载完整的示例代码，最终打印结果如下。

    ``` {#codeblock_7w3_z2p_ywp}
    IndexResponse
    [
    index=apitest_index,
    type=apitest_type,
    id=1,
    version=1,
    result=created,
    seqNo=0,
    primaryTerm=1,
    shards={"total":2,"successful":1,"failed":0}
    ]
    ```

5.  在UpdateAPI\_use.java文件中，参考如下示例代码，使用Java UpdateRequest API更新文档。 

    部分示例代码如下。

    ``` {#codeblock_9hd_1z5_ygz}
    RestHighLevelClient client = new RestHighLevelClient(builder);
         try {
    
                UpdateRequest updateRequest = new UpdateRequest("apitest_index", "apitest_type", "1");
                IndexRequest indexRequest = new IndexRequest("apitest_index", "apitest_type", "1");
                Map<String, String> source = new HashMap<>();
                source.put("user", "dingw2");
                indexRequest.source(source);
                updateRequest.doc(indexRequest);
                UpdateResponse result = client.update(updateRequest);
                System.out.println(result);
    
            }catch (IOException e) {
                    e.printStackTrace();
            } finally {
                client.close();
            }
    ```

    可单击[此处](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/111308/cn_zh/1560146630789/UpdateAPI_use.java)下载完整的示例代码，最终打印结果如下。

    ``` {#codeblock_fyu_v95_3o8}
    UpdateResponse
    [
    index=apitest_index,
    type=apitest_type,
    id=1,
    version=2,
    seqNo=1,
    primaryTerm=1,
    result=updated,
    shards=ShardInfo{
        total=2,
        successful=2,
        failures=[]
      }
    ]
    ```

6.  在GetAPI\_use.java文件中，参考如下示例代码，使用Java GetRequest API获取文档。 

    部分示例代码如下。

    ``` {#codeblock_ub7_tsv_ppg}
    RestHighLevelClient client = new RestHighLevelClient(builder);
         try {
                GetRequest request = new GetRequest("apitest_index", "apitest_type", "1");
                GetResponse result = client.get(request);
                System.out.println(result);
    
            }catch (IOException e) {
                    e.printStackTrace();
            } finally {
                client.close();
            }
    ```

    可单击[此处](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/111308/cn_zh/1560147643769/GetAPI_use.java)下载完整的示例代码，最终打印结果如下。

    ``` {#codeblock_4zo_n5z_e7f}
    {
        "_index": "apitest_index",
        "_type": "apitest_type",
        "_id": "1",
        "_version": 2,
        "found": true,
        "_source": {
            "post_date": "2019-06-10T05:50:52.752Z",
            "message": "trying out Elasticsearch",
            "user": "dingw2"
        }
    }
    ```

7.  在DeleteAPI\_use.java文件中，参考如下示例代码，使用Java DeleteRequest API删除文档。 

    部分示例代码如下。

    ``` {#codeblock_4cd_h1z_9ls}
    RestHighLevelClient client = new RestHighLevelClient(builder);
         try {
                DeleteRequest request = new DeleteRequest("apitest_index", "apitest_type", "1");
                DeleteResponse result = client.delete(request);
                System.out.println(result);
            } catch(Throwable e) {
                e.printStackTrace();
            } finally {
                client.close();
            }
    ```

    可单击[此处](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/111308/cn_zh/1560148219617/DeleteAPI_use.java)下载完整的示例代码，最终打印结果如下。

    ``` {#codeblock_2jl_8lj_2f5}
    DeleteResponse
    [
    index=apitest_index,
    type=apitest_type,
    id=1,
    version=3,
    result=deleted,
    shards=ShardInfo{
        total=2,
        successful=2,
        failures=[]
      }
    ]
    ```


根据您的实际需求，调用其他Document API完成相关操作，其他API的使用示例请参考[Elasticsearch官方文档](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/6.3/java-rest-high-supported-apis.html)。

