# Temporary access token {#concept_byk_lts_zgb .concept}

Users \(people or applications\) that only access your cloud resources occasionally are called temporary users. You can use Security Token Service \(STS, an extended authorization service of RAM\) to issue an access token to these users \(subaccounts\). The permission and automatic expiration time of the token can be defined as required upon issuing.

The advantage of using the STS access token to authorize temporary users is making the authorization more controllable. You do not need to create a RAM user account and key for the temporary users. The RAM user account and key are valid in the long term but the temporary users do not need to access the resources for long. For use cases, see [Grant temporary permissions to mobile apps](../../../../intl.en-US/Best Practices/Grant temporary permissions to mobile apps.md#) and [Cross-account resource authorization and access](../../../../intl.en-US/Best Practices/Cross-account resource authorization and access.md#).

## Create a role {#section_ryf_ypt_zgb .section}

1.  On the RAM console, choose **RAM Roles** \> **Create RAM Role**

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134307/156534262040209_en-US.png)

2.  Select the role type. Here, the role **User** is selected.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134307/156534262040210_en-US.png)

3.  Enter the type information. A subaccount of a trusted account can play the created role.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134307/156534262040211_en-US.png)

4.  Enter the role name.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134307/156534262040212_en-US.png)

5.  After a role is created, authorize the role. For details, see [Permission granting in RAM](../../../../intl.en-US/User Guide/(Old Version) User Guide/Permission granting/Permission granting in RAM.md#) and [Authorized resources](intl.en-US/User Guide/RAM/Authorized resources.md#).

## Temporary access authorization {#section_z2v_hqt_zgb .section}

Before using STS for access authorization, authorize the role to be assumed by the subaccount of the trusted cloud account created in Step 3. If any subaccount could assume these roles, unpredictable risks may occur. Therefore, in order to assume the corresponding role, a subaccount has to have explicitly configured permissions.

## Authorization of the trusted cloud account {#section_yjq_3qt_zgb .section}

1.  Click **Policy Management** on the left side of the page to go to the **Policy Management** page.
2.  Click **Create Authorization Policy** on the right side of the page to go to the **Create Authorization Policy** page.
3.  Select a **blank template** to go to the **Create Custom Authorization Policy** page.
4.  Enter the authorization policy name and fill the following content to the policy content field.

    ``` {#codeblock_y99_0hz_cfa}
    {
    "Version": "1",
    "Statement": [
    {
       "Effect": "Allow",
       "Action": "sts:AssumeRole",
       "Resource": "acs:ram::${aliyunID}:role/${roleName}"
    }
    ]
    }
    ```

    $\{aliyunID\} indicates the ID of the user that creates the role.

    $\{roleName\} indicates the role name in lowercase.

    **Note:** The resource details can be obtained from the Arn field in Role Details and Basic Information.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134307/156534262040213_en-US.png)

5.  On the **User Management** page, authorize the permission of the role created for the subaccount. For details, see [Permission granting in RAM](../../../../intl.en-US/User Guide/(Old Version) User Guide/Permission granting/Permission granting in RAM.md#).

## Role assumed by a subaccount {#section_rdx_pqt_zgb .section}

After logging on to the console through the subaccount, the subaccount can switch to the authorized role assumed by the subaccount to practise permissions of the role. The steps are as follows:

1.  Move the mouse to the profile picture on the upper-right corner of the navigation bar, and click Switch Role in the window.
2.  Enter the enterprise alias of the account with which you intend to create a role. If the enterprise alias is not modified, the account ID is used by default. Enter the role name and then click Switch to switch to the specified role.

