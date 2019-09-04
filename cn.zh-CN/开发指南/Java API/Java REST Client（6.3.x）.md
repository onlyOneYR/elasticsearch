# Java REST Client（6.3.x） {#concept_1813575 .concept}

本文基于Java High Level REST Client 6.3.x版本，为您介绍Elasticsearch Java API的用法。

## 准备工作 {#section_rsh_0aj_osf .section}

-   安装Java：要求JDK为1.8及以上版本。
-   创建阿里云Elasticsearch实例：实例版本要求大于等于elasticsearch-rest-high-level-client的版本。本文创建一个6.3.2版本的实例。

    **说明：** High Level Client能够向上兼容，例如6.3.2版本的elasticsearch-rest-high-level-client能确保与大于等于6.3.2版本的Elasticsearch集群通信。为了保证最大程度地使用最新版客户端的特性，推荐High Level Client版本与集群版本一致。

-   创建Java Maven工程，并将如下的[pom依赖](#section_flr_0uz_6lx)添加到Java工程的pom.xml文件中。

    ![添加pom依赖](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1435822/156756731356770_zh-CN.png)

    **说明：** 本文以Elasticsearch 6.3.2版本为例。


## pom依赖 {#section_flr_0uz_6lx .section}

``` {#codeblock_sd3_6na_mi8}
<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-high-level-client</artifactId>
    <version>6.3.2</version>
</dependency>
```

## 示例 {#section_nlg_ey7_rsh .section}

以下示例使用Index API索引文档，随即使用Delete API删除该文档。

``` {#codeblock_nvp_wm3_8pg}
//文件路径为：src/main/java/RestClientTest.java。
import org.apache.http.HttpHost;
import org.apache.http.auth.AuthScope;
import org.apache.http.auth.UsernamePasswordCredentials;
import org.apache.http.client.CredentialsProvider;
import org.apache.http.impl.client.BasicCredentialsProvider;
import org.apache.http.impl.nio.client.HttpAsyncClientBuilder;

import org.elasticsearch.action.delete.DeleteRequest;
import org.elasticsearch.action.delete.DeleteResponse;
import org.elasticsearch.action.index.IndexRequest;
import org.elasticsearch.action.index.IndexResponse;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestClientBuilder;
import org.elasticsearch.client.RestHighLevelClient;

import java.io.IOException;
import java.util.HashMap;
import java.util.Map;

public class RestClientTest {
    public static void main(String[] args) {

        // 阿里云ES集群需要basic auth验证。
        final CredentialsProvider credentialsProvider = new BasicCredentialsProvider();
       //访问用户名和密码为您创建阿里云Elasticsearch实例时设置的用户名和密码，也是Kibana控制台的登录用户名和密码。
        credentialsProvider.setCredentials(AuthScope.ANY, new UsernamePasswordCredentials("{访问用户名}", "{访问密码}"));

       // 通过builder创建rest client，配置http client的HttpClientConfigCallback。
       // 单击所创建的Elasticsearch实例ID，在基本信息页面获取公网地址，即为ES集群地址。
        RestClientBuilder builder = RestClient.builder(new HttpHost("{ES集群地址}", 9200))
                .setHttpClientConfigCallback(new RestClientBuilder.HttpClientConfigCallback() {
                    @Override
                    public HttpAsyncClientBuilder customizeHttpClient(HttpAsyncClientBuilder httpClientBuilder) {
                        return httpClientBuilder.setDefaultCredentialsProvider(credentialsProvider);
                    }
                });

        // RestHighLevelClient实例通过REST low-level client builder进行构造。
        RestHighLevelClient highClient = new RestHighLevelClient(builder);

        try {
            // 创建request。
            Map<String, Object> jsonMap = new HashMap<>();
           // field_01、field_02为字段名，value_01、value_02为对应的值。
            jsonMap.put("{field_01}", "{value_01}");
            jsonMap.put("{field_02}", "{value_02}");
            //index_name为索引名称；type_name为类型名称；doc_id为文档的id。       
            IndexRequest indexRequest = new IndexRequest("{index_name}", "{type_name}", "{doc_id}").source(jsonMap);

            // 同步执行。
            IndexResponse indexResponse = highClient.index(indexRequest);

            long version = indexResponse.getVersion();

            System.out.println("Index document successfully! " + version);
            //index_name为索引名称；type_name为类型名称；doc_id为文档的id。与以上创建索引的名称和id相同。
            DeleteRequest request = new DeleteRequest("{index_name}", "{type_name}", "{doc_id}");
            DeleteResponse deleteResponse = highClient.delete(request);

            System.out.println("Delete document successfully!");

            highClient.close();

        } catch (IOException ioException) {
            // 异常处理。
        }
    }
}
```

以上示例代码中带`{}`的参数需要替换为您具体业务的参数，详情请参见代码注释。

