---
keyword: [Container Registry, pull container images without a password, Kubernetes]
---

# Use aliyun-acr-credential-helper to pull images without a password

Container Registry provides the aliyun-acr-credential-helper component for you to pull private images without a password from Container Registry Enterprise Edition or Personal Edition. This component is automatically installed in Container Service for Kubernetes \(ACK\) clusters. This topic describes how to use aliyun-acr-credential-helper to pull a private image without a password in different scenarios.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md)

**Note:**

-   To use aliyun-acr-credential-helper, do not manually specify the imagePullSecret field. If the imagePullSecret field is specified in the template of a Kubernetes resource, such as a Deployment, the component becomes invalid.
-   If a Kubernetes resource, such as a Deployment, uses custom service accounts, you must modify the service-account field in the configuration file of the component. Then, the component is authorized to pull images with the custom service accounts.
-   Check whether the private image that you want to pull is in the region where your ACK cluster is deployed. By default, you can pull private images from only Container Registry instances that are deployed in the region where your ACK cluster is deployed. If you want to pull images from Container Registry instances that are deployed in different regions, see Scenario 3 in this topic.
-   If you want to assume a custom Resource Access Management \(RAM\) role to pull private images, you must specify the AccessKey ID and AccessKey secret of the RAM role in the acr-configuration ConfigMap. However, this may disclose the AccessKey information. To ensure data security, make sure that the RAM role is granted only the permissions to pull images.
-   After you create a service account in a cluster, it takes some time for aliyun-acr-credential-helper to renew the token of the service account. The new token for pulling private images is generated based on the default permissions of the ACK cluster. Applications with the service account can use the token to pull images only after the token is renewed. If you create an application immediately after you create a service account, the application will fail to pull images because it is unauthorized.
-   By default, the configuration of aliyun-acr-credential-helper overwrites the imagePullSecret field of default service accounts in all namespaces. These service accounts are automatically modified when the service-account field of the **acr-configuration** ConfigMap in the kube-system namespace is changed.
-   When you modify the **acr-configuration** ConfigMap in the kube-system namespace, make sure that you use the same indentation as the example in this topic. We recommend that you paste the YAML code provided in this topic to the editor, replace the corresponding values, and apply the configuration. This ensures that the format of the ConfigMap is valid.

aliyun-acr-credential-helper reads the required information from the acr-configuration ConfigMap that is created in the kube-system namespace and then pulls private images. You can pull private images by using the following methods:

-   Assume the worker role of an ACK cluster to pull private images from Container Registry instances that are created within your account.
-   Use the AccessKey ID and AccessKey secret of a custom RAM role to pull private images from Container Registry instances that are created within your account.
-   Use AssumeRole to assume the RAM role of another account to pull private images from that account.

aliyun-acr-credential-helper supports the following images and clusters:

-   Supported images
    -   You can use aliyun-acr-credential-helper to pull private images from instances of Container Registry Enterprise Edition and Personal Edition.
    -   You can use the component to pull private images from your Container Registry instances. You can also pull private images from other accounts after authorization or by using the AccessKey ID and AccessKey secret.
    -   You can use the component to pull private images from Container Registry instances that are deployed in different regions.
-   Supported clusters
    -   You can use the component to pull images without a password from clusters in multiple namespaces.
    -   Supported cluster types:
        -   Dedicated Kubernetes clusters.
        -   Managed Kubernetes clusters.
    -   Supported cluster versions:
        -   Dedicated Kubernetes clusters: The Kubernetes version must be V1.11.2 or later. If the Kubernetes version is earlier than V1.11.2, you must manually upgrade Kubernetes to V1.11.2 or later. For more information, see [Upgrade a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Upgrade cluster/Upgrade a cluster.md).
        -   Managed Kubernetes clusters: All versions.

## Upgrade and configure the component

Before you use the aliyun-acr-credential-helper component to pull images, you must perform the following steps:

1.  Upgrade the aliyun-acr-credential-helper component.

    1.  Log on to the [the ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the **Clusters** page, find the cluster that you want to manage and choose **More** \> **Manage System Components** in the Actions column.

    4.  On the page that appears, find **aliyun-acr-credential-helper** in the **Security** section and click **Upgrade**.

2.  Configure acr-configuration.

    **Configure acr-configuration in the ACK console.**

    1.  Log on to the [the ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the **Clusters** page, find the cluster that you want to manage and click **Details** in the Actions column.

    4.  In the left-side navigation pane of the **details** page, choose **Configurations** \> **ConfigMaps**.

    5.  At the top of the **ConfigMap** page, select **kube-system** from the Namespace drop-down list. Then, find and configure **acr-configuration** by using one of the following methods:

        -   Method 1: Click **Edit** on the right side of acr-configuration, and enter keys and values in the **ConfigMap**.

            If **acr-configuration** is not found in the list of ConfigMaps, see [Create a ConfigMap](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Configuration items and key/Manage ConfigMaps.md). For more information about how to update a ConfigMap, see [Modify a ConfigMap](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Configuration items and key/Manage ConfigMaps.md).

        -   Method 2: Click **Edit YAML** on the right side of acr-configuration, and enter keys and values in the **ConfigMap**.
        The following table describes the keys and values of the acr-configuration ConfigMap.

        |Key|Description|Value|
        |---|-----------|-----|
        |service-account|The service accounts that are used by the component to pull images.|Default value: **Default**. **Note:** Separate multiple service accounts with commas \(,\). Enter an asterisk \(\*\) to specify all service accounts in the specified namespace. |
        |acr-registry-info|The information about Container Registry instances. Each instance can be specified by three string type fields in a YAML file. **Note:** Set the three fields based on the following descriptions:

        -   instanceId: the ID of the Container Registry instance. This field is required for instances of Container Registry Enterprise Edition.
        -   regionId: the ID of the region where the Container Registry instance is deployed. This field is optional. The default value is the region where your ACK cluster is deployed.
        -   domains: the domain names of the Container Registry instance. This field is optional. By default, all domain names of the instance are specified. Separate multiple domain names with commas \(,\).
|By default, this parameter is not specified. This means that images are pulled from the default repository of the Container Registry instance that is deployed in the region where the ACK cluster is deployed. The following template shows the configuration of an instance of Container Registry Enterprise Edition:

        ```
- instanceId: cri-xxx
  regionId: cn-hangzhou
  domains: xxx.com,yyy.com
        ```

The following template shows the configuration of an instance of Container Registry Personal Edition:

        ```
- instanceId: ""
  regionId: cn-hangzhou
  domains: xxx.com,yyy.com
        ``` |
        |watch-namespace|The namespaces to which the images to be pulled belong.|Default value: **Default**. **Note:** If the value is set to all, images are pulled from all namespaces without a password. Separate multiple namespaces with commas \(,\). |
        |expiring-threshold|The duration after which the cache token expires.|Default value: **15m**. We recommend that you use the default value.|

    **Configure acr-configuration by using kubectl**

    1.  Run the following command to open the ConfigMap of acr-configuration:

        ```
        kubectl edit cm acr-configuration -n kube-system
        ```

    2.  Configure the parameters of acr-configuration based on your requirements.

        The following templates show the configurations of acr-configuration for instances of Container Registry Enterprise Edition and Personal Edition:

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

        -   Personal Edition

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

ACK allows you to pull private images from Container Registry Enterprise Edition, Container Registry Personal Edition, or both Container Registry Enterprise Edition and Personal Edition. Modify the **acr-configuration** ConfigMap to meet your requirements. For more information about how to configure the component, see [Configure aliyun-acr-credential-helper](#step_85j_3yv_akc). Sample configurations:

-   Pull private images from Container Registry Enterprise Edition.

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

-   Pull private images from Container Registry Personal Edition.

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


## Scenario 2: Pull images with the current account

If you want to pull images with the current account, you must check whether the current account has the permissions to pull images from Container Registry instances.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the **details** page of the cluster, click the **Cluster Resources** tab. On the Cluster Resources tab, click the hyperlink next to **Worker RAM role**.

5.  On the page that appears, click the **Permissions** tab, and click the name of the policy that you want to manage in the **Policy** column.

6.  Click **Modify Policy Document**.

7.  In the **Policy Document** section, add the following content and click **OK**:

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


**Note:** If you fail to pull images without a password with the current account, you must check whether the image specified in the pod exists in the Container Registry repository.

-   The image exists in the Container Registry repository but is not found on the Container Registry instances that are created by the current account. In this case, you must pull the image across accounts. For more information, see Scenario 4.
-   The image exists in the Container Registry repository and is found on the Container Registry instances that are created by the current account. In this case, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).
-   The image is not found in the Container Registry repository. In this case, you must check whether the image can be found on Container Registry. If the image is not found on Container Registry, you must upload the image to Container Registry or make the image publicly available over the Internet.

## Scenario 3: Pull images across regions

If you want to pull images from Container Registry instances that are deployed in different regions, you must modify the **acr-configuration** ConfigMap.

For example, you want to pull images from Container Registry instances that are deployed in the China \(Beijing\) and China \(Hangzhou\) regions at a time. In this case, modify acr-configuration as shown in the following code block. For more information about how to configure the component, see [Configure aliyun-acr-credential-helper](#step_85j_3yv_akc).

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

In this scenario, you can pull images from a different account by using the following methods:

-   **Pull images from a different account by role assuming**: Account A assumes the RAM role of Account B to pull private images from Account B.
-   **Pull images from a different account by using the AccessKey pair of the account**: The account whose RAM role you want to assume must have the permissions to pull images.

**Pull images from a different account by role assuming**

**Note:**

Follow these rules when you pull images from a different account:

1.  The RAM role of Account B is authorized to pull private images from a specified private repository. This rule requires you to grant the **cr.\*** permission \(full permissions on Container Registry\) to the RAM role of Account B.
2.  Account B allows the worker role of the ACK cluster created by Account A to assume the RAM role of Account B. This rule requires you to modify the trust policy of the RAM role of Account B.
3.  The worker role of the ACK cluster created by Account A has the permissions to assume the RAM role of Account B. This rule requires you to attach the AliyunAssumeRoleAccess permission policy to the worker role of Account A.
4.  Set the worker role of Account A to assume the RAM role of Account B. This rule requires you to specify the assumeRoleARN field in the acr-configuration ConfigMap.

1.  Create a RAM role of Account B. Specify **Alibaba Cloud accounts** as the trusted entity of the RAM role. Make sure that the created RAM role has the permissions to pull private images from Account B.

    1.  Create a RAM role.

        For more information, see [Create a RAM role for a trusted Alibaba Cloud account](/intl.en-US/RAM Role Management/Create a RAM role/Create a RAM role for a trusted Alibaba Cloud account.md).

    2.  Customize the permissions of the created RAM role and make sure that the created RAM role has the permissions to pull private images from Account B.

        For more information, see [Modify a custom policy](/intl.en-US/Policy Management/Custom policies/Modify a custom policy.md).

        **Note:** Make sure that you have granted the **cr.\*** permission to the RAM role of Account B.

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

        ![The RAM role is granted full permissions on Container Registry](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9175359951/p99793.png)

2.  Modify the trust policy of the RAM role of Account B. This way, Account B allows the worker role of the ACK cluster created by Account A to assume the RAM role of Account B.

    1.  Obtain the Alibaba Cloud Resource Name \(ARN\) of the worker role of the ACK cluster created by Account A.

        For more information, see [How do I find the ARN of a RAM role?](/intl.en-US/FAQ/FAQ about RAM roles and STS tokens.md)

    2.  Modify the trust policy of the RAM role of Account B.

        1.  Log on to the [RAM console](https://ram.console.aliyun.com/). In the left-side navigation pane, choose **RAM Roles**. On the RAM Roles page, find and click the RAM role of Account B.
        2.  On the details page of the RAM role of Account B, click the **Trust Policy Management** tab, and enter the ARN of the worker role into the Principal field of the trust policy.

            ![RamRoleARN](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9175359951/p95493.png)

3.  Check whether the worker role of Account A has the AssumeRole permission.

    ![AssumeRole permission](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9175359951/p95494.png)

    For more information, see [View the basic information about a policy](/intl.en-US/Policy Management/View the basic information about a policy.md).

4.  Add the assumeRoleARN field to the ConfigMap of the aliyun-acr-credential-helper component.

    Set the value of the assumeRoleARN field to the ARN of the RAM role for Account B. For more information about how to obtain the ARN information, see [How do I find the ARN of a RAM role?](/intl.en-US/FAQ/FAQ about RAM roles and STS tokens.md) The following YAML file is used as an example. For more information about how to configure the component, see the preceding [Configure aliyun-acr-credential-helper](#step_85j_3yv_akc) section.

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


**Pull images from a different account by using the AccessKey pair of the account**

1.  Create a RAM role for your account and grant the RAM role the permissions to pull images from Container Registry.

    For more information about how to authorize an account to pull images from Container Registry, see [Step 1 in Pull images from a different account by role assuming](#section_jze_opy_4i7).

2.  Configure the acr-configuration ConfigMap in the **kube-system** namespace. Specify the AccessKey ID and AccessKey secret of the created RAM role.

    This way, aliyun-acr-credential-helper can assume the RAM role to pull private images. For more information about how to obtain the AccessKey information, see [View the basic information about AccessKey pairs](/intl.en-US/Security Settings/AccessKey pairs/View the basic information about AccessKey pairs.md).

    The following code block provides an example. For more information about how to configure the component, see [Configure aliyun-acr-credential-helper](#step_85j_3yv_akc).

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


