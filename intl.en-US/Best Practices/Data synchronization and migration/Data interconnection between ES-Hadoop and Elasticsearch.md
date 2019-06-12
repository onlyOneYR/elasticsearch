# Data interconnection between ES-Hadoop and Elasticsearch {#concept_knr_4ns_zgb .concept}

You can directly write data to Alibaba Cloud Elasticsearch through ES-Hadoop based on Alibaba Cloud Elasticsearch and E-MapReduce.

## Versions {#section_z2y_wxs_zgb .section}

Elasticsearch 5.5.3 with X-Pack is supported.

**Note:** Elasticsearch 6.3.2 with X-Pack is not supported.

## Activate Alibaba Cloud Elasticsearch {#section_wvk_yxs_zgb .section}

This example uses the following Alibaba Cloud services:

-   VPC: Transmitting data in a public network is not secure. To ensure a secure connection to your Alibaba Cloud Elasticsearch instances, you must deploy a VPC and a VSwitch in the specified region. Therefore, you must activate VPC.
-   OSS: In this example, OSS is used to store the E-MapReduce log. You must activate OSS and create a bucket before you activate E-MapReduce.
-   Elasticsearch
-   E-MapReduce

Follow these steps to activate the corresponding Alibaba Cloud services:

1.  Activate Alibaba Cloud VPC
    1.  On the Alibaba Cloud website, choose **Products** \> **Networking** \> **Virtual Private Cloud**, and then click **Activate Now**.
    2.  Log on to the VPC console, and click **Create VPC** to create a VPC.
    3.  You can manage the VPC that you have created in the console.

        **Note:** For more information about Alibaba Cloud VPC, see [Virtual Private Cloud \(VPC\)](https://www.alibabacloud.com/help/product/27706.htm) .

2.  Activate Alibaba Cloud Object Storage Service
    1.  Log on to the Alibaba Cloud console, choose **Products** \> **Storage & CDN** \> **Object Storage Service**, and click **Buy Now**.
    2.  Log on to the OSS console, click **Create Bucket** to create a bucket.

        **Note:** You must create the bucket in the same region where the E-MapReduce cluster is created. This example chooses the China \(Hangzhou\) region.

    3.  Create a bucket according to the instructions displayed on the page.
3.  Activate Alibaba Cloud Elasticsearch
    1.  On the Alibaba Cloud website, choose **Products** \> **Analytics & Big Data** \> **Elasticsearch**, and then the product page is displayed.

        **Note:** You can get a 30-day free trial.

    2.  After you have successfully activated Elasticsearch, you can view the newly created Elasticsearch instances in the Elasticsearch console.
4.  Activate Alibaba Cloud E-MapReduce
    1.  On the Alibaba Cloud website, choose **Products** \> **Analytics & Big Data** \> **E-MapReduce**, and then the product page is displayed.
    2.  Click **Buy Now**, and complete the configuration.
    3.  You can view the E-MapReduce clusters that you have created in the cluster list, and perform the following operations to verify the creation status.
        -   You can remotely log on to the clusters through a public IP address:

            ```
            ssh root@your public IP address
            ```

        -   Run the jps command to view background processes:

            ```
            [root@emr-header-1 ~]# jps
            16640 Bootstrap
            17988 RunJar
            19140 HistoryServer
            18981 WebAppProxyServer
            14023 Jps
            15949 gateway.jar
            16621 ZeppelinServer
            1133 EmrAgent
            15119 RunJar
            17519 ResourceManager
            1871 Application
            19316 JobHistoryServer
            1077 WatchDog
            17237 SecondaryNameNode
            16502 NameNode
            16988 ApacheDsTanukiWrapper
            18429 ApplicationHistoryServer
            ```


## Create an MR job that writes data to Elasticsearch from E-MapReduce {#section_rnt_xzs_zgb .section}

We recommend that you use Maven to manage projects. To use Maven, follow these steps:

1.  Install Maven.

    Make sure that your computer has [Maven](http://maven.apache.org/index.html?spm=a2c4g.11186623.2.15.142a7c17L4OIWa) installed.

2.  Generate an engineering framework.

    Run the following command in the root directory of the project:

    ```
    mvn archetype:generate -DgroupId=com.aliyun.emrtoes -DartifactId=emrtoes -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    Maven will automatically generate an empty sample project named emrtoes, which is the same as the specified artifactId. The project contains a pom.xml file and an application class. The path of the class package is the same as the specified groupId.

3.  Add Hadoop and ES-Hadoop dependencies.

    Start this project with any IED, then edit the pom.xml file. Add the following content to dependencies:

    ```
    <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-client-common</artifactId>
          <version>2.7.3</version>
      </dependency>
      <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-common</artifactId>
          <version>2.0.3</version>
      </dependency>
       <dependency>
           <groupId>org.elasticsearch</groupId>
           <artifactId>elasticsearch-hadoop-mr</artifactId>
           <version>2.5.0</version>
       </dependency>
    ```

4.  Add the packaging plugin.

    Since a third-party database is used, you must package this database into a JAR package. Add the following maven-assembly-plugin coordinates to the pom.xml file:

    ```
    <plugins>
       <plugin>
         <artifactId>maven-assembly-plugin</artifactId>
         <configuration>
           <archive>
             <manifest>
               <mainClass>com.aliyun.emrtoes.EmrToES</mainClass>
             </manifest>
           </archive>
           <descriptorRefs>
             <descriptorRef>jar-with-dependencies</descriptorRef>
           </descriptorRefs>
         </configuration>
         <executions>
           <execution>
             <id>make-assembly</id>
             <phase>package</phase>
             <goals>
               <goal>single</goal>
             </goals>
           </execution>
         </executions>
       </plugin>
       <plugin>
         <groupId>org.apache.maven.plugins</groupId>
         <artifactId>maven-shade-plugin</artifactId>
         <version>2.1.0</version>
         <executions>
           <execution>
             <phase>package</phase>
             <goals>
               <goal>shade</goal>
             </goals>
             <configuration>
               <transformers>
                 <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                 </transformer>
               </transformers>
             </configuration>
           </execution>
         </executions>
       </plugin>
     </plugins>
    ```

5.  Write code.

    Add a new class EmrToES.java that is parallel to the application class to the com.aliyun.emrtoes package. Add the following content:

    ```
    package com.aliyun.emrtoes;
     import org.apache.hadoop.conf.Configuration;
     import org.apache.hadoop.fs.Path;
     import org.apache.hadoop.io.NullWritable;
     import org.apache.hadoop.io.Text;
     import org.apache.hadoop.mapreduce.Job;
     import org.apache.hadoop.mapreduce.Mapper;
     import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
     import org.apache.hadoop.mapreduce.lib.input.TextInputFormat;
     import org.apache.hadoop.util.GenericOptionsParser;
     import org.elasticsearch.hadoop.mr.EsOutputFormat;
     import java.io.IOException;
     public class EmrToES {
         public static class MyMapper extends Mapper<Object, Text, NullWritable, Text> {
             private Text line = new Text();
             @Override
             protected void map(Object key, Text value, Context context)
                     throws IOException, InterruptedException {
                 if (value.getLength() > 0) {
                     line.set(value);
                     context.write(NullWritable.get(), line);
                 }
             }
         }
         public static void main(String[] args) throws IOException, ClassNotFoundException, InterruptedException {
             Configuration conf = new Configuration();
             String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
             //Alibaba Cloud Elasticsearch X-PACK username and password
             conf.set("es.net.http.auth.user", "X-PACK username");
             conf.set("es.net.http.auth.pass", "X-PACK password");
             conf.setBoolean("mapred.map.tasks.speculative.execution", false);
             conf.setBoolean("mapred.reduce.tasks.speculative.execution", false);
             conf.set("es.nodes", "The private address of your Elasticsearch instance");
             conf.set("es.port", "9200");
             conf.set("es.nodes.wan.only", "true");
             conf.set("es.resource", "blog/yunqi");
             conf.set("es.mapping.id", "id");
             conf.set("es.input.json", "yes");
             Job job = Job.getInstance(conf, "EmrToES");
             job.setJarByClass(EmrToES.class);
             job.setMapperClass(EsMapper.class);
             job.setInputFormatClass(TextInputFormat.class);
             job.setOutputFormatClass(EsOutputFormat.class);
             job.setMapOutputKeyClass(NullWritable.class);
             job.setMapOutputValueClass(Text.class);
             FileInputFormat.setInputPaths(job, new Path(otherArgs[0]));
             System.exit(job.waitForCompletion(true) ? 0 : 1);
         }
     }
    ```

6.  Compile and package.

    Run the following command in the project directory:

    ```
    mvn clean package
    ```

    After you have run the command, you can view the JAR package named emrtoes-1.0-SNAPSHOT-jar-with-dependencies.jar of the job in the target directory of the project.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134316/155359282740166_en-US.png)


## Complete the job in E-MapReduce {#section_p24_h1t_zgb .section}

1.  Test the data
    1.  Write the following data to blog.json:

        ```
        {"id":"1","title":"git introduction","posttime":"2016-06-11","content":"The main difference between svn and git..."}
        {"id":"2","title":"Introduction and simple use of Java Generics","posttime":"2016-06-12","content":"Basic operations: CRUD ..."}
        {"id":"3","title":"Basic operations of SQL","posttime":"2016-06-13","content":"The main difference between svn and git..."}
        {"id":"4","title":"Basic Hibernate framework","posttime":"2016-06-14","content":"Basic Hibernate framework..."}
        {"id":"5","title":"Basics of Shell","posttime":"2016-06-15","content":"What is Shell?..."}
        ```

    2.  Run the following scp remote copy command to upload the file to the Alibaba Cloud EMR cluster:

        ```
        scp blog.json root@your EIP:/root
        ```

    3.  Upload blog.json to HDFS:

        ```
        hadoop fs -mkdir /work
        hadoop fs -put blog.json /work
        ```

2.  Upload the JAR package

    Upload the JAR package stored in the target directory of the Maven project to the Alibaba Cloud EMR cluster:

    ```
    scp target/emrtoes-1.0-SNAPSHOT-jar-with-dependencies.jar root@YourIP:/root
    ```

3.  Execute the MR job

    Run the following command:

    ```
    hadoop jar emrtoes-1.0-SNAPSHOT-jar-with-dependencies.jar /work/blog.json
    ```

    If the job is successfully executed, the following message is displayed in the console:

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134316/155359282940167_en-US.png)


## Verify results {#section_nzs_n1t_zgb .section}

Run the following command to verify that the data is successfully written to Elasticsearch:

```
curl -u elastic -XGET es-cn-v0h0jdp990001rta9.elasticsearch.aliyuncs.com:9200/blog/_search? pretty
```

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134316/155359282940168_en-US.png)

You can also view the result on Kibana:

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134316/155359282940169_en-US.png)

## API analysis {#section_rnm_t1t_zgb .section}

During the Map process, data is read and written by line. The type of input key is object. The type of input value is text. The type of output key is NullWritable, which is a special type of Writable with zero-length serialization. No bytes are written to or read from the stream. It is used as a placeholder.

For example, in MapReduce, a key or value can be declared as NullWritable when you do not need to use the key or value. This example sets the output key to NullWritable. If the output value is set to BytesWritable, serialize the JSON strings.

The Reduce process is not required because only data writing is performed.

**Parameter descriptions**

-   conf.set\(“es.net.http.auth.user”, “X-PACK username”\)

    This parameter specifies the X-PACK username.

-   conf.set\(“es.net.http.auth.pass”, “X-PACK password”\)

    This parameter specifies the X-PACK password.

-   conf.setBoolean\(“mapred.map.tasks.speculative.execution”, false\)

    This parameter disables speculative execution for the reducers.

-   Conf.setBoolean\(“mapred.reduce.tasks.speculative.exe cution ", false\)

    This parameter disables speculative execution for the mappers.

-   conf.set\(“es.nodes”, “The internal network address of your Elasticsearch”\)

    This parameter specifies the IP address and port for logging on to the Elasticsearch instance.

-   conf.set\(“es.resource”, “blog/yunqi”\)

    This parameter specifies the index names and types that are used to index the data written to the Elasticsearch instance.

-   conf.set\(“es.mapping.id”, “id”\)

    This parameter specifies the document IDs. “id” indicates the ID column in the document.

-   conf.set\(“es.input.json”, “yes”\)

    This parameter specifies the format of the input files as JSON.

-   job.setInputFormatClass\(TextInputFormat.class\)

    This parameter specifies the format of the input stream as text.

-   job.setOutputFormatClass\(EsOutputFormat.class\)

    This parameter specifies the output format as EsOutputFormat.

-   job.setMapOutputKeyClass\(NullWritable.class\)

    This parameter specifies the the output key format of Map as NullWritable.

-   job.setMapOutputValueClass\(BytesWritable.class\)

    This parameter specifies the output value format of Map as BytesWritable.

-   FileInputFormat.setInputPaths\(job, new Path\(otherArgs\[0\]\)\)

    This parameter specifies the path of the files that you need to upload to HDFS.


