# 什么是阿里云Logstash Service {#concept_2261363 .concept}

阿里云Logstash Service作为服务器端的数据处理管道，提供了100%兼容开源的Logstash功能。Logstash能够动态地从多个来源采集数据、转换数据，并且将数据存储到所选择的位置。通过输入、过滤和输出插件，Logstash可以对任何类型的事件加工和转换。

## 为什么选择阿里云Logstash Service {#section_wpa_e6c_458 .section}

阿里云Logstash Service除了支持所有官方预置插件外，还致力于打造包含Logstash-input-logservice、Logstash-oss-input/output等适用各类场景插件的插件中心，为您提供更为强大的数据处理和搬迁能力，实现云上数据生态打通。

在阿里云ELK（Elasicsearch、Logstash、Kibana）生态下，Elasticsearch作为实时分布式搜索和分析引擎，Kibana为Elasticsearch提供了强大的可视化界面，Logstash提供了数据采集、转换、优化和输出的能力，可以被广泛应用于实时日志处理、全文搜索和数据分析等领域。

## Logstash数据传输原理 {#section_eev_ma2_hy8 .section}

-   数据采集与输入：Logstash支持各种输入选择，能够以连续的流式传输方式，轻松地从日志、指标、Web应用以及数据存储中采集数据。
-   实时解析和数据转换：通过Logstash过滤器解析各个事件，识别已命名的字段来构建结构，并将它们转换成通用格式，最终将数据从源端传输到存储库中。
-   存储与数据导出：Logstash提供多种输出选择，可以将数据发送到指定的地方，且能够灵活地解锁众多下游用例。

## 特点与优势 {#section_77c_dsi_eyb .section}

-   快速部署、轻松管理、简化复杂的运维操作。
-   集成官方全部Input/Output/Filter插件。
-   支持Log Service、OSS等阿里云产品输入/输出插件。
-   开放灵活的插件中心。
-   关联Elasticsearch实例进行集中式管道管理。

