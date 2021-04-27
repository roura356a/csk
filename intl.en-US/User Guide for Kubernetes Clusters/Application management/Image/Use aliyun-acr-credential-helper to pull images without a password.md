---
keyword: [ACR, pull container images without a password, Kubernetes]
---

# Use aliyun-acr-credential-helper to pull images without a password

Container Registry provides the aliyun-acr-credential-helper component for you to pull private images without a password from Container Registry Enterprise Edition or Personal Edition. This component is automatically installed in Container Service for Kubernetes \(ACK\) clusters. This topic describes how to use aliyun-acr-credential-helper to pull a private image without a password in different scenarios.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md)

**Note:**

-   If you want to use aliyun-acr-credential-helper, do not manually specify the imagePullSecret parameter. If the imagePullSecret parameter is specified in the template of a Kubernetes resource, such as a Deployment, aliyun-acr-credential-helper becomes invalid.
-   If a Kubernetes resource, such as a Deployment, uses custom service accounts, you must modify the service-account parameter in the configuration file of aliyun-acr-credential-helper. This way, aliyun-acr-credential-helper is authorized to pull images by using the custom service accounts.
-   Check whether the private image that you want to pull is in the region where your ACK cluster resides. By default, you can pull private images only from Container Registry instances that reside in the same region as your ACK cluster. If you want to pull images from Container Registry instances that reside in different regions, see Scenario 3 in this topic.
-   If you want to use a RAM user to pull private images, you must specify the AccessKey ID and AccessKey secret of the RAM user in the acr-configuration ConfigMap. However, this may disclose the AccessKey information. To ensure data security, grant the RAM user only the permissions to pull images.

aliyun-acr-credential-helper reads the configuration from the acr-configuration ConfigMap that is created in the kube-system namespace and then pulls private images. You can pull private images by using the following methods:

-   Assume the worker role of an ACK cluster to pull images from Container Registry instances that are created within your account. This is the default method.
-   Use the AccessKey ID and AccessKey secret of a custom RAM user to pull private images.
-   Assume the RAM role of another account to pull images from Container Registry instances of that account.

aliyun-acr-credential-helper supports the following images and clusters:

-   Supported images
    -   You can use aliyun-acr-credential-helper to pull private images from instances of Container Registry Enterprise Edition and Personal Edition.
    -   You can use aliyun-acr-credential-helper to pull private images from Container Registry instances within your own account. You can also pull private images from Container Registry instances of other accounts after authorization or by using the AccessKey ID and AccessKey secret.
    -   You can use aliyun-acr-credential-helper to pull private images from Container Registry instances that reside in regions different from your ACK cluster.
-   Supported clusters
    -   You can use aliyun-acr-credential-helper to pull images without a password from clusters that contain multiple namespaces.
    -   Supported cluster types:
        -   Dedicated Kubernetes clusters.
        -   Managed Kubernetes clusters.
    -   Supported cluster versions:
        -   Dedicated Kubernetes clusters: The Kubernetes version must be V1.11.2 or later. If the Kubernetes version is earlier than V1.11.2, you must manually update the Kubernetes version to V1.11.2 or later. For more information, see [Upgrade a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Upgrade cluster/Upgrade a cluster.md).
        -   Managed Kubernetes clusters: all versions.

## Update and configure aliyun-acr-credential-helper

Before you use aliyun-acr-credential-helper to pull images, you must update and configure aliyun-acr-credential-helper by performing the following steps:

1.  Update aliyun-acr-credential-helper.

    1.  Log on to the [the ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the **Clusters** page, find the cluster that you want to manage and choose **More** \> **Manage System Components** in the Actions column.

    4.  On the page that appears, click the **Security** tab, find **aliyun-acr-credential-helper**, and then click **Upgrade** for this component.

2.  Configure the acr-configuration ConfigMap.

    **Configure the acr-configuration ConfigMap in the ACK console**

    1.  Log on to the [the ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the **Clusters** page, find the cluster that you want to manage and click **Details** in the Actions column.

    4.  In the left-side navigation pane of the cluster details page, choose **Configurations** \> **ConfigMaps**.

    5.  At the top of the **ConfigMap** page, select **kube-system** from the Namespace drop-down list and find the **acr-configuration** ConfigMap. Then, modify the ConfigMap by using one of the following methods:

        -   Method 1: Click **Edit** in the Actions column for the ConfigMap and edit the keys and values of the ConfigMap.

            If your cluster does not have the **acr-configuration** ConfigMap, create one. For more information, see [Create a ConfigMap on the ConfigMap tab](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Configuration items and key/Create a ConfigMap.md). For more information about how to update a ConfigMap, see [t15755.md\#section\_2px\_yjo\_j61](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Configuration items and key/Create a ConfigMap.md).

        -   Method 2: Click **Edit YAML** in the Actions column for the ConfigMap and edit the keys and values of the ConfigMap.
        The following table describes the keys and values of acr-configuration ConfigMap.

        |Key|Description|Setting|
        |---|-----------|-------|
        |service-account|The service accounts that are used by aliyun-acr-credential-helper to pull images.|Default value: **default**. **Note:** Separate multiple service accounts with commas \(,\). Enter an asterisk \(\*\) to specify all service accounts in the specified namespace. |
        |acr-registry-info|The information about Container Registry instances. Each instance can be specified by three string-type fields in a YAML file. **Note:** Set the three fields based on the following descriptions:

        -   instanceId: the ID of the Container Registry instance. This field is required for instances of Container Registry Enterprise Edition.
        -   regionId: the ID of the region where the Container Registry instance resides. This field is optional. The default value is the region where your ACK cluster resides.
        -   domains: the domain names of the Container Registry instance. This field is optional. By default, all domain names of the instance are specified. Separate multiple domain names with commas \(,\).
|By default, the value is empty. This means that images are pulled from the Container Registry default instance that resides in the same region as the ACK cluster. Sample configuration for a Container Registry Enterprise Edition instance:

        ```
- instanceId: cri-xxx
  regionId: cn-hangzhou
  domains: xxx.com,yyy.com
        ```

Sample configuration for the Container Registry default instance:

        ```
- instanceId: ""
  regionId: cn-hangzhou
  domains: xxx.com,yyy.com
        ``` |
        |watch-namespace|The namespaces from which the images are pulled without a password.|Default value: **default**. **Note:** If the value is set to all, images can be pulled from all namespaces without a password. Separate multiple namespaces with commas \(,\). |
        |expiring-threshold|The duration after which the cache token expires.|Default value: **15m**.|

    **Configure the acr-configuration ConfigMap by using kubectl**

    1.  Run the following command to go to the editing page of the acr-configuration ConfigMap:

        ```
        kubectl edit acr-configuration -n kube-system
        ```

    2.  Edit the values of the acr-configuration ConfigMap based on your business requirements.

        Sample configurations for Container Registry Enterprise Edition and Personal Edition:

        -   Enterprise Edition

            ```
            apiVersion: v1
            data:
              acr-api-version: "2018-12-01"
              acr-registry-info: |-
                - instanceId: "cri-xxx"
                  regionId: "cn-hangzhou"
              expiring-threshold: 15m
              service-account: default
              watch-namespace: all
            kind: ConfigMap
            metadata:
             name: acr-configuration
              namespace: kube-system
             selfLink: /api/v1/namespaces/kube-system/configmaps/acr-configuration
            ```

        -   Default edition

            ```
            apiVersion: v1
            data:
              acr-api-version: "2018-12-01"
              acr-registry-info: |-
                - instanceId: ""
                    regionId: "cn-hangzhou"
              expiring-threshold: 15m
              service-account: default
              watch-namespace: all
            kind: ConfigMap
            metadata:
             name: acr-configuration
             namespace: kube-system
             selfLink: /api/v1/namespaces/kube-system/configmaps/acr-configuration
            ```


## Scenario 1: Pull private images from instances of Container Registry Enterprise Edition and Personal Edition

ACK allows you to pull private images from both Container Registry Enterprise Edition and Personal Edition, only from Container Registry Enterprise Edition, or only from Container Registry Personal Edition. Modify the **acr-configuration** ConfigMap based on your business requirements. For more information, see [Update and configure aliyun-acr-credential-helper](#step_85j_3yv_akc). Sample configurations:

-   Pull private images only from Container Registry Enterprise Edition.

    ```
    data:    
        service-account: "default"    
        watch-namespace: "all"    
        expiring-threshold: "15m"    
        notify-email: "cs@aliyuncs.com"    
        acr-registry-info: |      
          - instanceId: "cri-xxx"
            regionId: "cn-hangzhou"         
            domains: "xxx.com","yyy.com"
    ```

-   Pull private images only from Container Registry Personal Edition.

    ```
    data:     
        service-account: "default"
        watch-namespace: "all"
        expiring-threshold: "15m"
        notify-email: "cs@aliyuncs.com"
        acr-registry-info: |
          - instanceId: ""
            regionId: "cn-hangzhou"
            domains: "xxx.com","yyy.com"
    ```

-   Pull private images from both Container Registry Enterprise Edition and Personal Edition.

    ```
    data:
        service-account: "default"
        watch-namespace: "all"
        expiring-threshold: "15m"
        notify-email: "cs@aliyuncs.com"
        acr-registry-info: |
          - instanceId: ""
          - instanceId: "cri-xxxx"
    ```


## Scenario 2: Pull images by using the current account

If you want to pull images by using the current account, you must check whether the current account has the permissions to pull images from Container Registry instances.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the cluster details page, click the **Cluster Resources** tab. On the Cluster Resources tab, click the hyperlink next to **Worker RAM role**.

5.  On the page that appears, click the **Permissions** tab, and click the policy name in the **Policy** column.

6.  Click **Modify Policy Document**.

7.  In the **Policy Document** section, add the following content and click **OK**.

    ```
    {
      "Action": [
        "cr:Get*",
        "cr:List*",
        "cr:PullRepository"
      ],
      "Resource": "*",
      "Effect": "Allow"
    }
    ```


**Note:** If you fail to pull images without a password by using the current account, you must check whether the image specified in the pod exists in the Container Registry repository.

-   If the image exists in the Container Registry repository but is not found on the Container Registry instances that are created by the current account, you need to pull the image across accounts. For more information, see Scenario 4 in this topic.
-   If the image exists in the Container Registry repository and is found on the Container Registry instances that are created by the current account, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).
-   If the image is not found in the Container Registry repository, you need to check whether the image can be found on Container Registry. If the image is not found on Container Registry, you need to synchronize the external image to Container Registry or change the image status so that the image can be pulled over the Internet.

## Scenario 3: Pull images across regions

If you want to pull images from Container Registry instances that reside in different regions, you must modify the **acr-configuration** ConfigMap.

For example, if you want to pull images from Container Registry instances that reside in the China \(Beijing\) and China \(Hangzhou\) regions at the same time, modify acr-configuration, as shown in the following sample code. For more information, see [Update and configure aliyun-acr-credential-helper](#step_85j_3yv_akc).

```
data:
    service-account: "default"
    watch-namespace: "all"
    expiring-threshold: "15m"
    notify-email: "cs@aliyuncs.com"
    acr-registry-info: |
      - instanceId: ""
        regionId: cn-beijing
      - instanceId: ""
        regionId: cn-hangzhou            
```

## Scenario 4: Pull images across accounts

In this scenario, you can pull images across accounts by using the following methods:

-   **Pull images across accounts by assuming a RAM role**: Account A assumes the RAM role of Account B to pull private images from Container Registry instances of Account B.
-   **Pull images across accounts by using the AccessKey information of a RAM user**: The RAM user must have the permissions to pull specified images.

**Pull images across accounts by assuming a RAM role**

**Note:**

Follow these rules when you pull images across accounts:

1.  The RAM role of Account B is authorized to pull private images from a specified private repository. This rule requires you to grant Container Registry-related permissions to the RAM role of Account B.
2.  Account B allows the worker role of the ACK cluster that is created by Account A to assume the RAM role of Account B. This rule requires you to modify the trust policy of the RAM role of Account B.
3.  The worker role of the ACK cluster created by Account A has the permissions to assume the RAM role of Account B. This rule requires you to attach the AliyunAssumeRoleAccess policy to the worker role of Account A.
4.  Set the worker role of Account A to assume the RAM role of Account B. This rule requires you to specify the assumeRoleARN field in the acr-configuration ConfigMap.

1.  Create a RAM role of Account B. Specify **Alibaba Cloud accounts** as the trusted entity of the RAM role. Make sure that the created RAM role has the permissions to pull private images from Container Registry instances of Account B.

    1.  Create a RAM role.

        For more information, see [Create a RAM role for a trusted Alibaba Cloud account](/intl.en-US/RAM Role Management/Create a RAM role/Create a RAM role for a trusted Alibaba Cloud account.md).

    2.  Customize the permissions of the created RAM role. Make sure that the RAM role has the permissions to pull private images from Container Registry instances of Account B.

        For more information, see [Modify a custom policy](/intl.en-US/Policy Management/Custom policies/Modify a custom policy.md).

        **Note:** Make sure that you have granted Container Registry-related permissions permissions to the RAM role of Account B.

        ```
        ```
        {
        "Action": [
         "cr:GetAuthorizationToken",
         "cr:ListInstanceEndpoint",
         "cr:PullRepository"
        ],
        "Resource": [
        "*"
        ],
        "Effect": "Allow"
        }
        
        ```
        ```

        The following figure shows where the content is added.

        ![The RAM role is granted the Container Registry-related permissions](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9175359951/p99793.png)

2.  Modify the trust policy of the RAM role of Account B. This way, Account B allows the worker role of the ACK cluster created by Account A to assume the RAM role of Account B.

    1.  Obtain the ARN of the worker role of the ACK cluster created by Account A.

        For more information, see [How do I find the ARN of a RAM role?](/intl.en-US/FAQ/FAQ about RAM roles and STS tokens.md).

    2.  Modify the trust policy of the RAM role of Account B.

        1.  Log on to the [RAM console](https://ram.console.aliyun.com/). In the left-side navigation pane, click **RAM Roles**. On the RAM Roles page, find the RAM role of Account B and click the role name.
        2.  On the details page of the RAM role, click the **Trust Policy Management** tab, and add the ARN of the worker role to the Principal field of the trust policy.

            ![RamRoleARN](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9175359951/p95493.png)

3.  Check whether the worker role of Account A has the AssumeRole permission.

    ![AssumeRole permission](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9175359951/p95494.png)

    For more information, see [View the basic information about a policy](/intl.en-US/Policy Management/View the basic information about a policy.md).

4.  Add the assumeRoleARN field to the configuration file of aliyun-acr-credential-helper.

    Set the value of the assumeRoleARN field to the ARN of the RAM role for Account B. For more information about how to obtain the ARN information, see [How do I find the ARN of a RAM role?](/intl.en-US/FAQ/FAQ about RAM roles and STS tokens.md). The following YAML file shows the sample configuration. For more information about how to configure aliyun-acr-credential-helper, see [Update and configure aliyun-acr-credential-helper](#step_85j_3yv_akc).

    ```
    data:
        service-account: "default"
        watch-namespace: "all"
        expiring-threshold: "15m"
        notify-email: "cs@aliyuncs.com"
        acr-registry-info: |
          - instanceId: ""
            regionId: cn-beijing
            domains: registry.cn-beijing.aliyuncs.com
            assumeRoleARN: acs:ram::.*:role/kubernetesworkerrole-test
    ```


**Pull images across accounts by using the AccessKey information of a RAM user**

1.  Create a RAM user for your account and grant the RAM user the permissions to pull images from Container Registry instances within your account.

    For more information about how to grant the permissions to pull images from Container Registry instances, see [Step 1](#step_5b4_2v8_wr8) in the "Pull images across accounts by assuming a RAM role" section.

2.  Configure the acr-configuration ConfigMap in the **kube-system** namespace. Specify the AccessKey ID and AccessKey secret of the created RAM user.

    This way, aliyun-acr-credential-helper can use the RAM user to pull private images. For more information about how to obtain the AccessKey information, see [View the basic information about AccessKey pairs](/intl.en-US/Security Settings/AccessKey pairs/View the basic information about AccessKey pairs.md).

    The following code shows the sample configuration. For more information, see [Update and configure aliyun-acr-credential-helper](#step_85j_3yv_akc).

    ```
    data:
        service-account: "default"
        watch-namespace: "all"
        expiring-threshold: "15m"
        notify-email: "cs@aliyuncs.com"
        acr-registry-info: |
          - instanceId: ""
            customAccessKey: "xxxxx" // Enter the AccessKey ID of the created RAM user. 
            customAccessKeySecret: "xxxxxx" // Enter the AccessKey secret of the created RAM user. 
    ```


