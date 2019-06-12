# Kibana console password {#concept_aw5_wql_zgb .concept}

## Kibana console password {#section_c2p_hrl_zgb .section}

What is the password of Alibaba Cloud Elasticsearch Kibana console used for?

-   The `elastic` account is the root account that gives you access to the Alibaba Cloud Elasticsearch service. This account has the maximum permissions of cluster management, so keep the root account information safe.
-   If you use an API or SDK to access the Alibaba Cloud Elasticsearch instance, verify your privilege using `elastic/your_password`. If no password is set, initialize the password in the console.
-   If you use the Kibana service to access the Alibaba Cloud Elasticsearch instance, verify your privilege using `elastic/your_password`. If no password is set, initialize the password in the console.

## Permission management in Kibana {#section_ohh_krl_zgb .section}

How to effectively manage user permissions in Elasticsearch Kibana?

-   We recommend that you create users and assign roles to them using the Kibana service of your Elasticsearch instance, instead of using the `root` account directly for instance operations. To create a user, see [Kibana User Guide](https://www.elastic.co/guide/en/kibana/current/xpack-security.html).
-   We recommend that you do not use the root account `elastic` for the search service, because exposure of its password may bring security risks to your service cluster.
-   Exercise caution when changing the password of the root account `elastic` if this account is used to provide the search service. After the password of the Alibaba Cloud Elasticsearch instance is reset, the search service becomes unavailable due to authorization failure.

