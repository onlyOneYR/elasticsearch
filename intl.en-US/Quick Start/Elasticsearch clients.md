# Elasticsearch clients {#concept_rsj_dmk_zgb .concept}

This topic describes how to use the PHP, Python, and Java clients of Elasticsearch to access Alibaba Cloud Elasticsearch. Client examples and usage guidelines are also provided for your reference.

## PHP client {#section_p4e_bdp_qvq .section}

**Warning:** 

-   The default connection pool provided by the PHP client of Elasticsearch is not suitable for accessing services on the cloud. Alibaba Cloud Elasticsearch handles requests sent from clients based on load balancing. Therefore, the PHP client must use the SimpleConnectionPool method to create a connection pool. Otherwise, when the Alibaba Cloud Elasticsearch is restarted, a connection error occurs. This may adversely affect your businesses.
-   However, even if the connection pool is created by using the SimpleConnectionPool method, a connection error may occur when the Elasticsearch instance is restarted. For example, the "No enabled connection" may occur. To resolve this issue, make sure that the PHP client supports auto reconnection.

To test the connectivity to your Elasticsearch instance, connect the PHP client to port 9200 on your instance. The client example is as follows. For more information, see [elasticsearch-php](https://www.elastic.co/guide/en/elasticsearch/client/php-api/6.7.x/index.html).

``` {#codeblock_f0k_c5y_ha5}
<? php
require 'vendor/autoload.php';
use Elasticsearch\ClientBuilder;

$client = ClientBuilder::create()->setHosts([
  [
    'host'   => '<HOST>',
    'port'   => '9200',
    'scheme' => 'http',
    'user'   => '<USER NAME>',
    'pass'   => '<PASSWORD>'
  ]
])->setConnectionPool('\Elasticsearch\ConnectionPool\SimpleConnectionPool', [])
  ->setRetries(10)->build();

$indexParams = [
  'index'  => 'my_index',
  'type'   => 'my_type',
  'id'     => '1',
  'body'   => ['testField' => 'abc'],
  'client' => [
    'timeout'         => 10,
    'connect_timeout' => 10
  ]
];
$indexResponse = $client->index($indexParams);
print_r($indexResponse);

$searchParams = [
  'index'  => 'my_index',
  'type'   => 'my_type',
  'body'   => [
    'query' => [
      'match' => [
        'testField' => 'abc'
      ]
    ]
  ],
  'client' => [
    'timeout'         => 10,
    'connect_timeout' => 10
  ]
];
$searchResponse = $client->search($searchParams);
print_r($searchResponse);
? >
```

-   `<USER NAME>`: replace USER NAME with the username of your Elasticsearch instance.
-   `<PASSWORD>`: replace PASSWORD with the password of your Elasticsearch instance.
-   `<HOST>`: replace HOST with the public or internal network address on the [Basic information](../../../../reseller.en-US/User Guide/Instance management/Basic information.md#) page of your Elasticsearch instance.

## Python client {#section_ktr_xyl_zgb .section}

To test the connectivity to your Elasticsearch instance, connect the Python client to port 9200 on your instance. The client example is as follows. For more information, see [elasticsearch-py](https://www.elastic.co/guide/en/elasticsearch/client/python-api/current/index.html).

``` {#codeblock_b9d_hxk_skc}
from elasticsearch import Elasticsearch, RequestsHttpConnection
import certifi
es = Elasticsearch(
    ['<HOST>'],
    http_auth=('username', 'password'),
    port=9200,
    use_ssl=False
)
res = es.index(index="my_index", doc_type="my_type", id=1, body={"title": "One", "tags": ["ruby"]})
print(res['created'])
res = es.get(index="my-index", doc_type="my-type", id=1)
print(res['_source'])
```

## Java client {#section_vlw_xyl_zgb .section}

**Note:** 

-   The official Elasticsearch team no longer maintains the TransportClient. We recommend that you do not use the TransportClient to access Alibaba Cloud Elasticsearch. A `NoNodeAvailableException` error occurs when you use TransportClient 5.5.3 to access Alibaba Cloud Elasticsearch. We recommend that you use the [Java low level REST client](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/5.5/_basic_authentication.html) to access Alibaba Cloud Elasticsearch.
-   The Java REST client described in this topic is only compatible with Alibaba Cloud Elasticsearch 5.5.3. It is incompatible with Alibaba Cloud Elasticsearch 6.3.2. If you are using Alibaba Cloud Elasticsearch 6.3.2, reference the official Elasticsearch document [Java REST Client 6.3.2](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/6.3/index.html).
-   The version of the Java REST client must be same as the Elasticsearch instance version.

 Preparations 

-   [Create an Alibaba Cloud Elasticsearch instance](reseller.en-US/Quick Start/Activate Alibaba Cloud Elasticsearch.md#), select Elasticsearch 5.5.3, and then [enable auto-indexing](../../../../reseller.en-US/User Guide/Instance management/Elasticsearch cluster configuration.md). If you have manually created indexes and mappings, then you do not need to enable auto-indexing.
-   Install the JDK and configure environment variables. The JDK version must be 1.8 or later.

To test the connectivity to your Elasticsearch instance, connect the Java REST client to port 9200 on your instance. The client example is as follows:

``` {#codeblock_xiu_cju_ikl}
import org.apache.http.HttpEntity;
import org.apache.http.HttpHost;
import org.apache.http.auth.AuthScope;
import org.apache.http.auth.UsernamePasswordCredentials;
import org.apache.http.client.CredentialsProvider;
import org.apache.http.entity.ContentType;
import org.apache.http.impl.client.BasicCredentialsProvider;
import org.apache.http.impl.nio.client.HttpAsyncClientBuilder;
import org.apache.http.nio.entity.NStringEntity;
import org.apache.http.util.EntityUtils;
import org.elasticsearch.client.Response;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestClientBuilder;
import java.io.IOException;
import java.util.Collections;
public class RestClientTest {
    public  static void main(String[]args){
        final CredentialsProvider credentialsProvider = new BasicCredentialsProvider();
        credentialsProvider.setCredentials(AuthScope.ANY,
                new UsernamePasswordCredentials("<USER NAME>", "<PASSWORD>"));
        RestClient restClient = RestClient.builder(new HttpHost("<HOST>", 9200))
                .setHttpClientConfigCallback(new RestClientBuilder.HttpClientConfigCallback() {
                    @Override
                    public HttpAsyncClientBuilder customizeHttpClient(HttpAsyncClientBuilder httpClientBuilder) {
                        return httpClientBuilder.setDefaultCredentialsProvider(credentialsProvider);
                    }
                }).build();
        try {
            //index a document
            HttpEntity entity = new NStringEntity("{\n\"user\" : \"kimchy\"\n}", ContentType.APPLICATION_JSON);
            Response indexResponse = restClient.performRequest(
                    "PUT",
                    "/index/type/123",
                    Collections. <String, String>emptyMap(),
                    entity);
            //search a document
            Response response = restClient.performRequest("GET", "/index/type/123",
                    Collections.singletonMap("pretty", "true"));
            System.out.println(EntityUtils.toString(response.getEntity()));
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

-   `<USER NAME>`: replace USER NAME with the username of your Elasticsearch instance.
-   `<PASSWORD>`: replace PASSWORD with the password of your Elasticsearch instance.
-   `<HOST>`: replace HOST with the public or internal network address shown on the **Basic Information** page of your Elasticsearch instance.

For more client examples for other languages, see [HTTP/REST Clients and Security](https://www.elastic.co/guide/en/x-pack/current/http-clients.html).

