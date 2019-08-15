# Plug-in configuration {#concept_761720 .concept}

Alibaba Cloud Kibana provides multiple plug-ins based on open-source community plug-ins. This topic introduces Alibaba Cloud Kibana plug-ins and describes how to install and remove these plug-ins.

## Plug-ins {#section_uu7_gbl_1r1 .section}

[BSearch-QueryBuilder](reseller.en-US/User Guide/Data visualization/Kibana/Use BSearch-QueryBuilder.md#)

BSearch-QueryBuilder is an advanced query plug-in, as well as a UI component.

-   Easy to learn: the BSearch-QueryBuilder plug-in is a UI component, allowing you to create Elasticsearch DSL queries in a visualized manner. You can customize search conditions without coding. This saves the costs on learning complex DSL statements. It also helps developers write and verify DSL statements.
-   Easy to use: all queries that you have defined are saved in Kibana, which are ready for use at anytime.
-   Compact: BSearch-QueryBuilder only consumes about 14 MB of disk space. BSearch-QueryBuilder does not stay resident in the memory. This means that it will not adversely affect the performance of Kibana and Elasticsearch.
-   Secure and reliable: BSearch-QueryBuilder does not rewrite, store, or forward any user data. The source code of BSearch-QueryBuilder has been verified by Alibaba Cloud security auditing.

**Note:** BSearch-QueryBuilder currently only supports Alibaba Cloud Elasticsearch instances V6.3 and V6.7. Version 5.5.3 is not supported.

## Install a plug-in {#section_msj_jn2_373 .section}

**Note:** After you purchase an Alibaba Cloud Elasticsearch instance, Elasticsearch offers you a free Kibana node with one core and 2 GB of memory. A plug-in consumes resources. Before you install a plug-in, you must upgrade the Kibana node to **2-core, 4 GB** or higher. For more information, see [Cluster upgrade](reseller.en-US/User Guide/Instance management/Cluster upgrade.md#).

1.  Log on to the [Alibaba Cloud Elasticsearch console](https://elasticsearch.console.aliyun.com/), and [purchase an Elasticsearch instance](../../../../reseller.en-US/Quick Start/Activate Alibaba Cloud Elasticsearch.md#).
2.  Click**Instance ID/Name** \> **Data Visualization**.
3.  Click **Edit Configuration** under **Kibana**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156583848749321_en-US.png)

4.  On the **Kibana Configuration** page, click **Install** in the **Actions** column in the **Plug-in Configuration** list.

    **Note:** 

    -   After you confirm the install operation, the system will restart the Kibana node. During the restart process, Kibana cannot provide services normally. Therefore, before you confirm the operation, make sure that the restart process does not affect your operations on the Kibana console.
    -   If the specification of your Kibana node is lower than **2-core, 4 GB**, the system prompts a notification requiring you to upgrade the instance. Follow the instructions to upgrade the Kibana node to **2-core, 4 GB** or higher.
5.  Confirm the operation and restart the Kibana node.

    After the Kibana node is restarted, the installation process is then completed. The plug-in will be in the **Installed** state.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/216001/156583848749322_en-US.png)

    **Note:** The installation process may be time-consuming.


## Remove a plug-in {#section_vmy_azn_kfo .section}

1.  Follow the steps in [Install a plug-in](#section_msj_jn2_373) to go to the **Kibana Configuration** page, and then click **Remove** in the **Actions** column in the **Plug-in Configuration** list.

    **Note:** After you confirm the remove operation, the system will restart the Kibana node. During the restart process, Kibana cannot provide services normally. Therefore, before you confirm the operation, make sure that the restart process does not affect your operations on the Kibana console.

2.  Confirm the operation and restart the Kibana node.

    After the Kibana node is restarted, the remove process is then completed. The plug-in will be in the **Not Installed** state.


