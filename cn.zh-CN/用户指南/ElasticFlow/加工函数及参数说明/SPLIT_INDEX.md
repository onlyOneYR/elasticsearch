# SPLIT\_INDEX {#concept_wmb_twc_ghb .concept}

本文为您介绍如何使用字符串函数SPLIT\_INDEX。

## 功能描述 {#section_lfn_c1d_ghb .section}

以sep作为分隔符，将字符串str分隔成若干段，取其中的第index段。index从0开始，取不到字段，则返回null。

任一参数为NULL，则返回null。

## 语法 {#section_f5x_c1d_ghb .section}

```

VARCHAR SPLIT_INDEX(VARCHAR str, VARCHAR sep, INT index)


```

## 输入参数 {#section_fmf_d1d_ghb .section}

|参数|数据类型|说明|
|str|VARCHAR|被分隔的字符串。|
|sep|VARCHAR|分隔符的字符串。|
|index|INT|截取的字段位置。|

## 示例 {#section_uwk_d1d_ghb .section}

**测试数据**

|str\(VARCHAR\)|sep\(VARCHAR\)|index\(INT\)|
|Jack,John,Mary|,|2|
|Jack,John,Mary|,|3|
|Jack,John,Mary|null|0|
|null|,|0|

**测试语句**

```

SPLIT_INDEX(str, sep, index)


```

**测试结果**

|Value\(VARCHAR\)|
|Mary|
|null|
|null|
|null|

