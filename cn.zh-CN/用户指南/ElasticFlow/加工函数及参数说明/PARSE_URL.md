# PARSE\_URL {#concept_a2z_ztc_ghb .concept}

本文为您介绍如何使用字符串函数PARSE\_URL。

## 功能描述 {#section_lfn_c1d_ghb .section}

解析url，获取partToExtract的值。如partToExtract='QUERY'，获取url参数key的值。partToExtract可取HOST、PATH、QUERY、REF、PROTOCOL、FILE、AUTHORITY、USERINFO。

**说明：** 参数为null，则返回null。

## 语法 {#section_f5x_c1d_ghb .section}

```

VARCHAR PARSE_URL(VARCHAR urlStr, VARCHAR partToExtract [, VARCHAR key])


```

## 输入参数 {#section_fmf_d1d_ghb .section}

|参数|数据类型|说明|
|urlStr|VARCHAR|url的字符串。|
|partToExtract|VARCHAR|解析后获取的值。|
|key|VARCHAR|参数名。|

## 示例 {#section_uwk_d1d_ghb .section}

**测试数据**

|url1\(VARCHAR\)|nullstr\(VARCHAR\)|
|http://facebook.com/path/p1.php?query=1|null|

**测试语句**

```

PARSE_URL(url1, 'QUERY', 'query') // var1,
PARSE_URL(url1, 'QUERY') // var2,
PARSE_URL(url1, 'HOST') // var3,
PARSE_URL(url1, 'PATH') // var4,
PARSE_URL(url1, 'REF') // var5,
PARSE_URL(url1, 'PROTOCOL') // var6,
PARSE_URL(url1, 'FILE') // var7,
PARSE_URL(url1, 'AUTHORITY') // var8,
PARSE_URL(nullstr, 'QUERY') // var9,
PARSE_URL(url1, 'USERINFO') // var10,
PARSE_URL(nullstr, 'QUERY', 'query') // var11
```

**测试结果**

|var1\(VARCHAR\)|var2\(VARCHAR\)|var3\(VARCHAR\)|var4\(VARCHAR\)|var5\(VARCHAR\)|var6\(VARCHAR\)|var7\(VARCHAR\)|var8\(VARCHAR\)|var9\(VARCHAR\)|var10\(VARCHAR\)|var11\(VARCHAR\)|
|1|query=1|facebook.com|/path/p1.php|null|http|/path/p1.php?query=1|facebook.com|null|null|null|

