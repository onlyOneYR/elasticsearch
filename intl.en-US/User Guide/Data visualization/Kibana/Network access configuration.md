# Network access configuration {#concept_761683 .concept}

This topic describes the network access configuration of Kibana clusters. The network access configuration includes the public network access configuration and Kibana whitelist.

## Go to the network access configuration page {#section_3ln_939_f2d .section}

1.  Log on to the [Alibaba Cloud Elasticsearch console](https://elasticsearch.console.aliyun.com/), and click **Instance ID/Name** \> **Data Visualization**.
2.  Click **Edit Configuration** under **Kibana** to go to the **Kibana Configuration** page.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156231254949321_en-US.png)

    You can then view the **Network Access Configuration** on the **Kibana Configuration** page. In the **Network Access Configuration** area, you can enable or disable [Public network access](#section_bbj_euc_ly7), and configure the [Kibana whitelist](#section_ovn_tjs_bcm). By default, the public network access feature is enabled.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/614906/156231254949791_en-US.png)


## Public network access {#section_bbj_euc_ly7 .section}

By default, the **Public Network Access** switch is toggled on \(green\). You can click the **Public Network Access** switch to disable this feature. When this feature is disabled, the switch is **gray**. When the **Public Network Access** feature is disabled, you cannot log on to the Kibana console through the Internet.

## Kibana whitelist {#section_ovn_tjs_bcm .section}

To configure the Kibana whitelist, click **Update** next to the Kibana whitelist, enter IP addresses into the dialog box, and click **OK**.

**Note:** By default, all public network addresses are allowed to access the Kibana console.

The Kibana console supports both IP addresses and CIDR blocks. Enter IP addresses and CIDR blocks in the format of `192.168.0.1` and `192.168.0.0/24`, respectively. Separate these IP addresses and CIDR blocks with commas \(,\). You can enter `127.0.0.1` to forbid all IPv4 addresses or enter `0.0.0.0/0` to allow all IPv4 addresses.

If your Kibana node is deployed in the China \(Hangzhou\) region, then you can add IPv6 addresses to the Kibana whitelist. Enter IPv6 addresses and CIDR blocks in the format of `2401:b180:1000:24::5` and `2401:b180:1000::/48`, respectively. Enter `::1` to forbid all IPv6 addresses and enter `::/0` to allow all IPv6 addresses.

