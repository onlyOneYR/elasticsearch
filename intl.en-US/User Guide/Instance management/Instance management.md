# Instance management {#concept_uk5_pzl_zgb .concept}

## Elasticsearch instance management {#section_hpw_bcm_zgb .section}

Alibaba Cloud Elasticsearch supports multiple features for instance management, including Kibana console, instance monitoring, instance restart, refresh, and task list.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134288/155324148939990_en-US.png)

## Kibana console {#section_pjd_ccm_zgb .section}

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134288/155324148939991_en-US.png)

Elasticsearch provides the Kibana console for business scaling.

The Kibana console is a part of the Elasticsearch ecosystem, which has been seamlessly integrated into Elasticsearch. The Kibana console enables you to view the running status of your Elasticsearch instances and manage these instances.

## Instance monitoring {#section_f53_ccm_zgb .section}

Elasticsearch supports instance monitoring. You can customize alert thresholds and enable Elasticsearch to use SMS alerts when any exceptions have been detected. For more information, see [ES CloudMonitor alarm](../../../../../intl.en-US/Cloud Monitor/ES CloudMonitor alarm.md).

## Instance restart {#section_p5n_ccm_zgb .section}

This feature allows you to use the restart and force-restart method to restart an Elasticsearch instance. Select a restart method based on your business scenario.

**Restart the agent**

This method ensures **service continuity** by keeping at least one replica running on the Elasticsearch instance during the restart process. However, a restart using this method takes a long period of time.

**Note:** 

-   Make sure that the health status of your Elasticsearch instance is green.
-   The CPU and memory usage of the Elasticsearch instance will experience a usage spike during the restart process. This may affect the stability of your service for a short period of time.

**Force-restart**

This method may cause service instability on the Elasticsearch instance during the restart process. However, this method takes less time.

**Note:** 

When an Elasticsearch instance has a high disk usage, such as 85% or higher, the health status of the instance may change to **yellow** or **red**. You cannot **restart** an instance in red or yellow health status. To restart the instance, you must use the **force-restart** method.

-   We recommend that you do not perform instance operations including **node scaling**, **disk scaling**, **restart**, **password modification**, and **configuration modification** when the health status of your Elasticsearch instance is yellow or red. Perform these operations when the health status of your instance is **green**.
-   If you change the configuration of an unhealthy instance that contains two or more **nodes**, the instance will remain in the **Applying** status. To resolve this issue, submit a ticket.
-   If you perform the **update**, **restart**, **scaling**, or **password reset** operation on an Elasticsearch instance that contains only one node, the service on the instance will become unavailable during the execution of the operation. To resolve this issue, create an Elasticsearch instance and migrate your service to the newly created instance.

## Refresh {#section_iss_ccm_zgb .section}

In certain cases, the console may fail to update the information. For example, the console may fail to update the status of an Alibaba Cloud Elasticsearch instance after the instance has been successfully created. To resolve this issue, use the refresh function to manually refresh the status of the instance.

## Task list {#section_xfd_gcm_zgb .section}

The Task list page shows running tasks on the current instance.

-   No task is running on the current instance.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134288/155324148939992_en-US.png)

-   Tasks running on the current instance.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134288/155324148939993_en-US.png)

-   Show detailed information about a running task.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134288/155324148939995_en-US.png)


