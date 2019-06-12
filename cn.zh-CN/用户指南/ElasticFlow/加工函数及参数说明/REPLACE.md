# REPLACE {#concept_b3h_swc_ghb .concept}

本文为您介绍如何使用字符串函数REPLACE。

## 功能描述 {#section_lfn_c1d_ghb .section}

字符串替换函数。

## 语法 {#section_f5x_c1d_ghb .section}

```

VARCHAR REPLACE(str1, str2, str3)


```

## 输入参数 {#section_fmf_d1d_ghb .section}

|参数|数据类型|说明|
|str1|VARCHAR|原字符|
|str2|VARCHAR|目标字符|
|str3|VARCHAR|替换字符|

## 示例 {#section_uwk_d1d_ghb .section}

**测试数据**

|str1\(INT\)|str2\(INT\)|str3\(INT\)|
|alibaba es|es|eflow|

**测试语句**

```
REPLACE(str1, str2, str3)
```

**测试结果**

|result\(VARCHAR\)|
|alibaba eflow|

