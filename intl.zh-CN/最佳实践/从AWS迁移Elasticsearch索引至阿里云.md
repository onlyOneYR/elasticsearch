# 从AWS迁移Elasticsearch索引至阿里云 {#concept_wjm_dcp_fhb .concept}

本文为您介绍如何将Elasticsearch（ES）索引从AWS迁移到阿里云。

本次ES索引迁移方案的参考架构图如下所示：

![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527686872268/01.png)

## ES索引迁移方案介绍 {#section_x4j_php_fhb .section}

 相关概念 

-   Elasticsearch：一个分布式的RESTful风格的搜索与分析引擎，能够解决不断涌现出的各种用例。作为Elastic Stack的核心，Elasticsearch可以集中存储您的数据，帮助您发现意料之中与意料之外的情况。
-   Kibana：您可以使用Kibana对您的Elasticsearch数据进行可视化并导航弹性堆栈。
-   亚马逊Elasticsearch服务（Amazon Elasticsearch Service）： 一项完全托管的服务，可以提供各种易于使用的Elasticsearch API 和实时分析功能，还可以实现生产工作负载需要的可用性、可扩展性和安全性。您可以使用AmazonElasticsearch Service轻松部署、保护、操作和扩展Elasticsearch，以便进行日志分析、全文搜索和应用程序监控等工作。
-   阿里云Elasticsearch服务（Alibaba Elasticsearch Service）： 该服务在国际站还未上线，本文涉及的操作主要在阿里云Elasticsearch服务中国站上进行。
-   快照和恢复（Snapshotand Restore）：您可以使用快照和恢复功能将各个索引或整个集群的快照创建到远程存储库（如共享文件系统，S3或HDFS）中。这些快照可以相对快速恢复，因此非常适合备份。但这些快照只能恢复到可以读取索引的Elasticsearch版本：

    -   在5.x中创建的索引快照可以恢复到6.x。
    -   在2.x中创建的索引快照可以恢复到5.x。
    -   在1.x中创建的索引快照可以恢复到2.x。
    **说明：** 

    在1.x中创建的索引快照不能恢复到5.x或6.x。在2.x中创建的索引快照也不能恢复到6.x。

    快照是增量的，可以包含在多个ES版本中创建的索引。如果一个快照中的索引是在不兼容的ES版本中创建的，则该快照不能恢复。


 解决方案概述 

您可以通过以下步骤来迁移Elasticsearch索引：

1.  创建基线索引
    1.  创建一个快照存储库，并将其与AWSS3存储空间相关联。
    2.  为要迁移的索引创建第一个完整的快照。

        该快照会自动存储在步骤一中创建的AWSS3存储空间中。

    3.  在阿里云侧创建一个OSS存储空间，并将其注册到阿里云ES实例的快照存储库中。
    4.  使用OSSImport工具AWS S3存储空间中的数据提取到阿里云OSS存储空间中。
    5.  将此完整快照恢复到阿里云ES实例。
2.  定期处理增量快照

    重复以上步骤处理增量快照并进行恢复。

3.  确定最终快照，进行服务切换。
    1.  停止可能会修改索引数据的服务。
    2.  创建AWS ES实例的最终增量快照。
    3.  将最终增量快照迁移并恢复到阿里云ES实例。
    4.  进行服务切换，查看阿里云ES实例。

## 前提条件 {#section_vsd_f3p_fhb .section}

-   Elasticsearch服务 
    -   AWS ES的版本号必须为5.5.2，区域为新加坡。
    -   阿里云ES的版本号必须为5.5.3，区域为杭州。
    -   示例的索引名称为movies。
-   在AWS中创建手动快照的前提条件 

    Amazon ES每天会自动创建一个域中主要索引分片的快照，并将这些快照存储在预配置的AmazonS3存储空间中。这些快照会保留14天，您无需额外付费。此外，您可以使用这些快照来恢复域。

    但是您不能使用自动快照迁移到新域。自动快照只能从指定的域中读取。如果要迁移，您必须使用存储在自己的存储库（S3存储空间）中的手动快照。手动快照将收取标准S3费用。

    您需要使用IAM和Amazon S3才能手动创建和恢复索引快照。创建快照之前，请确保您已满足以下条件。

    |前提条件|描述|
    |----|--|
    |创建S3存储空间|为您的 Amazon ES域存储手动快照。|
    |创建IAM角色|为Amazon Elasticsearch服务授权。在给该角色添加信任关系时，须在Principal语句中指定Amazon Elasticsearch 服务。使用Amazon ES注册您的快照存储库时也需要该IAM角色。只有具有该角色访问权限的IAM用户才可以注册快照存储库。|
    |创建IAM策略|指定Amazon S3可以对S3存储空间执行的操作。该策略须添加给为Amazon Elasticsearch 服务授权的IAM角色。您需要在该策略的Resource语句中指定S3存储空间。|

    -   创建S3存储空间 

        您需要一个S3存储空间来存储手动快照，并记下其Amazon资源名称（ARN）。该ARN在以下场景中会用到：

        -   为IAM角色添加的IAM策略中的Resource语句。
        -   用于注册快照存储库的Python客户端。
        S3存储空间的ARN示例：

        ``` {#codeblock_cok_x11_zxw}
        arn:aws:s3:::eric-es-index-backups
        ```

    -   创建IAM角色 

        确保您已创建一个IAM角色，且在其信任关系中的Service语句中指定该角色的服务类型为Amazon Elasticsearch服务（es.amazonaws.com），如下所示：

        ``` {#codeblock_xx0_q1a_3rm}
        {
          "Version": "2012-10-17",
          "Statement": [
            {
              "Sid": "",
              "Effect": "Allow",
              "Principal": {
                "Service": "es.amazonaws.com"
              },
              "Action": "sts:AssumeRole"
            }
          ]
        }
        ```

        您可以在AWS IAM控制台查看信任关系的详细信息。

        ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527691898319/02.png)

        ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527691909482/03.png)

        在IAM控制台创建AWS服务角色时，Amazon ES不包含在**Select role type**下拉列表中。 但是，您可以先选择**Amazon EC2**，按照提示完成角色创建，然后将ec2.amazonaws.com修改为 es.amazonaws.com。

    -   创建IAM策略 

        您需要将IAM策略添加给IAM角色。该策略指定用于存储Amazon ES域的手动快照的S3存储空间。以下示例指定了存储空间eric-es-index-backups的ARN：

        ``` {#codeblock_9dk_qc7_c5z}
        {
            "Version": "2012-10-17",
            "Statement": [
                {
                    "Action": [
                        "s3:ListBucket"
                    ],
                    "Effect": "Allow",
                    "Resource": [
                        "arn:aws:s3:::eric-es-index-backups"
                    ]
                },
                {
                    "Action": [
                        "s3:GetObject",
                        "s3:PutObject",
                        "s3:DeleteObject"
                    ],
                    "Effect": "Allow",
                    "Resource": [
                        "arn:aws:s3:::eric-es-index-backups/*"
                    ]
                }
            ]
        }
        ```

        将策略内容复制到编辑策略区域：

        ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527693813919/04.png)

        您可以通过查看**Policies** \> **Summary**来检查策略是否正确。![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527693868929/05.png)

    -   为IAM角色添加IAM策略

        ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527693915659/06.png)


## 注册手动快照存储库 {#section_cv3_djp_fhb .section}

您必须通过亚马逊Elasticsearch服务（Amazon Elasticsearch Service）注册快照存储库，然后才能拍摄手动索引快照。此一次性操作需要为IAM角色的信任关系中指定的用户或角色中的一位使用凭证签发您的AWS请求，如上文在AWS中创建手动快照的前提条件中所述。

不能使用curl执行此操作，因为它不支持AWS请求签名。请改用[示例Python客户端](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/72927/intl_en/1527694314531/Sample%20Python%20Client.docx)注册您的快照存储库。

1.  修改示例Python客户端 

    下载[示例Python客户端](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/72927/intl_en/1527694314531/Sample%20Python%20Client.docx)文件，然后修改文件中标黄的值，填入实际匹配的值。完成修改后，复制示例Python客户端文件中的内容至Python文件中，并命名为snapshot.py。

    示例Python客户端：

    |变量名|描述|
    |---|--|
    |地域|您创建快照存储库所在的AWS地域。|
    |主机|您Amazon ES域名的访问地址。|
    |aws\_access\_key\_id|IAM凭证|
    |aws\_secret\_access\_key|IAM凭证|
    |路径|快照存储库的名字|
    |data: bucket; region;role\_arn|必须包含您在上述在AWS中创建手动快照的前提条件中为IAM角色创建的S3存储空间的名字和ARN。要为快照存储库启用使用S3托管密钥的服务器端加密，请将 `"server_side_encryption": true`添加到settings JSON。 Important

 如果S3存储空间在地域us-east-1，您需要使用`"endpoint": "s3.amazonaws.com"` 替代`"region": "us-east-1"`。|

2.  安装Amazon Web Services Library boto-2.48.0 

    此示例Python客户端要求您在注册快照存储库的计算机上安装boto软件包的2.x版本。

    ``` {#codeblock_7ao_qwu_hk3}
    # wget https://pypi.python.org/packages/66/e7/fe1db6a5ed53831b53b8a6695a8f134a58833cadb5f2740802bc3730ac15/boto-2.48.0.tar.gz#md5=ce4589dd9c1d7f5d347363223ae1b970 
    # tar zxvf boto-2.48.0.tar.gz
    # cd boto-2.48.0
    # python setup.py install
    ```

3.  执行Python客户端以注册快照存储库 

    ``` {#codeblock_yw3_qjj_6ch}
    # pyth
    on snapshot.py
    ```

    注册快照存储库

    通过**Kibana** \> **Dev Tools**查看请求结果：

    ``` {#codeblock_v37_k77_406}
    GET _snapshot
    ```

    ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527695392939/07.png)


## 首次快照和恢复 {#section_ic4_3jp_fhb .section}

1.  在AWS ES上手动拍摄快照 

    以下命令均可通过**Kibana** \> **Dev Tools**执行，也可以从Linux或者Mac OSX命令行中使用curl来执行。

    -   仅为在eric-snapshot-repository存储库中的movies索引拍摄名为snapshot\_movies\_1的快照。

        ``` {#codeblock_18b_h2z_m2u}
        PUT _snapshot/eric-snapshot-repository/snapshot_movies_1
        {
        "indexes": "movies"
        }
        ```

    -   查看快照状态。

        ``` {#codeblock_40y_3w5_8fr}
        GET _snapshot/ eric-snapshot-repository/snapshot_movies_1
        ```

        ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527696490126/08.png)

    -   查看AWS S3控制台中的快照文件。

        ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527696560586/09.png)

2.  从AWS S3提取快照数据至阿里云OSS 

    此步操作，您需要从AWS S3存储空间提取快照数据至阿里云的对象存储OSS（Object Storage Service）中。具体详情，请参见[从AWS S3上的应用无缝切换至OSS](../../../../intl.zh-CN/最佳实践/数据迁移/从AWS S3上的应用无缝切换至OSS.md#)。

    数据提取后，在OSS控制台中查看存储的快照数据。

    ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1550653856005/%E5%90%88%E6%88%90.PNG)

3.  还原快照至阿里云ES实例 

    创建快照存储库

    在**Kibana** \> **Dev Tools**中执行以下请求来创建一个同名的快照存储库。按照参数说明填下实际数值。

    ``` {#codeblock_g88_r57_m9f}
    PUT _snapshot/eric-snapshot-repository
    {
    "type": "oss",
    "settings": {
                "endpoint": "http://oss-cn-hangzhou-internal.aliyuncs.com",  
                "access_key_id": "Put your AccessKey id here.",
                "secret_access_key": "Put your secret AccessKey here.",
                "bucket": "eric-oss-aws-es-snapshot-s3",
                "compress": true
          }
    }
    ```

     ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527696940099/011.png)

    创建好快照存储库后，查看在AWS ES手动快照步骤中部署名为snapshot\_movies\_1的快照状态。

    ``` {#codeblock_6tc_xlu_3l4}
    GET _snapshot/eric-snapshot-repository/snapshot_movies_1
    ```

    **说明：** 请记录此快照操作的起始时间和结束时间：当您用阿里云OssImport迁移工具迁移增量快照数据时，此记录会被用到。如：

    “start\_time\_in\_millis”: 1519786844591

    “end\_time\_in\_millis”: 1519786846236

    ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1550565009094/012.png)

4.  恢复快照 

    在 **Kibana** \> **Dev Tools**中执行以下请求。

    ``` {#codeblock_31e_efq_8w0}
    POST _snapshot/eric-snapshot-repository/snapshot_movies_1/_restore
    {
        "indexes": "movies"
    }
    GET movies/_recovery
    ```

    ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527698135558/013.png)

    在**Kibana** \> **Dev Tools**上查看movies索引的可用性。您可以看到在movies索引中存在三条记录，即为AWS ES实例中的记录数。![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527698191058/014.png)


## 末次快照和恢复 {#section_qkl_wjp_fhb .section}

1.  AWS ES movies索引中创建实例数据 

    上步中，在movies索引中已存在三条记录，您还需插入另两条记录。

    ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1550564871227/Image%203.png)

    使用请求 `GET movies/_count`，查看引数。

    ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527698440332/016.png)

2.  手动拍摄另一个快照 

    请参见上述的在AWS ES上手动拍摄快照步骤，并查看快照状态。

    ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527698440332/016.png)

    查看S3存储空间中列出的文件。

    ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527698555787/017.png)

    如果您查看索引文件夹，也可找出不同。

     从AWS S3提取增量快照数据至阿里云OSS 

    您可以使用OSSImport工具从AWS S3迁移数据至阿里云OSS。目前有两个快照文件存储在S3存储空间里，可以通过修改在配置文件local\_job.cfg中的isSkipExistFile值来迁移新的文件。

    |已归档|含义|描述|
    |---|--|--|
    |isSkipExistFile|布尔值，数据迁移期间是否跳过现有对象。| 如果设置为true，则根据size和LastModifiedTime跳过对象。

 如果设置为false，则覆盖现有对象。 默认值为false。 当jobType设置为audit时，此选项无效。

 |

    迁移工作完成后，您可以看到新的文件已被迁移至OSS中。

    阿里云的OSS存储空间：

    ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1550653926451/%E5%90%88%E6%88%9002.PNG)

    AWS S3的存储空间：

    ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527699274814/020.png)

3.  恢复增量快照 

    可以参照恢复快照中的步骤恢复增量快照。但是首先需要关闭movies索引，然后再恢复快照。快照恢复后可以再次打开movies索引。

    恢复快照步骤完成后，可以看到movies索引中文档数5和AWS ES实例中的数量相同。

    ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/72927/intl_en/1527699430579/021.png)


## 总结 {#section_y5x_jwo_e7c .section}

可以通过快照和恢复的方法将AWS Elasticsearch的服务数据迁移至阿里云的Elasticsearch服务中。

此方案要求首先关闭AWS ES实例以防止迁移期间的写入和请求。

参考资料：

-   [https://www.elastic.co/products/elasticsearch](https://www.elastic.co/products/elasticsearch)
-   [https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html)
-   [https://docs.aws.amazon.com/elasticsearch-service/latest/developerguide/es-managedomains-snapshots.html](https://docs.aws.amazon.com/elasticsearch-service/latest/developerguide/es-managedomains-snapshots.html)
-   [从AWS S3上的应用无缝切换至OSS](../../../../intl.zh-CN/最佳实践/数据迁移/从AWS S3上的应用无缝切换至OSS.md#)
-   [说明及配置](../../../../intl.zh-CN/常用工具/数据迁移工具ossimport/说明及配置.md#)

