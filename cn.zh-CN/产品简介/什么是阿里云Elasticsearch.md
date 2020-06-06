# 什么是阿里云Elasticsearch {#test .concept}

Elasticsearch简称ES，是一个基于Lucene的实时分布式的搜索与分析引擎，是遵从Apache开源条款的一款开源产品，是当前主流的企业级搜索引擎。它提供了一个分布式服务，可以使您快速的近乎于准实时的存储、查询和分析超大数据集，通常被用来当做构建复杂查询特性和需求强大应用的基础引擎或技术。

阿里云Elasticsearch提供Elasticsearch 5.5.3 with Commercial Feature、6.3.2 with Commercial Feature、6.7.0 with Commercial Feature及商业插件X-pack服务，致力于数据分析、数据搜索等场景服务。在开源Elasticsearch的基础上提供企业级权限管控、安全监控告警、自动报表生成等功能。

X-Pack是Elasticsearch的一个商业版扩展包，将安全，警告，监视，图形和报告功能捆绑在一个易于安装的软件包中。X-Pack被集成在Kibana中，为用户提供授权认证、角色权限管控、实时监控、可视化报表、机器学习等能力。

## 使用场景 {#section_ph2_h0k_nqt .section}

阿里云Elasticsearch可以被用在如下几个场景中：

-   当您运营一个提供客户检索商品的在线电子商城的时候，可以使用ES来存储整个商品的目录和库存，并且为客户提供检索和自动推荐功能。
-   收集交易数据，存储数据并做趋势、统计、概要或异常分析。这种情况下，可以使用Logstash来收集、聚合和解析数据，并且存储到Elasticsearch。一旦数据进入Elasticsearch，您可以通过检索、聚合来掌握您感兴趣的信息。
-   价格预警平台，为价格敏感客户提供匹配其需求（主要是价格方面）的商品。
-   在报表分析/BI领域，可以使用ES的聚合功能完成针对大数据量的复杂分析。

## 特点及优势 {#section_znf_rbf_zgb .section}

-   分布式的实时文件存储，每个字段都被索引并可被搜索。2
-   分布式的实时分析搜索引擎。
-   商业版X-pack插件，提供企业级权限管控、实时系统监控等强大服务。
-   可弹性扩展到上百台服务器规模，处理PB级结构化或非结构化数据。
-   支持IK analyzer插件。
-   Elastic官方技术支持团队7\*24小时技术支持。

## 预置插件 {#section_b15_sbf_zgb .section}

阿里云Elasticsearch预置插件如下（包含但不完全包含）：

-   IK Analyzer：IK Analyzer是一个开源的，基于java语言开发的中文分词工具包。是开源社区中处理中分分词非常热门的插件。
-   pinyin Analyzer：拼音分词器。
-   Smart Chinese Analysis Plugin：lucene默认的中文分词器。
-   ICU Analysis plugin：lucene自带的ICU分词，ICU是一套稳定、成熟、功能强大、轻便易用和跨平台支持Unicode 的开发包。
-   Mapper Attachments Type plugin：附件类型插件，通过tika库将各种类型格式解析成字符串。

