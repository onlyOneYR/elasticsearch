# TO\_TIMESTAMP {#concept_yvb_syd_ghb .concept}

本文为您介绍如何使用日期函数TO\_TIMESTAMP。

## 功能描述 {#section_yvh_bzd_ghb .section}

将VARCHAR类型的日期转换成TIMESTAMP类型。

## 语法 {#section_u54_bzd_ghb .section}

```

TIMESTAMP TO_TIMESTAMP(VARCHAR date)
TIMESTAMP TO_TIMESTAMP(VARCHAR date, VARCHAR format)


```

## 输入参数 {#section_bd5_bzd_ghb .section}

|参数|数据类型|说明|
|date|VARCHAR| |
|format|VARCHAR|默认格式为yyyy-MM-dd HH:mm:ss\[.SSS\]。|

## 示例 {#section_tng_czd_ghb .section}

**测试数据**

|timestamp1\(VARCHAR\)|timestamp2\(VARCHAR\)|
|2017/9/15 0:00|20170915000000|

**测试用例**

```

TO_TIMESTAMP(timestamp1) // result1
TO_TIMESTAMP(timestamp2, 'yyyyMMddHHmmss') // result2


```

**测试结果**

|result1\(TIMESTAMP\)|result2\(TIMESTAMP\)|
|2017-09-15 00:00:00.0|2017-09-15 00:00:00.0|

