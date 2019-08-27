# Built-in plug-ins {#concept_878700 .concept}

This topic describes the built-in plug-ins supported by Alibaba Cloud Elasticsearch, and introduces the standard update and rolling update methods for updating IK analyzer plug-ins.

The following figure shows the built-in plug-ins supported by Alibaba Cloud Elasticsearch:

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134301/156266063340208_en-US.png)

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134301/156266063340207_en-US.png)

After you purchase an Alibaba Cloud Elasticsearch instance, the system will automatically install the plug-ins in the **Built-in Plug-ins** list. You can also manually [install or remove](#section_d0y_kyx_fu0) these plug-ins as needed. The **analysis-ik** and **elasticsearch-repository-oss** plug-ins are extensions of Alibaba Cloud Elasticsearch. You cannot remove these plug-ins.

-   **analysis-ik**: an IK analyzer plug-in. Based on open-source plug-ins, this plug-in supports dynamically loading dictionary files stored on Object Storage Service \(OSS\). You can use the [standard update or rolling update](#section_qtc_drr_t28) method to update dictionary files.
-   **elasticsearch-repository-oss**: based on open-source plug-ins, this plug-in allows you to use OSS for storage when creating and restoring index snapshots.

## Install or remove built-in plug-ins {#section_d0y_kyx_fu0 .section}

**Note:** To install or remove a built-in plug-in, Elasticsearch must restart the cluster. If you remove a built-in plug-in, Elasticsearch will delete the plug-in. You must confirm the operation before you can proceed.

The following example shows how to remove the **analysis-kuromoji** plug-in.

1.  Click **Remove** in the **Actions** column on the right side of the **analysis-kuromoji** plug-in.
2.  Read the note in the **Confirm Operation** dialog box carefully and then click **OK**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/711743/156266063350441_en-US.png)

    After you confirm the operation, the cluster is restarted. After the cluster is restarted, the **status** of the **analysis-kuromoji** plug-in changes to **Not Installed**. This indicates that the plug-in has been removed.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/711743/156266063350461_en-US.png)

    If you want to use this plug-in again, follow these steps to re-install the plug-in.

3.  Click **Install** on the right side of the plug-in to install the plug-in.

    Elasticsearch will restart the cluster to install the plug-in. After the cluster is restarted, the **status** of the plug-in displays **Installed**. This indicates that the plug-in has been installed.


## Update IK dictionaries {#section_qtc_drr_t28 .section}

The IK analyzer plug-in of Alibaba Cloud Elasticsearch allows you to use the following methods to update IK dictionaries:

-   [Standard update](#)
-   [Rolling update](#)

**Note:** For indexes that are already configured with IK analyzers, the updated dictionary is only applied to new data in these indexes. If you want to apply the updated dictionary to both the existing data and new data, you must recreate these indexes.

## Standard update {#section_zuy_art_5ix .section}

The standard update method updates the dictionary on all nodes in an Elasticsearch cluster. If you choose the standard update method, Elasticsearch will send the uploaded dictionary file to all nodes in the cluster, modify the IKAnalyzer.cfg.xml file, and then restart the nodes to load the uploaded dictionary file.

You can use the standard update method to update the IK main dictionary and stopword list. On the standard update page, you can check the built-in main dictionary `SYSTEM_MAIN.dic` and stopword list `SYSTEM_STOPWORD.dic`.

**Note:** 

-   If you want to update the built-in main dictionary, upload a dictionary file named as **SYSTEM\_MAIN.dic**.
-   If you want to update the built-in stopword list, upload a dictionary file named as **SYSTEM\_STOPWORD.dic**.

## Standard update example {#section_m0p_yqv_obt .section}

1.  Log on to the [Alibaba Cloud Elasticsearch console](https://elasticsearc.console.aliyun.com/), and click the ID of the Elasticsearch instance that you want to update IK dictionaries for.
2.  In the left-side navigation pane, click **Plug-ins**, locate the plug-in that you want to update, click **Standard Update** in the **Actions** column.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134301/156266063440216_en-US.png)

3.  In the Plug-in Configuration dialog box, click **Configure**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134301/156266063440217_en-US.png)

4.  Click **Upload DIC File** under **IK Main Dictionary**, and upload a custom main dictionary file.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134301/156266063440218_en-US.png)

    **Note:** You can **upload a dic file** or **add an OSS file**. If the content of the dictionary file stored in the cloud or on your local host changes, you must use these methods to manually upload the dictionary file to update the dictionary.

5.  **Note:** This operation will restart the Elasticsearch instance. Make sure that your businesses are not affected before you confirm the operation.

    Scroll down to the bottom, select **This operation will restart the instance. Continue?** to confirm the operation, and click **Save**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134301/156266063449588_en-US.png)

6.  After the cluster is restarted, log on to the Kibana console, and then run the following command to verify the update is effective:

    ``` {#codeblock_cho_a7w_1ma}
    GET _analyze
    {
    "analyzer": "ik_smart",
    "text": ["tokens in your updated dictionary"]
    }
    ```

    **Note:** 

    -   You cannot delete the built-in main dictionary and stopword list.
    -   Whether you upload a new dictionary file, remove a dictionary file, or update the dictionary content, the standard update operation always requires Elasticsearch to restart the cluster.
    -   You can perform the standard update operation only when the status of the cluster is healthy.

## Rolling update {#section_4w3_rvx_pqm .section}

When the content of your dictionary file changes, you can use the rolling update method to update the dictionary. After you upload the latest dictionary file, the Elasticsearch nodes will automatically load the file.

When you perform a rolling update, if the dictionary file list changes, all nodes in the cluster need to reload the dictionary configuration. For example, when you upload a new dictionary file or delete an existing dictionary file, the changes will be updated to the IKAnalyzer.cfg.xml file.

The procedure of rolling update is similar to [standard update](#section_zuy_art_5ix). If this is the first time that you have uploaded a dictionary file, you must edit the IKAnalyzer.cfg.xml configuration file. This means that Elasticsearch needs to restart the cluster to reload the configuration file.

## Rolling update example {#section_7fw_0nz_jo2 .section}

1.  Log on to the [Alibaba Cloud Elasticsearch console](https://elasticsearc.console.aliyun.com/) and click the ID of the Elasticsearch instance that you want to update the dictionaries for.
2.  In the left-side navigation pane, click **Plug-ins**, locate the plug-in that you need to update, and click **Rolling Update** in the **Actions** column.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134301/156266063440222_en-US.png)

3.  In the Plug-in Configuration dialog box, click **Configure**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134301/156266063440217_en-US.png)

4.  Click **Upload DIC File** under **IK Main Dictionary**, and upload a custom main dictionary file.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134301/156266063440218_en-US.png)

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134301/156266063540223_en-US.png)

    **Note:** You can **upload a dic file** or **add an OSS file**. If the content of the dictionary file stored in the cloud or on your local host changes, you must use these methods to manually upload the dictionary file to update the dictionary.

5.  **Note:** This operation will restart the Elasticsearch instance. Make sure that your businesses are not affected before you confirm the operation.

    Scroll down to the bottom, select the **This operation will restart the instance. Continue?** check box to confirm the operation, and click **Save**. Elasticsearch needs to restart the cluster only if this is the first time that you have uploaded a dictionary file.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134301/156266063449588_en-US.png)

    After you click **Save**, the cluster will perform a rolling update. After the rolling update is complete, the updated dictionary takes effect.

    If you need to add tokens to or remove tokens from the updated dictionary, follow these steps to replace the a\_10words.dic dictionary file.

6.  In the rolling update dialog box, delete the existing dictionary file, and then upload a new dictionary file named as a\_10words.dic.

    This task changes the content of an existing dictionary file on the cluster. Therefore, Elasticsearch does not need to restart the cluster for the update to take effect, as shown in the following figure.

7.  Click **Save**.

    The plug-in on the nodes of the Elasticsearch cluster will automatically load the dictionary file. The time that each node takes to load the dictionary file varies. Please wait for the new dictionary to take effect. It may take about two minutes for all nodes to upload the dictionary file. You can log on to the Kibana console and run the following command to verify that the new dictionary is effective.

    ``` {#codeblock_sar_vqr_40q}
    GET _analyze
    {
    "analyzer": "ik_smart",
    "text": ["tokens in your updated dictionary"]
    }
    ```

    **Note:** You can not use the rolling update method to edit the built-in main dictionary. If you want to modify the built-in main dictionary, use the standard update method.


For more information, see [elasticsearch-analysis-ik](https://github.com/medcl/elasticsearch-analysis-ik/tree/v6.3.2/config).

