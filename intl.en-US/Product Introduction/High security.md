# High security {#concept_vjk_mck_zgb .concept}

## Access an Alibaba Cloud Elasticsearch instance through its internal IP address {#section_yfr_klk_zgb .section}

You can access an Alibaba Cloud Elasticsearch instance through its **internal IP address** from a VPC network. If you want to enhance the security of your access, create an Alibaba Cloud Elastic Compute Service \(ECS\) instance in the same region and VPC network where the Alibaba Cloud Elasticsearch instance is created. You can then deploy applications on this ECS instance and use the ECS instance to access the internal IP address of the Alibaba Cloud Elasticsearch instance.

**Note:** A VPC network is isolated from the public network and provides a more secure access environment.

## Access control {#section_ygw_klk_zgb .section}

**Configure a whitelist**

You can configure a whitelist or blacklist to limit the access to the **internal IP address** of an Alibaba Cloud Elasticsearch instance. Only whitelisted or non-blacklisted IP addresses can access the Alibaba Cloud Elasticsearch instance. For more information, see [Elasticsearch cluster configuration](../../../../../reseller.en-US/User Guide/Instance management/Elasticsearch cluster configuration.md).

You can configure a whitelist to limit the access to the **public IP address** of an Alibaba Cloud Elasticsearch instance. Only whitelisted IP addresses can access the Alibaba Cloud Elasticsearch instance. For more information, see [Security settings](../../../../../reseller.en-US/User Guide/Instance management/Security settings.md).

**RAM-based access control**

Alibaba Cloud Elasticsearch allows you to create RAM users to manage access permissions. Resources of different RAM users are isolated from each other. RAM users can only manage and view Alibaba Cloud Elasticsearch instances created under their own accounts. For more information, see [Access authentication rules](../../../../../reseller.en-US/User Guide/RAM/Access authentication rules.md).

**X-Pack role-based access control**

Alibaba Cloud Elasticsearch allows you to use X-Pack. X-Pack is an Elastic Stack extension that bundles security, alerting, monitoring, reporting, and graph capabilities into one easy-to-install package. X-Pack can be installed in Kibana and provides a wide range of features, such as authentication, permission control, real-time monitoring, visualized reports, and machine learning. X-Pack role-based access control allows you to authenticate access requests to indexes. For more information, see [Security APIs](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/security-api.html).

## System security {#section_mcd_llk_zgb .section}

-   You can access an Alibaba Cloud Elasticsearch instance through a VPC network. A VPC network provides a more secure access environment.
-   You cannot log on to any servers of the nodes that are contained in an Elasticsearch instance.
-   No IP addresses are allowed to access the public IP address of an Elasticsearch instance by default. To allow access requests to the public IP address, you must configure a public IP whitelist. For more information, see [Public IP address whitelist](../../../../../reseller.en-US/User Guide/Instance management/Security settings.md#section_ux5_yct_zgb).
-   You can create a whitelist to limit the access to the public and internal IP addresses of an Alibaba Cloud Elasticsearch instance.
-   An Alibaba Cloud Elasticsearch instance opens ports 9200 and 9300 only, and enables you to access its public and internal IP addresses.

    **Note:** Port `9300` is closed for Alibaba Cloud Elasticsearch 6.3.2 that has X-Pack installed.


