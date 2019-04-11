# Query logs {#concept_swf_cts_zgb .concept}

Alibaba Cloud Elasticsearch allows you to search and view multiple types of logs, including the **Elasticsearch instance log**, **search slow log**, **indexing slow log**, and **GC log**.

You can search for specific log entries by entering keywords and setting a time range. All Alibaba Cloud Elasticsearch log entries are sorted in time descending order. You can search for log entries that are stored within the last seven days.

Alibaba Cloud Elasticsearch allows you to use Lucene to query logs. For more information, see [Query String Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/query-dsl-query-string-query.html#query-string-syntax).

**Note:** Due to the restrictions Elasticsearch puts on query conditions, a maximum of 10,000 log entries can be returned. If the log entries that you have queried are not contained in the returned 10,000 log entries, set a more specific time range to narrow down the search results.

## Example {#section_mby_bct_zgb .section}

The following example shows how to search for Elasticsearch instance logs whose content contains the keyword health, level is set to info, and host is set to 192.168.1.123.

1.  Log on to the Alibaba Cloud Elasticsearch console, select the target instance, and click Manage in the Actions column to go to the Basic Information page. On the Basic Information page, click **Logs** in the left-side navigation pane and then click the **Instance Log** tab.
2.  Enter `host:192.168.1.123 AND content:health AND level:info` in the search box.
3.  Specify a time range and click Search.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134294/155497161440170_en-US.png)


**Note:** 

-   If you do not specify the end time, it defaults to the current system time.
-   If you do not specify the start time, it defaults to one hour later than the end time.
-   The word `AND` connecting search conditions that you enter in the search box must be capitalized.

## Log description {#section_lqd_cct_zgb .section}

You can view log entries that are retrieved based on specified search conditions on the log search page. Each log entry contains the following parts: **Time**, **Node IP**, and **Content**.

**Time**

The time when the log entry was created.

**Node IP**

The IP address of the Alibaba Cloud Elasticsearch node.

**Content**

The information about the level, host, time, and content.

-   level: the level of the log entry. Log levels include trace, debug, info, warn, and error. GC log entries do not have levels.
-   host: indicates the IP address of the Elasticsearch node. You can view the IP address on the Nodes tab in the Kibana console.
-   time: indicates the time when the log entry was created.
-   content: displays major information about the log entry.

