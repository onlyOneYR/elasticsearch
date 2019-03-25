# Plug-in settings {#concept_jnh_hts_zgb .concept}

## Built-in plug-ins {#section_jmw_wpt_zgb .section}

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134301/155350205640206_en-US.png)

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134301/155350205640208_en-US.png)

## Upgrade IK analyzer dictionaries {#section_d3c_xpt_zgb .section}

Alibaba Cloud Elasticsearch allows you to use the following methods to update IK analyzer dictionaries:

-   Standard upgrade
-   Rolling upgrade

## Standard upgrade {#section_xrm_xpt_zgb .section}

The standard upgrade method updates the dictionaries on all nodes in an Elasticsearch cluster. Elasticsearch will propagate the uploaded dictionary file to all Elasticsearch nodes in the cluster, modify the `IKAnalyzer.cfg.xml` file on the nodes, and restart the Elasticsearch nodes to load the dictionary file.

You can use the standard upgrade method to modify the IK main dictionary and stopword list. The standard upgrade page already shows the built-in main dictionary `SYSTEM_MAIN.dic` and stopword list `SYSTEM_STOPWORD.dic`.

-   To modify the built-in main dictionary, upload the `SYSTEM_MAIN.dic` dictionary.
-   To modify the built-in stopword list, upload the `SYSTEM_STOPWORD.dic` dictionary.

## Examples {#section_s1s_xpt_zgb .section}

1.  Log on to the Elasticsearch console, click the ID of the target Elasticsearch instance, click **Plug-in Settings**, and click **Standard Upgrade** for the IK analyzer.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134301/155350205640216_en-US.png)

2.  Click **Configure**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134301/155350205640217_en-US.png)

3.  Click **Upload Dictionary**, and then upload a .dic main dictionary file.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134301/155350205640218_en-US.png)

    **Note:** 

    -   By default, you need to upload a dic file. You can also choose to import a dictionary file from OSS.
    -   To import a dictionary file from OSS, you need to modify the dictionary file on OSS and then import the file in the Elasticsearch console.
4.  Go to the bottom of the page, select **This operation requires a restart of the instance. Exercise with caution.**, and then click Save. The Elasticsearch cluster will restart all nodes.
5.  After the cluster completes restarting all nodes in the cluster, log on to the Kibana console to verify the dictionary as follows:

    ```
    GET _analyze
    {
    "analyzer": "ik_smart",
    "text": ["Words in your dictionary"]
    }
    ```

    **Note:** 

    -   You cannot delete the built-in IK main dictionary and stopword list.
    -   If you choose the standard upgrade method, both uploading a dictionary file and changing the dictionary content will restart all nodes in a cluster.
    -   You can perform the upgrade only when the Elasticsearch cluster is healthy.

## Rolling upgrade {#section_rry_xpt_zgb .section}

You can use the rolling upgrade method to update a dictionary when the content in the dictionary is changed. After you upload the new dictionary file, the Elasticsearch nodes will automatically load the new dictionary file.

If you use the rolling upgrade method to update the dictionary list, such as uploading a new dictionary file or deleting a dictionary file, the `IKAnalyzer.cfg.xml` file will be modified. Therefore, you must restart all nodes in the cluster to reload the changes.

The procedure of updating a dictionary by using the rolling upgrade method is the same as that of the standard upgrade method. If this is the first time you upload a dictionary file, you must modify the `IKAnalyzer.cfg.xml` file and then restart all nodes in the cluster.

## Examples {#section_awg_ypt_zgb .section}

1.  Log on to the Elasticsearch console, locate the target Elasticsearch instance, and click its ID. Click Plug-ins Settings, locate the IK plug-in, click Rolling Upgrade, and click Configure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134301/155350205640222_en-US.png)

2.  Click Upload Dictionary, and then select a main dictionary file.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134301/155350205740223_en-US.png)

    **Note:** 

    -   By default, you are required to upload a .dic file. You can also choose to import an OSS file.
    -   To import an OSS file, you must modify the file on OSS and then upload the file in the Elasticsearch console.
3.  Go to the bottom of the page, select **This operation requires a restart of the instance. Exercise with caution.**, and then click Save. The Elasticsearch cluster will restart all nodes. After the cluster has restarted all nodes, the uploaded dictionary takes effect.
4.  To add more content to the dictionary or delete the content in the dictionary, modify the `a_10words.dic` file. Go to the rolling upgrade page, delete the `a_10words.dic` file, and upload the modified file. This operation only updates the content in an existing dictionary. Therefore, you do not need to restart all nodes in the cluster.
5.  Go to the bottom of the page and then click **Save**. All nodes in the cluster will automatically load the modified dictionary file. It may take up to two minutes for all of the nodes to update the dictionary file. You must wait until all of the nodes finishing updating the dictionary file. To check whether the dictionary has been updated, call the following operation:

    ```
    GET _analyze
    {
    "analyzer": "ik_smart",
    "text": ["Words in your dictionary"]
    }
    ```

    **Note:** You cannot use the rolling upgrade method to modify the built-in dictionaries. To modify the built-in dictionaries, you must use the standard upgrade method.


