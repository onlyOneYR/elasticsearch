# RDS for MySQL与阿里云ES实时同步数据 {#concept_a5h_jsr_zgb .concept}

[Data Transmission Service](https://www.alibabacloud.com/product/data-transmission-service) （以下简称 DTS）支持RDS for MySQL与阿里云Elasticsearch实时同步数据，通过 DTS 提供的 RDS for MySQL-\>阿里云Elasticsearch实时同步功能，可以将企业线上RDS for MySQL中的生产数据实时同步到阿里云Elasticsearch中进行搜索。本小节介绍如何使用 DTS 快速创建RDS for MySQL-\>阿里云Elasticsearch的实时同步作业，实现RDS for MySQL数据到阿里云Elasticsearch的实时同步。

## 支持实时同步类型 {#section_gcv_lks_zgb .section}

同一个阿里云账号下 RDS for MySQL-\>阿里云Elasticsearch实例。

## 支持SQL操作类型 {#section_kn1_nks_zgb .section}

主要支持的SQL操作类型如下：

-   Insert
-   Delete
-   Update

**说明：** 目前暂不支持 DDL同步，如果同步过程中遇到DDL操作，DTS会忽略掉。

如果后续遇到DDL某个表，则对应表的DML操作可能失败，修复方法为：

1.  参考[减少同步对象](https://www.alibabacloud.com/help/zh/doc-detail/26635.htm)先将这个对象从同步列表中摘除。
2.  删除阿里云Elasticsearch中这个表对应的索引。
3.  参考 [新增同步对象](https://www.alibabacloud.com/help/zh/doc-detail/26634.htm)， 修改这个同步作业，将这个表重新添加到同步对象中，进行重新初始化。

如果是修改表、新增列的DDL，建议DDL的操作顺序为：

1.  先在阿里云Elasticsearch中手动修改对应表的mapping，新增列。
2.  再在源RDS for MySQL实例中手动修改表结构，新增列。
3.  暂停DTS同步实例，重启DTS同步实例让DTS重新加载阿里云Elasticsearch中修改后的mapping关系。

## 配置步骤 {#section_df1_qks_zgb .section}

下面详细介绍创建RDS for MySQL实例到阿里云Elasticsearch实例同步链路的具体步骤。

1.  购买同步链路

    进入[数据传输服务 DTS控制台](https://dts.console.aliyun.com/)，进入数据同步界面，点击控制台右上角**创建同步作业**先购买一个同步链路，购买完同步链路后返回DTS控制台，进行配置同步链路。

    **说明：** 在配置同步链路之前需要先购买一个同步链路，同步链路目前支持**包年包月**及**按量付费**两种付费模式，可以根据需要选择不同的付费模式。

    **购买界面参数**

    -   功能

        选择数据同步。

    -   源实例

        选择MySQL。

    -   源实例地域
        -   本示例为RDS for MySQL，需选择RDS for MySQL实例所在地域。
    -   目标实例

        选择Elasticsearch。

    -   目标实例地域

        阿里云Elasticsearch实例所在地域，订购后不支持更换地域，请谨慎选择。

    -   同步链路规格

        同步链路规格影响了链路的同步性能，同步链路规格跟性能之间的对应关系详见。[数据同步规格说明](https://www.alibabacloud.com/help/zh/doc-detail/26605.htm)

    -   订购时长
        -   如果是预付费，默认为1个月，支持勾选开启自动续费功能。
    -   购买数量

        默认为1，根据业务实际需要进行选择。

        **说明：** DTS控制台的同步实例按照地域展示，刚才购买的同步实例所属的地域为同步实例的目标地域。例如上面购买的是 杭州RDS for MySQL-\>杭州阿里云Elasticsearch的同步实例，那么这个同步实例在DTS的杭州地区。进入杭州区域的实例列表，查找刚才购买的同步实例，然后点击新购实例右侧的**配置同步作业**开始配置实例。

2.  配置同步链路

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134314/155487644540099_zh-CN.png)

    **同步作业名称**

    同步作业名称没有唯一性要求，为了更方便识别具体的作业，建议选择一个有业务意义的作业名称，方便后续的链路查找及管理。

    **源实例信息**

    本示例采用数据源为 RDS for MySQL，需要配置RDS实例的ID、数据库账号、数据库密码。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134314/155487644540100_zh-CN.png)

    **目标实例信息**

    目标实例信息中需要配置阿里云Elasticsearch的实例ID，及访问阿里云ES实例账号密码。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134314/155487644540101_zh-CN.png)

    以上内容配置完成后，点击**授权白名单并进入下一步**进行RDS for MySQL及阿里云Elasticsearch的白名单添加。

3.  授权实例白名单

    **说明：** 如果是RDS for MySQL，DTS会自动添加白名单或安全组。

    如果源实例为RDS for MySQL，那么DTS将自身的IP段添加到RDS实例的白名单的安全组中，避免因为RDS实例设置了白名单，DTS服务器连接不上数据库导致同步作业创建失败。为了保证同步作业的稳定性，在同步过程中，请勿将这些服务器 IP 从 RDS实例的白名单的安全组中删除。

    当白名单授权后，点击**下一步**，进入同步账号创建。

4.  选择同步对象

    当白名单授权完成后，即进入同步对象的选择步骤。在这个步骤可以配置需要同步的表列，以及索引的命名规则。

    1.  索引名称命名规则可以选择：表名、库名\_表名。

        -   如果选择了表名，那么索引名称同表名。
        -   如果选择了库名表名，那么索引名称的命名格式为：库名表名。例如，库名为：dbtest,表名为：sbtest1，那么这张表同步到阿里云Elasticsearch后，对应的索引名称为：dbtest\_sbtest1。
        -   如果需要同步的不同库中存在相同名称的表名，建议索引名称命名规则选择：库名\_表名。
    2.  选择具体需要同步的库表列，实时同步的同步对象的选择粒度可以支持到表级别，即用户可以选择同步某些库或某几张表。

        实时同步的同步对象的选择粒度可以支持到表级别，即用户可以选择同步某些库或某几张表。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134314/155487644540102_zh-CN.png)

    3.  默认所有表的docid为表的主键，如果部分表没有主键，那么对于这部分配置docid 对应的源表的列。在右侧-已选择对象 框中，将鼠标挪到对应表上，点击右侧的 编辑 入口，进入这个表的高级设置界面。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134314/155487644540103_zh-CN.png)

    4.  在高级配置中可以设置：

        索引名称、Type名称、分区列及分区数定义、\_id取值列。其中 \_id 取值如果选择 业务主键，那么需要选择对应的业务主键列。

    5.  配置完同步对象后，进入高级配置步骤。

5.  高级配置

    **主要配置**

    1.  **同步初始化类型**，建议选择 结构初始化+全量数据初始化，由DTS自动进行索引的创建及全量数据的初始化。如果不选择结构初始化，那么需要在同步创建之前，先手动在阿里云Elasticsearch中完成索引mapping的定义。如果不选择全量数据初始化，那么DTS同步增量数据的起始时间点为：启动同步的时间点。

    2.  **索引分片配置**，默认为5个分片，1个副本。可以根据业务需要进行调整，一旦调整后，所有的索引按照这个配置定义分片。

    3.  **字符串analyzer定义**，可以选择字符串的analyzer，默认为Standard Analyzer。取值包括：Standard Analyzer、Simple Analyzer、Whitespace Analyzer、Stop Analyzer、Keyword Analyzer、English Analyzer、Fingerprint Analyzer，所有索引的字符串字段按照这个配置定义Analyzer。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134314/155487644540104_zh-CN.png)

    4.  **时区**，可以配置同步到阿里云Elasticsearch中的时间字段存储的时区，默认为东八区。

6.  预检查

    同步作业配置完成后，DTS会进行预检查，当预检查通过后，可以点击 **启动** 按钮，启动同步作业。

    同步作业启动后，即进入同步作业列表，此时刚启动的作业处于**同步初始化**状态。初始化的时间长度取决于源实例中同步对象的数据量大小，初始化完成后，同步链路即进入**同步中**的状态，此时源跟目标实例的同步链路才真正建立。

7.  数据效验

    以上任务完执行成后，登录阿里云ES控制台，确认对应阿里云ES实例中有无创建对应索引，及同步的数据是否符合预期。


