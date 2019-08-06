# Security features {#concept_1254520 .concept}

This topic compares Alibaba Cloud Elasticsearch instances with user-built Elasticsearch instances to describe the advantages of Alibaba Cloud Elasticsearch in security protection.

## Background {#section_yot_d7m_40n .section}

Open-source software has typically been the first choice of attackers, such as the [MongoDB ransomware attacks](https://help.aliyun.com/noticelist/articleid/20527251.html) event. Elasticsearch has also become the target of the attackers. They may attack user-created Elasticsearch services that do not have professional security protection, and then delete important data or intrude into the business system.

![Elasticsearch security background](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1000828/156505517852244_en-US.png)

Alibaba Cloud Security Center has released a note about security risk warning on Elasticsearch and provided multiple security hardening strategies and solutions. Compared with the security protection for user-built Elasticsearch instances, the solutions provided by [Alibaba Cloud Elasticsearch](https://data.aliyun.com/product/elasticsearch) for data and service security are more reliable and professional.

## Security feature descriptions {#section_eta_7uq_mkd .section}

Alibaba Cloud has released the fully-hosted Elasticsearch service in November 2017. Alibaba Cloud Elasticsearch provides security protection features for you to safeguard your services.

![Elasticsearch security features](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1000828/156505517852236_en-US.png)

The following table compares Alibaba Cloud Elasticsearch with user-built Elasticsearch services in security protection:

|Security indicator|Security protection for user-built Elasticsearch service|Integrated security features for Alibaba Cloud Elasticsearch|
|------------------|--------------------------------------------------------|------------------------------------------------------------|
|Access control| -   Purchase cloud security products, such as security groups or firewalls, to control and quarantine source IP addresses.
-   Disable port 9200 unless it is necessary.
-   Bind source IP addresses.
-   Change the default port.

 | -   Alibaba Cloud Elasticsearch instances are deployed in VPC networks. In this way, they can be isolated at the data link layer.
-   IPv4 and IPv6 whitelists for access control. Both IP addresses and CIDR blocks are supported.
-   Kibana whitelist for access control. Both IP addresses and CIDR blocks are supported.

 |
|Authentication and authorization|Install third-party security plug-ins, such as Searchguard and Shield.| -   Cluster access control policies based on Resource Access Management \(RAM\), such as the ReadOnlyAccess policy for granting the read-only permission and the FullAccess policy for granting the administrator permission.
-   Permission control based on RAM, such as permissions on instances, accounts, and GET, POST, and PUT methods.
-   Role-based access control \(RBAC\) based on the X-Pack plug-in. Data field-specific access control is supported.
-   Single sign-on \(SSO\) based on the X-Pack plug-in. Active Directory, LDAP, and Elasticsearch native Realm are supported for identity verification.

 |
|Data encryption| -   Use storage media that support static data encryption.
-   Disable HTTP in YML configuration.

 | -   HTTPS is supported.
-   Static data encryption based on Key Management Service \(KMS\).
-   The X-Pack plug-in is integrated to support data transmission encryption through SSL or TLS.

 |
|Monitoring and auditing|Use third-party tools to audit logs and monitor services.| -   Operation log auditing based on the X-Pack plug-in.
-   CloudMonitor-based cluster monitoring with multiple metrics, such as cluster workload.

 |
|Disaster recovery| -   Purchase file systems to back up data periodically.
-   Use multiple clusters to implement disaster recovery.

 | -   A cluster can be deployed across multiple zones in one city to implement disaster recovery.
-   Snapshots are automatically created at a scheduled time.

 |

