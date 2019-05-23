# Configure synonyms {#concept_ljj_dts_zgb .concept}

## Description {#section_o53_xdt_zgb .section}

**Note:** 

-   After you upload a synonym dictionary file to an Alibaba Cloud Elasticsearch instance, you do not need to restart the nodes in the instance. The system will update the synonym dictionary file to all nodes. Depending on the number of nodes, this process may be time-consuming.
-   For example, index ‘index-aliyun’ is using the synonym dictionary file ‘aliyun.txt’. You have uploaded a new synonym dictionary file to overwrite the existing dictionary file. However, index ‘index-aliyun’ cannot automatically load the updated dictionary file. If you want the index to load the updated dictionary file, disable the index and then re-enable the index. We recommend that you rebuild the index after you update the dictionary file as a best practice. Otherwise, this may cause an issue that only the newly created data is using the updated dictionary file.

You can use a filter to configure synonyms. The sample code is as follows:

```
PUT /test_index
{    
    "settings": {        
        "index" : {            
            "analysis" : {                
                "analyzer" : {                    
                    "synonym" : {                        
                        "tokenizer" : "whitespace",                       
                        "filter" : ["synonym"]                    
                        }               
                   },                
                   "filter" : {                    
                        "synonym" : {                       
                             "type" : "synonym",                        
                              "synonyms_path" : "analysis/synonym.txt",                                          
                              "tokenizer" : "whitespace"                    
                          }               
                       }            
                    }        
                  }    
          }
}
```

-   `filter`: configure a `synonym` token filter that contains the path `analysis/synonym.txt`. This path is relative to the location of **config**.
-   `tokenizer`: the tokenizer that tokenizes synonyms. It is set to `whitespace` by default. Additional settings:
    -   `ignore_case`: the default value is **false**.
    -   `expand`: the default value is **true**.

Two synonym formats are supported: Solr and WordNet.

-   **Solr synonyms** 

    The following is a sample format of the file:

    ```
    # Blank lines and lines starting with pound are comments.
    # Explicit mappings match any token sequence on the LHS of "=>"
    # and replace with all alternatives on the RHS.  These types of mappings
    # ignore the expand parameter in the schema.
    # Examples:
    i-pod, i pod => ipod,
    sea biscuit, sea biscit => seabiscuit
    # Equivalent synonyms may be separated with commas and give
    # no explicit mapping.  In this case the mapping behavior will
    # be taken from the expand parameter in the schema.  This allows
    # the same synonym file to be used in different synonym handling strategies.
    # Examples:
    ipod, i-pod, i pod
    foozball , foosball
    universe , cosmos
    lol, laughing out loud
    # If expand==true, "ipod, i-pod, i pod" is equivalent
    # to the explicit mapping:
    ipod, i-pod, i pod => ipod, i-pod, i pod
    # If expand==false, "ipod, i-pod, i pod" is equivalent
    # to the explicit mapping:
    ipod, i-pod, i pod => ipod
    # Multiple synonym mapping entries are merged.
    foo => foo bar
    foo => baz
    # is equivalent to
    foo => foo bar, baz
    ```

    You can also directly define synonyms for the token filter in the configuration file. You must use `synonyms` instead of `synonyms_path`. Example:

    ```
    PUT /test_index
    {
        "settings": {
            "index" : {
                "analysis" : {
                    "filter" : {
                        "synonym" : {
                            "type" : "synonym",
                            "synonyms" : [
                                "i-pod, i pod => ipod",
                                "begin, start"
                            ]
                        }
                    }
                }
            }
        }
    }
    ```

    We recommend that you use `synonyms_path` to define large synonym sets in the file. Using `synonyms` to define large synonym sets will increase the size of your cluster.

-   **WordNet synonyms** 

    Synonyms based on the WordNet format can be declared by using the following format:

    ```
    PUT /test_index
    {
        "settings": {
            "index" : {
                "analysis" : {
                    "filter" : {
                        "synonym" : {
                            "type" : "synonym",
                            "format" : "wordnet",
                            "synonyms" : [
                                "s(100000001,1,'abstain',v,1,0).",
                                "s(100000001,2,'refrain',v,1,0).",
                                "s(100000001,3,'desist',v,1,0)."
                            ]
                        }
                    }
                }
            }
        }
    }
    ```

    You can also use `synonyms_path` to define **WordNet synonyms** in a file.


## Example 1: {#section_vgx_xdt_zgb .section}

 **Upload a synonym dictionary file** 

1.  Log on to the [Alibaba Cloud Elasticsearch console](https://elasticsearch-cn-hangzhou.console.aliyun.com/).
2.  Click **Create** in the upper-left corner to create an Alibaba Cloud Elasticsearch instance.
3.  Click the instance to go to the configuration page.
4.  In the left-side navigation pane, select **Cluster Configuration**, and then click **Synonym Dictionary Configuration**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134296/155859695740187_en-US.png)

5.  Click **Upload**, select the synonym dictionary file that you want to upload, and click **Save**. In this example, the TXT file that is generated in the format described in the preceding sections is uploaded.

    After the Alibaba Cloud Elasticsearch instance is activated and its status changes to Active, you can then use the synonym dictionary. In this example, file `aliyun_synonyms.txt` is uploaded for testing. The file contains: `begin, start`


 **Configure and test the synonym dictionary** 

1.  Click **Kinana Console** in the upper-right corner to go to the Kibana console.
2.  In the left-side navigation pane, click **Dev Tool**.
3.  Run the following command in the **Console** to create indexes:

    ```
    PUT aliyun-index-test
    {
    "index": {
     "analysis": {
       "analyzer": {
         "by_smart": {
           "type": "custom",
           "tokenizer": "ik_smart",
           "filter": ["by_tfr","by_sfr"],
           "char_filter": ["by_cfr"]
         },
         "by_max_word": {
           "type": "custom",
           "tokenizer": "ik_max_word",
           "filter": ["by_tfr","by_sfr"],
           "char_filter": ["by_cfr"]
         }
       },
       "filter": {
         "by_tfr": {
           "type": "stop",
           "stopwords": [" "]
         },
         "by_sfr": {
           "type": "synonym",
           "synonyms_path": "analysis/aliyun_synonyms.txt"
         }
       },
       "char_filter": {
         "by_cfr": {
           "type": "mapping",
           "mappings": ["| => |"]
         }
       }
     }
    }
    }
    ```

4.  Run the following command to configure the title field:

    ```
    PUT aliyun-index-test/_mapping/doc
    {
    "properties": {
     "title": {
       "type": "text",
       "index": "analyzed",
       "analyzer": "by_max_word",
       "search_analyzer": "by_smart"
     }
    }
    }
    ```

5.  Run the following command to verify the synonyms:

    ```
    GET aliyun-index-test/_analyze
    {
    "analyzer": "by_smart",
    "text":"begin"
    }
    ```

    The following results are returned if the configuration takes effect:

    ```
    {
    "tokens": [
     {
       "token": "begin",
       "start_offset": 0,
       "end_offset": 5,
       "type": "ENGLISH",
       "position": 0
     },
     {
       "token": "start",
       "start_offset": 0,
       "end_offset": 5,
       "type": "SYNONYM",
       "position": 0
     }
    ]
    }
    ```

6.  Run the following command to add data for further testing:

    ```
    PUT aliyun-index-test/doc/1
    {
    "title": "Shall I begin?"
    }
    ```

    ```
    PUT aliyun-index-test/doc/2
    {
    "title": "I start work at nine."
    }
    ```

7.  Run the following command to perform a query test:

    ```
    GET aliyun-index-test/_search
    {
     "query" : { "match" : { "title" : "begin" }},
     "highlight" : {
         "pre_tags" : ["<red>", "<bule>"],
         "post_tags" : ["</red>", "</bule>"],
         "fields" : {
             "title" : {}
         }
     }
    }
    ```

    If the query is successful, the following results are returned:

    ```
    {
    "took": 11,
    "timed_out": false,
    "_shards": {
     "total": 5,
     "successful": 5,
     "failed": 0,
    },
    "hits": {
     "total": 2,
     "max_score": 0.41048482,
     "hits": [
       {
         "_index": "aliyun-index-test",
         "_type": "doc",
         "_id": "2",
         "_score": 0.41048482,
         "_source": {
           "title": "I start work at nine."
         },
         "highlight": {
           "title": [
             "I <red>start</red> work at nine."
           ]
         }
       },
       {
         "_index": "aliyun-index-test",
         "_type": "doc",
         "_id": "1",
         "_score": 0.39556286,
         "_source": {
           "title": "Shall I begin?"
         },
         "highlight": {
           "title": [
             "Shall I <red>begin</red>?"
           ]
         }
       }
     ]
    }
    }
    ```


## Example 2 {#section_ol2_ydt_zgb .section}

Follow these steps to directly import the synonyms and use the IK analyzer to filter the synonyms:

1.  Configure synonym filter `my_synonym_filter` and a synonym dictionary.
2.  Configure analyzer `my_synonyms`, and use IK analyzer `ik_smart` to split words.

    The IK analyzer `ik_smart` splits the words and then changes all letters to lowercase.

    ```
    PUT /my_index
    {
     "settings": {
         "analysis": {
             "analyzer": {
                 "my_synonyms": {
                     "filter": [
                         "lowercase",
                         "my_synonym_filter"
                     ],
                     "tokenizer": "ik_smart"
                 }
             },
             "filter": {
                 "my_synonym_filter": {
                     "synonyms": [
                         "begin,start"
                     ],
                     "type": "synonym"
                 }
             }
         }
     }
    }
    ```

3.  Run the following command to configure the title field:

    ```
    PUT /my_index/_mapping/doc
    {
    "properties": {
     "title": {
       "type": "text",
       "index": "analyzed",
       "analyzer": "my_synonyms"
     }
    }
    }
    ```

4.  Run the following command to verify the synonyms:

    ```
    GET /my_index/_analyze
    {
     "analyzer":"my_synonyms",
     "text":"Shall I begin?"
    }
    ```

    If the synonyms are verified, the following results are returned:

    ```
    {
    "tokens": [
     {
       "token": "shall",
       "start_offset": 0,
       "end_offset": 5,
       "type": "ENGLISH",
       "position": 0
     },
     {
       "token": "i",
       "start_offset": 6,
       "end_offset": 7,
       "type": "ENGLISH",
       "position": 1
     },
     {
       "token": "begin",
       "start_offset": 8,
       "end_offset": 13,
       "type": "ENGLISH",
       "position": 2
     },
     {
       "token": "start",
       "start_offset": 8,
       "end_offset": 13,
       "type": "SYNONYM",
       "position": 2
     }
    ]
    }
    ```

5.  Run the following command to add data for further testing:

    ```
    PUT /my_index/doc/1
    {
    "title": "Shall I begin?"
    }
    ```

    ```
    PUT /my_index/doc/2
    {
    "title": "I start work at nine."
    }
    ```

6.  Run the following command to perform a query test:

    ```
    GET /my_index/_search
    {
    "query" : { "match" : { "title" : "begin" }},
    "highlight" : {
      "pre_tags" : ["<red>", "<bule>"], 
      "post_tags" : ["</red>", "</bule>"], 
      "fields" : {
          "title" : {}
      }
    }
    }
    ```

7.  If the query is successful, the following results are returned:

    ```
    {
    "took": 11,
    "timed_out": false,
    "_shards": {
     "total": 5,
     "successful": 5,
     "failed": 0,
    },
    "hits": {
     "total": 2,
     "max_score": 0.41913947,
     "hits": [
       {
         "_index": "my_index",
         "_type": "doc",
         "_id": "2",
         "_score": 0.41913947,
         "_source": {
           "title": "I start work at nine."
         },
         "highlight": {
           "title": [
             "I <red>start</red> work at nine."
           ]
         }
       },
       {
         "_index": "my_index",
         "_type": "doc",
         "_id": "1",
         "_score": 0.39556286,
         "_source": {
           "title": "Shall I begin?"
         },
         "highlight": {
           "title": [
             "Shall I <red>begin</red>?"
           ]
         }
       }
     ]
    }
    }
    ```


