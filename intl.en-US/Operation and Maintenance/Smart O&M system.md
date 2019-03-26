# Smart O&M system {#concept_erj_1sl_zgb .concept}

## Overview {#section_kpl_ksl_zgb .section}

Alibaba Cloud Elasticsearch Smart O&M is a smart maintenance system based on Alibaba Cloud Elasticsearch. This feature is designed to check the health status of Elasticsearch instances, detect potential risks, and provide suggestions.

Smart O&M helps Alibaba Cloud Elasticsearch users resolve maintenance issues, reduce service usage and maintenance costs, keep their Elasticsearch instances safe and healthy, and simplify their work.

Smart O&M alpha testing is currently available in all domestic regions.

## Supported regions {#section_fwk_lsl_zgb .section}

The Smart O&M system supports the following regions:

-   China \(Hangzhou\)
-   China \(Shanghai\)
-   China \(Beijing\)
-   China \(Shenzhen\)

## Cluster overview {#section_szr_msl_zgb .section}

1.  Log in to the Elasticsearch console.
2.  Click the name of an Elasticsearch instance to go to the Elasticsearch instance information page.
3.  On the left-side navigation pane, click **Intelligent Maintenance** to view relevant tabs.

-   The first time you activate Intelligent Maintenance, you must grant Intelligent Maintenance access permissions to the basic cluster information and logs. However, Intelligent Maintenance does not have access to the cluster data.
-   After Intelligent Maintenance is activated, it will diagnose the cluster every early morning and generate diagnostic reports.
-   The cluster overview feature shows the health status of the cluster in the last seven days to help you learn the status of the cluster.

## Health diagnosis {#section_u5g_rsl_zgb .section}

In addition to periodical diagnosis every early morning, you can use **Diagnose Now** to manually diagnose your cluster. By default, you can use this feature to diagnose clusters up to five times per day. Each diagnostic process takes about three minutes.

After diagnosis is complete, you will receive a diagnostic report, allowing you to view the latest status of the cluster. By default, Intelligent Maintenance diagnoses all indexes on a cluster against all diagnostic items. You can choose to specify the diagnostic items and indexes. A maximum of 10 indexes can be selected each time.

## Reports {#section_plz_tsl_zgb .section}

The **reports** feature allows you to view the 20 most recent diagnostic reports.

## Diagnostic results {#section_ujt_5sl_zgb .section}

Intelligent Maintenance uses red, yellow, and green to indicate the health status of a cluster.

-   RED: Indicates a severe issue or risk that may affect the usage of the cluster and requires immediate attention. If you do not resolve this issue, data loss or cluster crash may occur.

-   YELLOW: Indicates a moderate issue that may affect the usage of the cluster and requires prompt attention.

-   GREEN: Indicates that the cluster is healthy.


