# 通过ES-Hadoop将Hadoop数据写入阿里云Elasticsearch {#concept_knr_4ns_zgb .concept}

基于阿里云Elasticsearch和E-MapReduce，通过ES-Hadoop可直接将数据写入阿里云Elasticsearch。

## 支持版本 {#section_z2y_wxs_zgb .section}

阿里云Elasticsearch 5.5.3 with X-Pack。

**说明：** 不支持阿里云Elasticsearch 6.3.2 with X-Pack 版本。

## 开通服务 {#section_wvk_yxs_zgb .section}

本示例需要用到的阿里云产品如下：

-   专有网络VPC：由于通过公网访问推送数据安全性较差，为保证阿里云Elasticsearch访问环境安全，对应区域下必须要有VPC和虚拟交换机，因此需开通VPC专有网络。
-   OSS：在本示例中将E-MapReduce的日志存储在OSS上，在开通配置E-MapReduce前需开通OSS并创建完成Bucket。
-   Elasticsearch
-   E-MapReduce

请参考以下步骤开通上述阿里云产品：

1.  开通阿里云VPC

    1.  登录阿里云首页后选择**产品** \> **云计算基础** \> **网络** \> **创建网络环境** \> **专有网络VPC**，然后单击**立即开通**。
    2.  进入到VPC管理控制台界面，新建专有网络。
    3.  创建完成之后在控制台中可以进行管理。

        **说明：** 更多关于专有网络VPC的文档请参[专有网络VPC](https://help.aliyun.com/product/27706.html?spm=a2c4g.11186623.2.12.142a7c17L4OIWa) 。

2.  开通专有网络OSS

    1.  登录阿里云首页后选择**产品** \> **云计算基础** \> **存储服务** \> **云存储** \> **对象存储 OSS**，然后单击**立即开通**。
    2.  进入到OSS管理控制台界面，单击**新建 Bucket**。

        **说明：** Bucket的区域要和E-MapReduce集群的区域一致，本示例将区域均选择为华东1区。

    3.  根据界面提示完成Bucket创建。
3.  开通阿里云Elasticsearch

    1.  登录阿里云首页后选择**产品** \> **大数据** \> **大数据搜索与分析** \> **Elasticsearch**，进入阿里云Elasticsearch产品界面。

        **说明：** 新用户可以免费试用30天

    2.  购买成功后，在Elasticsearch控制台可以看到新创建的Elasticsearch集群实例。
4.  开通阿里云E-MapReduce

    1.  登录阿里云首页后选择**产品** \> **大数据** \> **大数据计算** \> **E-MapReduce**，进入E-MapReduce产品页面。
    2.  单击**立即购买**，根据界面提示完成参数配置。
    3.  E-MapReduce集群创建成功后在集群列表中查看，也可通过以下操作验证集群创建结果：
        -   公网IP可以直接访问，远程登录：

            ```
            ssh root@你的公网IP
            ```

        -   使用jps命令查看后台进程：

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


## 编写EMR写数据到ES的MR作业 {#section_rnt_xzs_zgb .section}

推荐使用maven来进行项目管理，操作步骤如下：

1.  安装 Maven

    首先确保计算机已经正确安装[maven](http://maven.apache.org/index.html?spm=a2c4g.11186623.2.15.142a7c17L4OIWa)。

2.  生成工程框架

    在工程根目录处执行如下命令：

    ```
    mvn archetype:generate -DgroupId=com.aliyun.emrtoes -DartifactId=emrtoes -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    mvn 会自动生成一个空的Sample工程，工程名为emrtoes（和指定的artifactId一致），里面包含一个简单的 pom.xml和App类（类的包路径和指定的groupId一致）。

3.  加入Hadoop和ES-Hadoop依赖

    使用任意IDE打开这个工程，编辑pom.xml文件。在dependencies内添加如下内容：

    ```
    <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-client-common</artifactId>
          <version>2.7.3</version>
      </dependency>
      <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-common</artifactId>
          <version>2.7.3</version>
      </dependency>
       <dependency>
           <groupId>org.elasticsearch</groupId>
           <artifactId>elasticsearch-hadoop-mr</artifactId>
           <version>5.5.3</version>
       </dependency>
    ```

4.  添加打包插件

    由于使用了第三方库，需要把第三方库打包到jar文件中，在pom.xml中添加maven-assembly-plugin插件的坐标：

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
         <version>3.1.0</version>
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

5.  编写代码

    在com.aliyun.emrtoes包下和App类平行的位置添加新类EmrToES.java，内容如下：

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
             //阿里云 Elasticsearch X-PACK用户名和密码
             conf.set("es.net.http.auth.user", "你的X-PACK用户名");
             conf.set("es.net.http.auth.pass", "你的X-PACK密码");
             conf.setBoolean("mapred.map.tasks.speculative.execution", false);
             conf.setBoolean("mapred.reduce.tasks.speculative.execution", false);
             conf.set("es.nodes", "你的Elasticsearch内网地址");
             conf.set("es.port", "9200");
             conf.set("es.nodes.wan.only", "true");
             conf.set("es.resource", "blog/yunqi");
             conf.set("es.mapping.id", "id");
             conf.set("es.input.json", "yes");
             Job job = Job.getInstance(conf, "EmrToES");
             job.setJarByClass(EmrToES.class);
             job.setMapperClass(MyMapper.class);
             job.setInputFormatClass(TextInputFormat.class);
             job.setOutputFormatClass(EsOutputFormat.class);
             job.setMapOutputKeyClass(NullWritable.class);
             job.setMapOutputValueClass(Text.class);
             FileInputFormat.setInputPaths(job, new Path(otherArgs[0]));
             System.exit(job.waitForCompletion(true) ? 0 : 1);
         }
     }
    ```

6.  编译并打包

    在工程的目录下，执行如下命令：

    ```
    mvn clean package
    ```

    执行完毕以后，可在工程目录的target目录下看到一个emrtoes-1.0-SNAPSHOT-jar-with-dependencies.jar，这个就是作业jar包。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134316/155305103540166_zh-CN.png)


## EMR中完成作业 {#section_p24_h1t_zgb .section}

1.  测试数据
    1.  把下面的数据写入到blog.json中：

        ```
        {"id":"1","title":"git简介","posttime":"2016-06-11","content":"svn与git的最主要区别..."}
        {"id":"2","title":"ava中泛型的介绍与简单使用","posttime":"2016-06-12","content":"基本操作：CRUD ..."}
        {"id":"3","title":"SQL基本操作","posttime":"2016-06-13","content":"svn与git的最主要区别..."}
        {"id":"4","title":"Hibernate框架基础","posttime":"2016-06-14","content":"Hibernate框架基础..."}
        {"id":"5","title":"Shell基本知识","posttime":"2016-06-15","content":"Shell是什么..."}
        ```

    2.  上传到阿里云E-MapReduce集群，使用scp远程拷贝命令上传文件：

        ```
        scp blog.json root@你的弹性公网IP:/root
        ```

    3.  把blog.json上传至HDFS：

        ```
        hadoop fs -mkdir /work
        hadoop fs -put blog.json /work
        ```

2.  上传JAR包

    把maven工程target目录下的jar包上传至阿里云E-MapReduce集群：

    ```
    scp target/emrtoes-1.0-SNAPSHOT-jar-with-dependencies.jar root@YourIP:/root
    ```

3.  执行MR作业

    执行以下命令：

    ```
    hadoop jar emrtoes-1.0-SNAPSHOT-jar-with-dependencies.jar /work/blog.json
    ```

    运行成功的话，控制台会输出如下图所示信息：

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134316/155305103540167_zh-CN.png)


## 结果验证 {#section_nzs_n1t_zgb .section}

执行以下操作验证数据是否成功写入Elasticsearch。

```
curl -u elastic -XGET es-cn-v0h0jdp990001rta9.elasticsearch.aliyuncs.com:9200/blog/_search?pretty
```

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134316/155305103640168_zh-CN.png)

也可以通过Kibana查看：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134316/155305103640169_zh-CN.png)

## API分析 {#section_rnm_t1t_zgb .section}

Map过程按行读入，input kye的类型为Object，input value的类型为Text。输出的key为NullWritable类型，NullWritable是Writable的一个特殊类，实现方法为空实现，不从数据流中读数据，也不写入数据，只充当占位符。

MapReduce中如果不需要使用键或值，就可以将键或值声明为NullWritable，这里把输出的key设置NullWritable类型。输出为BytesWritable类型，把json字符串序列化。

在本示例中只需要写入，因此没有Reduce过程。

**参数配置说明**

-   conf.set\(“es.net.http.auth.user”, “你的X-PACK用户名”\)

    设置X-PACK的用户名

-   conf.set\(“es.net.http.auth.pass”, “你的X-PACK密码”\)

    设置X-PACK的密码

-   conf.setBoolean\(“mapred.map.tasks.speculative.execution”, false\)

    关闭mapper阶段的执行推测

-   conf.setBoolean\(“mapred.reduce.tasks.speculative.execution”, false\)

    关闭reducer阶段的执行推测

-   conf.set\(“es.nodes”, “你的Elasticsearch内网地址”\)

    配置Elasticsearch的IP和端口

-   conf.set\(“es.resource”, “blog/yunqi”\)

    设置索引到Elasticsearch的索引名和类型名

-   conf.set\(“es.mapping.id”, “id”\)

    设置文档id，这个参数”id”是文档中的id字段

-   conf.set\(“es.input.json”, “yes”\)

    指定输入的文件类型为json

-   job.setInputFormatClass\(TextInputFormat.class\)

    设置输入流为文本类型

-   job.setOutputFormatClass\(EsOutputFormat.class\)

    设置输出为EsOutputFormat类型

-   job.setMapOutputKeyClass\(NullWritable.class\)

    设置Map的输出key类型为NullWritable类型

-   job.setMapOutputValueClass\(BytesWritable.class\)

    设置Map的输出value类型为BytesWritable类型

-   FileInputFormat.setInputPaths\(job, new Path\(otherArgs\[0\]\)\)

    传入HDFS上的文件路径


