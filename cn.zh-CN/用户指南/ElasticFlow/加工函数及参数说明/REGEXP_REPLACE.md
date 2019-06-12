# REGEXP\_REPLACE {#concept_ads_b5c_ghb .concept}

本文为您介绍如何使用字符串函数REGEXP\_REPLACE。

## 功能描述 {#section_lfn_c1d_ghb .section}

用字符串replacement替换字符串str中正则模式为pattern的子串，返回新的字符串。正则匹配替换。

**说明：** 参数为null或者正则不合法返回null。

## 语法 {#section_f5x_c1d_ghb .section}

```

VARCHAR REGEXP_REPLACE(VARCHAR str, VARCHAR pattern, VARCHAR replacement)


```

## 输入参数 {#section_fmf_d1d_ghb .section}

|参数|数据类型|说明|
|str|VARCHAR|指定的字符串。|
|pattern|VARCHAR|被替换的字符串。|
|replacement|VARCHAR|用于替换的字符串。|

**说明：** 请您按照Java代码来写正则常量。codegen会自动将SQL常量字符串转化为Java代码。如果要描述一个字符串\(\\d\)，需要写成 '\\d'，同在Java中书写正则一样。

## 示例 {#section_uwk_d1d_ghb .section}

**测试数据**

|str1\(VARCHAR\)|pattern1\(VARCHAR\)|replace1\(VARCHAR\)|
|2014/3/13|-|空|
|null|-|空|
|2014/3/13|-|null|
|2014/3/13|空|s|
|2014/3/13|\(|s|
|100-200|\(\\d+\)|num|

**测试语句**

```

REGEXP_REPLACE(str1, pattern1, replace1)


```

**测试结果**

|result\(VARCHAR\)|
|20140313|
|null|
|null|
|2014/3/13|
|null|
|num-num|

