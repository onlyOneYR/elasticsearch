# Custom plug-ins {#concept_878701 .concept}

This topic describes how to upload, install, and remove custom plug-ins for Alibaba Cloud Elasticsearch.

## Upload and install a custom plug-in {#section_65u_wdt_haj .section}

**Note:** 

-   After you upload a custom plug-in, Elasticsearch needs to restart the cluster to install the plug-in. The custom plug-in may adversely affect the stability of the cluster. Make sure that the uploaded custom plug-in is secure and can run normally on the cluster.
-   When the Elasticsearch cluster is upgraded, it will not upgrade the custom plug-in at the same time. To upgrade the plug-in, you have to upload the new version of the plug-in to the cluster.
-   If your plug-in is not included in any privacy policies, we hope that you can make it open-source to help us develop our open-source community plug-ins.

1.  On the Plug-ins page, select **Custom Plug-ins** \> **Upload**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/711744/156266066050444_en-US.png)

2.  In the Upload Plug-in dialog box, click **Select files, or drag and drop files to this area**, and select the custom plug-in that you want to upload.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/711744/156266066150445_en-US.png)

    You can also drag and drop a custom plug-in file to this area to upload the plug-in. As shown in the preceding figure, the plug-in file **Elasticsearch-sql-6.7.0.0** has been added.

    **Note:** You can add multiple custom plug-ins in the same way.

3.  Read the agreement carefully, select the check box, and click **Upload**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/711744/156266066150446_en-US.png)

    After you upload the plug-in, Elasticsearch will restart the cluster to install the plug-in. After the cluster is restarted, you can check the plug-in in the **Custom Plug-ins** list. The **status** of the plug-in that you upload will display **Installed**. This indicates that the plug-in has been uploaded and installed successfully.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/711744/156266066150464_en-US.png)

    If you no longer need the plug-in, click **Remove** on the right side to remove the plug-in. For more information, see [Install or remove built-in plug-ins](reseller.en-US/User Guide/Instance management/Plug-ins/Built-in plug-ins.md#section_d0y_kyx_fu0).


