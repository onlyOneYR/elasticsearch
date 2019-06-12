# Kibana控制台密码 {#concept_aw5_wql_zgb .concept}

## Kibana控制台密码 {#section_c2p_hrl_zgb .section}

阿里云Elasticsearch Kibana控制台密码有什么作用？

-   `elastic` 账号是您的阿里云 Elasticsearch 搜索服务的根账号，拥有集群管理最高权限，请妥善保管。
-   用户使用 API 及 SDK 访问实例，需要使用 `elastic/your_password` 来做权限校验。如果未设定密码，请前往控制台初始化密码。
-   用户使用 kibana服务访问实例，需要使用 `elastic/your_password` 来做权限校验。如果未设定密码，请前往控制台初始化密码。

## Kibana管理权限 {#section_ohh_krl_zgb .section}

如何在Elasticsearch Kibana更好的管理权限？

-   推荐用户在实例 kibana服务中创建新用户并授权角色，避免直接使用 `root` 权限的账号操作实例。参照 [kibana](https://www.elastic.co/guide/en/kibana/current/xpack-security.html) 官方文档创建用户
-   不建议用户使用 root 账号：`elastic` 用于搜索业务使用，因为 `elastic` 密码泄露后，可能导致您服务集群有安全风险。
-   请谨慎变更 root 账号：`elastic` 的密码，如果在业务中使用 `elastic` 账号提供服务。在阿里云Elasticsearch实例重置密码后，将会因为请求鉴权失败导致业务出现不可用状态。

