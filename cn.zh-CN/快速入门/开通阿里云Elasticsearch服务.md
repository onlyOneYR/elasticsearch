# 开通阿里云Elasticsearch服务 {#concept_xd1_cmk_zgb .concept}

本文档为您介绍开通阿里云Elasticsearch服务的方法。

## 准备工作 {#section_mo9_17s_2kb .section}

在购买阿里云Elasticsearch前，请确保您已经完成了以下准备工作。

-   已经注册了阿里云账号。如果还未注册，请先完成[账号注册](https://account.aliyun.com/register/register.html)。
-   已经开通VPC网络和虚拟交换机服务。如果还未开通，请参考[创建专有网络和交换机](../../../../intl.zh-CN/用户指南/专有网络和子网/管理专有网络.md#section_ufw_rhv_rdb)进行开通。

## 操作步骤 {#section_nzc_6y3_pew .section}

1.  登录[阿里云官网](https://account.aliyun.com/)。
2.  将鼠标移至**产品分类** \> **大数据**，单击**Elasticsearch**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134282/156384584250484_zh-CN.png)

3.  单击**立即开通**，在购买页面根据提示完成服务的配置，然后单击**立即购买**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134282/156384584250486_zh-CN.png)

    配置参数详情请参见[购买页面参数](intl.zh-CN/快速入门/购买页面参数.md#)。

4.  在确认订单页面核对您实例的信息，无误后勾选协议，单击**去开通**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134282/156384584239933_zh-CN.png)

5.  提示开通成功后，单击**管理控制台**，进入阿里云Elasticsearch的应用控制台界面。
6.  进入阿里云Elasticsearch应用控制台界面后，正常情况下可以看到您已经购买的Elasticsearch实例，等待实例激活成功。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134282/156384584239935_zh-CN.png)

    当实例的**状态**显示为**正常**时，表示您的实例已经激活成功。此时，您就可以使用阿里云Elasticsearch完成[访问测试](intl.zh-CN/快速入门/ES访问测试.md#)、[功能配置](intl.zh-CN/快速入门/Elasticsearch功能配置.md#)、数据导入以及数据查询等相关操作了。

    **说明：** 

    在为以上刚激活的Elasticsearch实例导入数据之前，需要首先手动同时创建索引和mapping（不是同时创建可能会出现问题，例如存在先删除索引mapping再重新创建，并且期间存在数据推送的情况，有可能会因为Elasticsearch自动创建的mapping信息不符合预期而导致异常）。

    为避免此类问题，阿里云Elasticsearch默认关闭了**自动创建索引**功能。因此您需先同时创建索引和mapping，否则直接导入数据会报错。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134282/156384584339936_zh-CN.png)

    如果您强依赖于自动创建索引功能，可在阿里云Elasticsearch控制台中的[ES集群配置](../../../../intl.zh-CN/用户指南/实例管理/ES集群配置.md#)页面，修改**YML文件配置**开启自动创建索引功能，重启完成后生效。


