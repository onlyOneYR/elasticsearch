# REGEXP\_EXTRACT {#concept_w3c_b5c_ghb .concept}

本文为您介绍如何使用字符串函数REGEXP\_EXTRACT。

## 功能描述 {#section_lfn_c1d_ghb .section}

使用正则模式pattern匹配抽取字符串str中的第index个子串，index从1开始，正则匹配提取。参数为null或者正则不合法返回null。

## 语法 {#section_f5x_c1d_ghb .section}

```
VARCHAR REGEXP_EXTRACT(VARCHAR str, VARCHAR pattern, INT index)
```

## 输入参数 {#section_fmf_d1d_ghb .section}

|参数|数据类型|说明|
|str|VARCHAR|指定的字符串。|
|pattern|VARCHAR|匹配的字符串。|
|index|INT|第几个被匹配的字符串。|

**说明：** 正则常量请按照Java代码来写。codegen会将常量字符串自动转化成Java代码。如果要描述一个数字\(\\d\)，需要写成 '\\d'，也就是像在Java中写正则一样。

## 示例 {#section_uwk_d1d_ghb .section}

**测试数据**

|str1 \(VARCHAR\)|pattern1\(VARCHAR\)|index1 \(INT\)|
|foothebar|foo\(.\*?\)\(bar\)|2|
|100-200|\(\\d+\)-\(\\d+\)|1|
|null|foo\(.\*?\)\(bar\)|2|
|foothebar|null|2|
|foothebar|空|2|
|foothebar|\(|2|

**测试语句**

```

REGEXP_EXTRACT(str1, pattern1, index1)


```

**测试结果**

|result\(VARCHAR\)|
|bar|
|100|
|null|
|null|
|null|
|null|

