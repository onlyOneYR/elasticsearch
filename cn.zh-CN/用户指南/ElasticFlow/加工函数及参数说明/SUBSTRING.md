# SUBSTRING {#concept_owt_twc_ghb .concept}

本文为您介绍如何使用字符串函数SUBSTRING。

## 功能描述 {#section_lfn_c1d_ghb .section}

获取字符串子串。截取从位置start开始，长度为len的子串。若未指定len则截取到字符串结尾。start从1开始，start为0当1看待，为负数时表示从字符串末尾倒序计算位置。

## 语法 {#section_f5x_c1d_ghb .section}

```

VARCHAR SUBSTRING(VARCHAR a, INT start)
VARCHAR SUBSTRING(VARCHAR a, INT start, INT len)


```

## 输入参数 {#section_fmf_d1d_ghb .section}

|参数|数据类型|说明|
|a|VARCHAR|指定的字符串。|
|start|INT|在字符串a中开始截取的位置。|
|len|INT|类截取的长度。|

## 示例 {#section_uwk_d1d_ghb .section}

**测试数据**

|str\(VARCHAR\)|nullstr\(VARCHAR\)|
|k1=v1;k2=v2|null|

**测试语句**

```
sql SUBSTRING(’’, 222222222) // var1, SUBSTRING(str, 2) // var2, SUBSTRING(str, -2) // var3, SUBSTRING(str, -2, 1) // var4, SUBSTRING(str, 2, 1) // var5, SUBSTRING(str, 22) // var6, SUBSTRING(str, -22) // var7, SUBSTRING(str, 1) // var8, SUBSTRING(str, 0) // var9, SUBSTRING(nullstr, 0) // var10
```

**测试结果**

|var1\(VARCHAR\)|var2\(VARCHAR\)|var3\(VARCHAR\)|var4\(VARCHAR\)|var5\(VARCHAR\)|var6\(VARCHAR\)|var7\(VARCHAR\)|var8\(VARCHAR\)|var9\(VARCHAR\)|var10\(VARCHAR\)|
|空|1=v1;k2=v2|v2|v|1|空|空|k1=v1;k2=v2|k1=v1;k2=v2|null|

