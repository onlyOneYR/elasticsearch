# Security configuration {#concept_sjs_cts_zgb .concept}

This topic describes the security configuration of Alibaba Cloud Elasticsearch, including the Elasticsearch instance password, public network whitelist, VPC whitelist, and HTTPS protocol.

## Network settings {#section_awy_xct_zgb .section}

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134295/156231777740173_en-US.png)

You can reset the [Elasticsearch instance password](#section_f1d_yct_zgb), configure the [VPC whitelist](#section_ztl_yct_zgb), enable [Public network access](#section_elq_yct_zgb), and configure the [Public network whitelist](#section_ux5_yct_zgb) and [Enable HTTPS](#section_i7x_sqt_enx) in network settings.

## Elasticsearch instance password {#section_f1d_yct_zgb .section}

To reset the Elasticsearch instance password, click **Reset**, and enter a new password for the administrator account **elastic**. After you reset the password, it takes up to 5 minutes for the new password to take effect.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134295/156231777840174_en-US.jpg)

If you use the **elastic** account to log on to the Alibaba Cloud Elasticsearch instance or Kibana console, then you must use the new password.

**Note:** 

-   The reset operation only resets the password of the **elastic** account. The operation does not reset the password of other accounts that are used to log on to the instance. We recommend that you do not use the **elastic** account to log on to your Alibaba Cloud Elasticsearch instance.
-   The **Reset** operation does not restart the Alibaba Cloud Elasticsearch instance.

## VPC whitelist {#section_ztl_yct_zgb .section}

When you need to access an Alibaba Cloud Elasticsearch instance from an ECS instance in a VPC network, you must add the IP address of the ECS instance to the VPC whitelist.

Click **Update**, enter the IP address in the VPC whitelist dialog box, and click **OK**.

You can add IP addresses and CIDR blocks to the whitelist in the format of `192.168.0.1` and `192.168.0.0/24`, respectively. Separate these IP addresses and CIDR blocks with commas \(,\). Enter `127.0.0.1` to forbid all IPv4 addresses or enter `0.0.0.0/0` to allow all IPv4 addresses.

**Note:** 

-   By default, all private IPv4 addresses are allowed to access Elasticsearch.
-   The VPC whitelist is used to control access from internal network addresses in VPC networks.

## Public network access {#section_elq_yct_zgb .section}

Click the **Public Network Access** switch to enable public network access. After this feature is enabled, the switch is in **green**. By default, the switch is in gray, which means that public network access is disabled. To access your Alibaba Cloud Elasticsearch instance through the Internet, you must enable public network access.

## Public network whitelist {#section_ux5_yct_zgb .section}

Before you configure the public network whitelist, you must toggle on the **Public Network Access** switch. By default, the public network access feature forbids all public network addresses.

To access your Alibaba Cloud Elasticsearch instance through the Internet, you must add the IP address of your client to the public network whitelist.

You can add IP addresses and CIDR blocks in the format of `192.168.0.1` and `192.168.0.0/24`, respectively. Separate these IP addresses and CIDR blocks with commas \(,\). Enter `127.0.0.1` to forbid all IPv4 addresses or enter `0.0.0.0/0` to allow all IPv4 addresses.

If your Elasticsearch instance is deployed in the China \(Hangzhou\) region, then you can add IPv6 addresses and CIRD blocks to the whitelist in the format of `2401:b180:1000:24::5` and `2401:b180:1000::/48`, respectively. Enter `::1` to forbid all IPv6 addresses or enter `::/0` to allow all IPv6 addresses.

## Enable HTTPS {#section_i7x_sqt_enx .section}

Hypertext Transfer Protocol Secure \(HTTPS\) is a secure version of HTTP. HTTPS uses Secure Socket Layer \(SSL\) for secure data transmission. This means that HTTPS still uses HTTP for communications. SSL is used to encrypt the data.

Procedure

**Note:** 

-   Alibaba Cloud Elasticsearch allows you to enable and disable HTTPS. To protect your data, we recommend that you enable HTTPS.
-   Before you enable HTTPS, you must purchase client nodes.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134295/156231777849770_en-US.png)


1.  Log on to the [Alibaba Cloud Elasticsearch console](https://elasticsearch.console.aliyun.com/), click **Instance ID/Name** \> **Security**, and click the **HTTPS** switch to enable HTTPS.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134295/156231777849772_en-US.png)

    **Note:** 

    -   Before you enable HTTPS, you must update the code of the client that is used to access the Elasticsearch instance. Otherwise, you may fail to access the instance. For more information, see [Sample client code for enabling or disabling HTTPS](#).
    -   During the process of enabling or disabling HTTPS, the services running on the instance will be interrupted and the instance will be restarted. Before you enable or disable HTTPS, make sure that your businesses will not be adversely affected.
2.  In the Confirm Operation dialog box, select **I have edited the code of the Elasticsearch instance**, and then click **OK**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134295/156231777849773_en-US.png)

    **Note:** If you have not purchased client nodes, after you enable **HTTPS**, the system prompts a notification requiring you to purchase client nodes. You can follow the instructions to purchase client nodes.

    After you confirm to enable or disable HTTPS, the instance will restart. You can click the Tasks icon in the upper-right corner to check the progress. After the instance is restarted, you can then access the instance through HTTPS.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134295/156231777849774_en-US.png)


Sample client code for enabling or disabling HTTPS

The following example shows the changes that need to be made to the code of the Elasticsearch REST client after you enable HTTPS.

-   The code of the REST client before HTTPS is enabled:

    ``` {#codeblock_fjj_8ce_w5w}
    final CredentialsProvider credentialsProvider = new BasicCredentialsProvider();
            credentialsProvider.setCredentials(AuthScope.ANY,
                new UsernamePasswordCredentials("elastic", "Your password"));
    RestClientBuilder restClientBuilder = RestClient.builder(
                new HttpHost("es-cn-xxxxx.elasticsearch.aliyuncs.com", 9200));
            RestClient restClient = restClientBuilder.setHttpClientConfigCallback(
                new RestClientBuilder.HttpClientConfigCallback() {
                    @Override
                    public HttpAsyncClientBuilder customizeHttpClient(HttpAsyncClientBuilder httpClientBuilder) {
                        return httpClientBuilder.setDefaultCredentialsProvider(credentialsProvider);
                    }
                }).build();
    ```

-   The code of the REST client after HTTPS is enabled:

    ``` {#codeblock_rs8_7he_dui}
    final CredentialsProvider credentialsProvider = new BasicCredentialsProvider();
            credentialsProvider.setCredentials(AuthScope.ANY,
                new UsernamePasswordCredentials("elastic", "Your password"));
    RestClientBuilder restClientBuilder = RestClient.builder(
                new HttpHost("es-cn-xxxxx.elasticsearch.aliyuncs.com", 9200, "https"));
            RestClient restClient = restClientBuilder.setHttpClientConfigCallback(
                new RestClientBuilder.HttpClientConfigCallback() {
                    @Override
                    public HttpAsyncClientBuilder customizeHttpClient(HttpAsyncClientBuilder httpClientBuilder) {
                        return httpClientBuilder.setDefaultCredentialsProvider(credentialsProvider);
                    }
                }).build();
    ```


As shown in the preceding example, after you enable HTTPS, you must include the `https` parameter in `HttpHost`: `new HttpHost("es-cn-xxxxx.elasticsearch.aliyuncs.com", 9200, "https"));`

