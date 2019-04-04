# CONCAT {#concept_atz_xtc_ghb .concept}

本文为您介绍如何使用实时计算字符串函数CONCAT。

## 功能描述 {#section_lfn_c1d_ghb .section}

连接两个或多个字符串值从而组成一个新的字符串。当参数为NULL时，则跳过该参数。

## 语法 {#section_f5x_c1d_ghb .section}

```

VARCHAR CONCAT(VARCHAR var1, VARCHAR var2, ...)


```

## 输入参数 {#section_fmf_d1d_ghb .section}

|参数|数据类型|说明|
|var1|VARCHAR|普通字符串值|
|var2|VARCHAR|普通字符串值|

## 示例 {#section_uwk_d1d_ghb .section}

**测试数据**

|var1\(VARCHAR\)|var2\(VARCHAR\)|var3\(VARCHAR\)|
|Hello|My|World|
|Hello|null|World|
|null|null|World|
|null|null|null|

**测试语句**

```
CONCAT(var1, var2, var3)
```

**测试结果**

|var\(VARCHAR\)|
|HelloMyWorld|
|HelloWorld|
|World|
|N/A|

