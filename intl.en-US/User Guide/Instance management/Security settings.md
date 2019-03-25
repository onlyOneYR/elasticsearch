# Security settings {#concept_sjs_cts_zgb .concept}

## Cluster network settings {#section_awy_xct_zgb .section}

You can reset the **Elasticsearch cluster password**, modify the **Kibana IP whitelist** and **VPC IP whitelist**, and enable **public addresses** and then configure the **public IP whitelist**.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134295/155350204540173_en-US.png)

## Elasticsearch cluster password {#section_f1d_yct_zgb .section}

**The password reset function** resets the password of the administrator account **elastic**. After you reset the password, you can only use your new password to log on to the Kibana console and access Elasticsearch instances.

**Note:** 

-   The password reset operation does not change the password of **non-elastic** administrator accounts. We recommend that you do not use the **elastic** administrator account to access Elasticsearch instances.
-   The new password will take effect 5 minutes after you submit the change.
-   The password reset operation does not restart the corresponding Alibaba Cloud Elasticsearch instance.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134295/155350204540174_en-US.jpg)

## Kibana IP whitelist {#section_x5h_yct_zgb .section}

You can add **comma-separated IP addresses** or **CIDR blocks** to the Kibana IP whitelist, for example, `192.168.0.1` or `192.168.0.0/24`. Set the Kibana IP whitelist to `127.0.0.1` to forbid all IPv4 addresses. Set the Kibana IP whitelist to `0.0.0.0/0` to allow all IPv4 addresses.

Currently, only the China \(Hangzhou\) region supports using public IPv6 addresses to access Elasticsearch instances. This region also supports the Kibana IPv6 whitelist. You can add IPv6 addresses or CIDR blocks to the Kibana IPv6 whitelist, such as `2401:b180:1000:24::5` or `2401:b180:1000::/48`. Set the Kibana IPv6 whitelist to `::1` to forbid all IPv6 addresses. Set the Kibana IPv6 whitelist to `::/0` to allow all IPv6 addresses.

**Note:** By default, all public IP addresses are allowed to access Elasticsearch.

## VPC IP whitelist {#section_ztl_yct_zgb .section}

You can add **comma-separated IP** addresses or **CIDR blocks** to the VPC IP whitelist, for example, `192.168.0.1` or `192.168.0.1/24`. Set the VPC IP whitelist to `127.0.0.1` to forbid all IPv4 addresses. Set the VPC IP whitelist to `0.0.0.0/0` to allow all IPv4 addresses.

**Note:** 

-   By default, all VPC IPv4 addresses are allowed to access Elasticsearch.
-   This whitelist is used to control access from VPCs to Elasticsearch.

## Public addresses {#section_elq_yct_zgb .section}

Toggle the Public Address switch to **green** to enable the public address function. **By default, this function is disabled**.

## Public IP address whitelist {#section_ux5_yct_zgb .section}

You can add **comma-separated IP addresses** or **CIDR blocks** to the public IP address whitelist, for example, `192.168.0.1` or `192.168.0.1/24`. Set the public IP address whitelist to `127.0.0.1` to forbid all IPv4 addresses. Set the public IP address whitelist to `0.0.0.0/0` to allow all IPv4 addresses.

Currently, only the China \(Hangzhou\) region supports using public IPv6 addresses to access Elasticsearch instances. This region also supports the public IPv6 whitelist. You can add IPv6 addresses or CIDR blocks to the public IPv6 whitelist, such as `2401:b180:1000:24::5` or `2401:b180:1000::/48`. Set the public IPv6 whitelist to `::1` to forbid all IPv6 addresses. Set the public IPv6 whitelist to `::/0` to allow all IPv6 addresses.

**Note:** By default, the public address function forbids all public IP addresses.

