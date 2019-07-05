# Instance management {#concept_uk5_pzl_zgb .concept}

This topic describes the instance management feature of Alibaba Cloud Elasticsearch, including cluster monitoring, instance restart, refresh, and task list.

## Manage instances {#section_hpw_bcm_zgb .section}

Alibaba Cloud Elasticsearch supports the [Cluster monitoring](#section_f53_ccm_zgb), [Restart instances](#section_p5n_ccm_zgb), [Refresh](#section_iss_ccm_zgb), and [Tasks](#section_xfd_gcm_zgb) features for you to manage instances.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134288/156231067339990_en-US.png)

## Cluster monitoring {#section_f53_ccm_zgb .section}

Alibaba Cloud Elasticsearch supports cluster monitoring and sending alerts to users through SMS messages. You can customize the threshold for triggering alerts. For more information, see [CloudMonitor alerts for Elasticsearch](../../../../reseller.en-US/Monitoring Alarms/ES CloudMonitor alarm.md).

## Restart instances {#section_p5n_ccm_zgb .section}

Alibaba Cloud Elasticsearch allows you to use the **restart** and **force restart** methods to restart instances. Follow these guidelines to select an appropriate restart method:

-    Prerequisites: Before you restart an instance, make sure that the **status** of the Elasticsearch instance is **Active** \(green flag\), the instance has at least one index replica, and the resource usage is not high. You can go to the [Cluster monitoring](reseller.en-US/User Guide/Instance management/Cluster monitoring.md#) page to check the resource usage. Ensure that the **Node CPU Usage \(%\)** is 80% or lower, the **Node Heep Memory Usage \(%\)** is around 50%, and the **Node Workload Within One Minute** does not exceed the number of cores of the current data node.

     **Restart**: If the Elasticsearch instance is restarted by this method, it can continuously provide services during the restart process. However, the instance must meet the requirements in [Prerequisites](#). The restart process is time-consuming.

    **Note:** 

    -   Before you restart the instance, make sure that the **status** of the instance is **Active** \(green flag\). Otherwise, you have to use the [force restart](#) method to restart the instance.
    -   The CPU and memory usage of the Elasticsearch instance will experience a usage spike during the restart process. This may affect the stability of your service for a short period of time.
    -   The time that the restart process takes depends on the amount of data stored on the instance, the number of nodes, and the number of indexes and replicas. Elasticsearch cannot estimate the total amount of time required to restart an instance. However, you can check the progress of the restart process in [Tasks](#section_xfd_gcm_zgb).
-    **Force restart**: If an Elasticsearch instance is restarted by this method, the services running on the instance may become unstable during the restart process. The restart process takes only a short period of time.

    **Note:** When the disk usage exceeds 85%, the **status** of the Elasticsearch instance may change to a yellow or red flag. If a yellow or red flag is displayed, you cannot use the **restart** method to restart the instance. You can only **forcibly restart** the instance.

    -   When a yellow or red flag is displayed, we recommend that you do not perform these operations on the instance: **upgrade nodes**, **upgrade disk space**, **restart**, **reset password**, and other operations that may change the configuration of the instance. Perform these operations only after the **status** of the instance changes to a **green flag**.
    -   If you update the configuration of an Elasticsearch instance with a yellow or red flag and the instance contains two or more nodes, the instance will be constantly in the **Initializing** state. You can [submit a ticket](https://workorder.console.aliyun.com/) to contact the Alibaba Cloud Elasticsearch Technical Support to resolve this issue.

## Refresh {#section_iss_ccm_zgb .section}

You can use this feature to manually refresh the information displayed in the console. For example, if the console fails to display the status of the Elasticsearch instance that you have just created, use the refresh feature to update the status.

## Tasks {#section_xfd_gcm_zgb .section}

You can click the Tasks icon to view the progress of tasks, such as the instance creation or restart progress.

-   No task is running on the current instance.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134288/156231067339992_en-US.png)

-   Tasks that are running on the current instance.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134288/156231067339993_en-US.png)

-   Show detailed information about a running task.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134288/156231067439995_en-US.png)


