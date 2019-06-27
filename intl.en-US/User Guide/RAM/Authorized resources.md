# Authorized resources {#concept_lx1_mts_zgb .concept}

## Resource types and descriptions {#section_bw0_lnk_iuj .section}

The following table lists the supported resource types and the corresponding Aliyun resource names \(ARN\).

|Resource type|ARN|
|-------------|---|
|instances|acs:elasticsearch:$regionId:$accountId:instances/\*|
|instances|acs:elasticsearch:$regionId:$accountId:instances/$instanceId|
|vpc|acs:elasticsearch:$regionId:$accountId:vpc/\*|
|vswitch|acs:elasticsearch:$regionId:$accountId:vswitch/\*|

-   $regionId: the ID of the specified region. You can also enter an asterisk `*`.
-   $accountId: the ID of your Alibaba Cloud account. You can also enter an asterisk `*`.
-   $instanceId: the ID of a specified Alibaba Cloud Elasticsearch instance. You can also enter an asterisk `*`.

## Instance authorization {#section_ovr_dly_zgb .section}

**Note:** The following ARNs are shortened. For the complete name information, see the preceding table.

-   Common actions on instances

    |Action|Description|ARN|
    |------|-----------|---|
    |elasticsearch:CreateInstance|You can perform this action to create an instance.|`instances/*`|
    |elasticsearch:ListInstance|You can perform this action to view instances.|`instances/*`|
    |elasticsearch:DescribeInstance|You can perform this action to view instance description.|`instances/*` or `instances/$instanceId`|
    |elasticsearch:DeleteInstance|You can perform this action to delete an instance.|`instances/*` or `instances/$instanceId`|
    |elasticsearch:RestartInstance|You can perform this action to restart an instance.|`instances/*` or `instances/$instanceId`|
    |elasticsearch:UpdateInstance|You can perform this action to update an instance.|`instances/*` or `instances/$instanceId`|

-   Actions on plug-ins

    |Action|Description|ARN|
    |------|-----------|---|
    |elasticsearch:ListPlugin|You can perform this action to obtain the list of plug-ins.|`instances/$instanceId`|
    |elasticsearch:InstallSystemPlugin|You can perform this action to install system plug-ins.|`instances/$instanceId`|
    |elasticsearch:UninstallPlugin|You can perform this action to uninstall a plug-in.|`instances/$instanceId`|

-   Actions on networks

    |Action|Description|ARN|
    |------|-----------|---|
    |elasticsearch:UpdatePublicNetwork|You can perform this action to check whether access through the public address is allowed.|`instances/$instanceId`|
    |elasticsearch:UpdatePublicIps|You can perform this action to modify the public network whitelist.|`instances/$instanceId`|
    |elasticsearch:UpdateWhiteIps|You can perform this action to modify the VPC whitelist.|`instances/$instanceId`|
    |elasticsearch:UpdateKibanaIps|You can perform this action to modify the Kibana whitelist.|`instances/$instanceId`|

-   Actions on dictionaries

    |Action|Description|ARN|
    |------|-----------|---|
    |elasticsearch:UpdateDict|You can perform this action to modify the IK analyzer and synonym dictionary.|`instances/$instanceId`|


## Authorized CloudMonitor actions \(CloudMonitor console\) {#section_agc_jly_zgb .section}

**Note:** The following ARNs are shortened to a \* wildcard form.

|Action|Description|ARN format|
|------|-----------|----------|
|cms:ListProductOfActiveAlert|You can perform this action to view services that have CloudMonitor enabled.|\*|
|cms:ListAlarm|You can perform this action to query the specified or all alarm rule settings.|\*|
|cms:QueryMetricList|You can perform this action to query the monitoring data of a specified instance.|\*|

## VPC and VSwitch authorization {#section_dag_axh_yow .section}

**Note:** The following ARNs are shortened. For the complete name information, see the preceding table.

|Action|Description|ARN|
|------|-----------|---|
|DescribeVpcs|You can perform this action to obtain a VPC list.|`vpc/*`|
|DescribeVswitches|You can perform this action to obtain a VSwitch list.|`vswitch/*`|

## Intelligent Maintenance authorization {#section_owj_4ly_zgb .section}

**Note:** The following ARNs are shortened. For the complete name information, see the preceding table.

|Action|Description|ARN|
|------|-----------|---|
|elasticsearch:OpenDiagnosis|You can perform this action to enable health diagnosis.|`instances/*` or `instances/$instanceId`|
|elasticsearch:CloseDiagnosis|You can perform this action to disable health diagnosis.|`instances/*` or `instances/$instanceId`|
|elasticsearch:UpdateDiagnosisSettings|You can perform this action to update the health diagnosis settings.|`instances/*` or `instances/$instanceId`|
|elasticsearch:DescribeDiagnosisSettings|You can perform this action to query the health diagnosis settings.|`instances/*` or `instances/$instanceId`|
|elasticsearch:ListInstanceIndices|You can perform this action to query instance indexes.|`instances/*` or `instances/$instanceId`|
|elasticsearch:DiagnoseInstance|You can perform this action to start health diagnosis.|`instances/*` or `instances/$instanceId`|
|elasticsearch:ListDiagnoseReportIds|You can perform this action to query diagnosis report IDs.|`instances/*` or `instances/$instanceId`|
|elasticsearch:DescribeDiagnoseReport|You can perform this action to view diagnosis report details.|`instances/*` or `instances/$instanceId`|
|elasticsearch:ListDiagnoseReport|You can perform this action to list diagnosis reports.|`instances/*` or `instances/$instanceId`|

## Supported regions {#section_ydy_ply_zgb .section}

|Elasticsearch region|RegionId|
|--------------------|--------|
|China \(Hangzhou|cn-hangzhou-d|
|China \(Beijing\)|cn-beijing|
|China \(Shanghai\)|cn-shanghai|
|China \(Shenzhen|cn-shenzhen|
|India \(Mumbai\)|ap-south-1|
|Singapore|ap-southeast-1|
|cn-hongkong|cn-hongkong|
|US \(Silicon Valley\)|us-west-1|
|Malaysia \(Kuala Lumpur\)|ap-southeast-3|
|Germany \(Frankfurt\)|eu-central-1|
|Japan \(Tokyo|ap-northeast-1|
|Australia \(Sydney|ap-southeast-2|
|Indonesia \(Jakarta\)|ap-southeast-5|
|China \(Qingdao\)|cn-qingdao|
|China \(Zhangjiakou\)|cn-zhangjiakou|

