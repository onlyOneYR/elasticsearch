# Access authentication rules {#concept_kj1_lts_zgb .concept}

## General permission policies {#section_wqc_nhy_zgb .section}

The following two general permission policies are provided to meet the needs for common access, so that you can select a permission policy suitable for you. You can search for the policy name in the brackets from **Optional Authorization Policy Names** and select it.

-   Read-only permissions for Elasticsearch instances, applicable for read-only users \(AliyunElasticsearchReadOnlyAccess\).
-   Administrator permissions for Elasticsearch instances, applicable for the administrator \(AliyunElasticsearchFullAccess\).

    **Note:** If none of the above general permission policies can meet your needs, you can refer to the following description and customize a permission policy.


## Permission to buy instances \(post-payment & prepayment\) {#section_kt1_qhy_zgb .section}

 **Permission to access the VPC of the primary account** 

-   \[“vpc:DescribeVSwitch\*”,“vpc:DescribeVpc\*”\]

    **Note:** You can refer to the system template AliyunVPCReadOnlyAccess.


## Subaccount order permission {#section_mrn_shy_zgb .section}

-   \[“bss:PayOrder”\]

    **Note:** You can refer to the system template AliyunBSSOrderAccess.


## API permissions {#section_ezj_5hy_zgb .section}

|Method|URI|Resource|Action|
|------|---|--------|------|
|GET|/instances|instances/\*|ListInstance|
|POST|/instances|instances/\*|CreateInstance|
|GET|/instances/$instanceId|instances/$instanceId|DescribeInstance|
|DELETE|/instances/$instanceId|instances/$instanceId|DeleteInstance|
|POST|/instances/$instanceId/actions/restart|instances/$instanceId|RestartInstance|
|PUT|/instances/$instanceId|instances/$instanceId|UpdateInstance|

## Authorization examples {#section_wrq_m3y_zgb .section}

-   [Authorized resources](reseller.en-US/User Guide/RAM/Authorized resources.md#) \(for example, $regionid, $accountid, and $instanceId\).
-   Elasticsearch instances in the resource can be indicated by the wildcard `*`.

 **Authorization example 1** 

To a subaccount under the primary account \(accountId “1234”\), assign all operation permissions, except for CreateInstance, over all instances in China East 1 \(Hangzhou\) on the console, and set the instances to be accessible from only the specified IP address.

After this policy is created on the console of the primary account, you need to use your primary account on the RAM console or the RAM SDK to authorize the subaccount.

1.  Create a policy

    ``` {#codeblock_0m8_j1c_lmh}
    {
      "Statement ":[
        {
          "Action": [
            "imagesearch:ListInstance",
            "imagesearch:DescribeInstance",
            "elasticsearch:DeleteInstance",
            "elasticsearch:RestartInstance",
            "elasticsearch:UpdateInstance"
          ],
          "Condition": {
            "IpAddress": {
              "acs:SourceIp": "xxx.xx.xxx.x/xx"
            }
          },
          "Effect": "Allow",
          "Resource": "acs:imagesearch:cn-shanghai:1234:instance/*"
        }
      ],
      "Version": "1"
    }
    ```

2.  Authorize the current policy to your specified subaccount.

 **Authorization example 2** 

For a subaccount under the primary account \(accountId “1234”\), assign all operation permissions, except for CreateInstance, over the specified instances in China East 1 \(Hangzhou\) on the console, and set the instances to be accessible from only the specified IP address.

After this policy is created on the console of the primary account, you should authorize the subaccount through your primary account on the RAM console or use RAM SDK to authorize the subaccount.

1.  Create a policy

    ``` {#codeblock_6jj_csj_it7}
    {
      "Statement ":[
        {
          "Action": [
            "elasticsearch:ListInstance"
          ],
          "Condition": {
            "IpAddress": {
              "acs:SourceIp": "xxx.xx.xxx.x/xx"
            }
          },
          "Effect": "Allow",
          "Resource": "acs:imagesearch:cn-shanghai:1234:instance/*"
        },
        {
          "Action": [
            "elasticsearch:DescribeInstance",
            "elasticsearch:DeleteInstance",
            "elasticsearch:RestartInstance",
            "elasticsearch:UpdateInstance"
          ],
          "Condition": {
            "IpAddress": {
              "acs:SourceIp": "xxx.xx.xxx.x/xx"
            }
          },
          "Effect": "Allow",
          "Resource": "acs:elasticsearch:cn-hangzhou:1234:instances/$instanceId"
        }
      ],
      "Version": "1"
    }
    ```

2.  Authorize the current policy to your specified subaccount.

 **Authorization example 3** 

To a subaccount under the primary account \(accountId “1234”\), assign all operation permissions over all instances in all regions supported by Alibaba Cloud Elasticsearch on the console.

After this policy is created on the console of the primary account, you should authorize the subaccount through your primary account on the RAM console or use RAM SDK to authorize the subaccount.

1.  Create a policy

    ``` {#codeblock_mi3_wou_69n}
    {
      "Statement ":[
        {
          "Action": [
              "elasticsearch:*"
                ],
          "Effect": "Allow",
          "Resource": "acs:imagesearch:*:1234:instance/*"
        }
      ],
      "Version": "1"
    }
    ```

2.  Authorize the current policy to your specified subaccount.

 **Authorization example 4** 

To a subaccount under the primary account \(accountId “1234”\), assign all operation permissions, except for CreateInstance and ListInstance, over specified instances in all regions supported by Alibaba Cloud Elasticsearch on the console.

After this policy is created on the console of the primary account, you should authorize the subaccount through your primary account on the RAM console or use RAM SDK to authorize the subaccount.

1.  Create a policy

    ``` {#codeblock_219_xk3_mk7}
    {
      "Statement ":[
        {
          "Action": [
              "elasticsearch:DescribeInstance",
              "elasticsearch:DeleteInstance",
              "elasticsearch:UpdateInstance",
              "elasticsearch:RestartInstance"
                ],
          "Effect": "Allow",
          "Resource": "acs:elasticsearch:*:1234:instances/$instanceId"
        }
      ],
      "Version": "1"
    }
    ```

2.  Authorize the current policy to your specified subaccount.

