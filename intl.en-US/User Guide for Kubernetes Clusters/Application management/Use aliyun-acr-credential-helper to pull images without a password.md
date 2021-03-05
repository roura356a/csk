---
keyword: [ACR, pull container images without a password, Kubernetes]
---

# Use aliyun-acr-credential-helper to pull images without a password

Container Registry provides the aliyun-acr-credential-helper component for you to pull private images without a password from instances of Container Registry Enterprise Edition and default instances. This component is automatically installed in clusters of Container Service for Kubernetes \(ACK\). This topic describes how to use aliyun-acr-credential-helper to pull a private image without a password in different scenarios.

[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)

**Note:**

-   If you want to use aliyun-acr-credential-helper, do not manually specify the imagePullSecret parameter. If the imagePullSecret parameter is specified in the template of a Kubernetes resource \(such as a Deployment\), the component becomes invalid.
-   If a Kubernetes resource \(such as a Deployment\) uses custom service accounts, you must modify the service-account field in the configuration file of the component. Then, the component is authorized to pull images with the custom service accounts.
-   Check whether the private image that you want to pull is in the region where your ACK cluster is deployed. By default, you can pull private images from only Container Registry instances that are deployed in the region where your ACK cluster is deployed. If you want to pull images from Container Registry instances that are deployed in different regions, refer to Scenario 3 in this topic.
-   If you want to assume a custom Resource Access Management \(RAM\) role to pull private images, you must specify the AccessKey ID and AccessKey secret of the RAM role in the acr-configuration ConfigMap. However, this may disclose the AccessKey information. To ensure data security, make sure that the RAM role is granted only the permissions to pull images.

aliyun-acr-credential-helper reads the required information from the acr-configuration ConfigMap created in the kube-system namespace and then pulls private images. You can pull private images in the following methods:

-   Assume the worker role of an ACK cluster to pull images from Container Registry instances that are created under your account.
-   Use the AccessKey ID and AccessKey secret of a custom RAM role to pull private images from Container Registry instances that are created under your account.
-   Use AssumeRole to assume the RAM role of another account to pull images from that account.

aliyun-acr-credential-helper supports the following images and clusters:

-   Supported images
    -   You can use the component to pull private images from instances of Container Registry Enterprise Edition and default instances.
    -   You can use the component to pull private images from your Container Registry instances. You can also pull private images from other accounts after authorization or by using the AccessKey ID and AccessKey secret.
    -   You can use the component to pull private images from Container Registry instances that are deployed in different regions.
-   Supported clusters
    -   You can use the component to pull images without a password from clusters that contain multiple namespaces.
    -   Supported cluster types:
        -   Dedicated Kubernetes clusters.
        -   Managed Kubernetes clusters.
    -   Supported cluster versions:
        -   Dedicated Kubernetes clusters: The Kubernetes version must be V1.11.2 or later. If the Kubernetes version is earlier than V1.11.2, you must manually upgrade to V1.11.2 or later. For more information, see [Upgrade a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Upgrade cluster/Upgrade a cluster.md).
        -   Managed Kubernetes clusters: all versions.

## Upgrade and configure the component.

Before you use the aliyun-acr-credential-helper component to pull images, you must perform the following steps:

1.  Upgrade the component.

    1.  Log on to the [the ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane, click **Clusters**.

    3.  On the **Clusters** page, find your cluster and choose **More** \> **Manage System Components** in the Actions column.

    4.  On the page that appears, find **aliyun-acr-credential-helper** and click **Upgrade**.

2.  Configure the component.

    1.  Log on to the [the ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane, click **Clusters**.

    3.  On the **Clusters** page, find your cluster and click **Details** in the Actions column.

    4.  In the left-side navigation pane of the **details** page, choose **Configurations** \> **ConfigMap**.

    5.  On the **ConfigMap** tab, find the related **ConfigMap** and click **Edit** in the Actions column. On the Edit panel, you can modify the **name** of the ConfigMap.

        If the ConfigMap is not found in the list, create one. For more information, see [Create a ConfigMap](/intl.en-US/User Guide for Kubernetes Clusters/Configuration items and key management/Create a ConfigMap.md). You can also [Update a ConfigMap](/intl.en-US/User Guide for Kubernetes Clusters/Configuration items and key management/Update a ConfigMap.md).

        The following example shows how to configure acr-configuration:

        -   Instances of Container Registry Enterprise Edition:

            ```
            acr-registry-info: |
                 - instanceId: cri-xxx
                   regionId: cn-hangzhou
                   domains: xxx.com,yyy.com
               watch-namespace: "all"
               service-account: "default"
               expiring-threshold: "15m"
            ```

        -   Default instances:

            ```
            acr-registry-info: 
               watch-namespace: "all"
               service-account: "default"
               expiring-threshold: "15m"
            ```

        |Parameter|Description|Default value|
        |---------|-----------|-------------|
        |service-account|This parameter specifies the service accounts that are used by the component to pull images.|Default.**Note:** Separate multiple service accounts with commas \(,\). Enter an asterisk \(\*\) to specify all service accounts in the specified namespace. |
        |acr-registry-info|This parameter specifies the information about Container Registry instances. Each instance can be specified by three string type fields in a YAML file.**Note:** Set the three fields based on the following descriptions:

        -   instanceId: the ID of the Container Registry instance. This field is required for instances of Container Registry Enterprise Edition.
        -   regionId: the ID of the region where the Container Registry instance is deployed. This field is optional. The default value is the region where your ACK cluster is deployed.
        -   domains: the domain names of the Container Registry instance. This field is optional. By default, all domain names of the instance are specified. Separate multiple domain names with commas \(,\).
|By default, this parameter is not specified. This means that images are pulled from the default repository of the Container Registry instance that is deployed in the region where the ACK cluster is deployed.|
        |watch-namespace|The namespaces to which the images to be pulled belong.|Default.**Note:** If the value is set to all, images are pulled from all namespaces without a password. Separate multiple namespaces with commas \(,\). |
        |expiring-threshold|The duration after which the cache token expires.|The default value is 15m \(15 minutes\). We recommend that you use the default value.|


## Scenario 1: Pull images across accounts

In this scenario, you can pull images across accounts by using the following methods:

-   **Pull images across accounts by role assuming**: Account A assumes the RAM role of Account B to pull private images from Account B
-   **Pull images across accounts by using the AccessKey information of other accounts**: The RAM role of the used account must have the permissions to pull images.

**Pull images across accounts by RAM role assuming**

**Note:**

Follow these rules when you pull images across accounts:

1.  The RAM role of Account B is authorized to pull private images from a specified private repository. This rule requires you to grant all **Container Registry- related** permissions to the RAM role of Account B.
2.  Account B allows the worker role of the ACK cluster created by Account A to assume the RAM role of Account B. This rule requires you to modify the trust policy of the RAM role of Account B.
3.  The worker role of the ACK cluster created by Account A has the permissions to assume the RAM role of Account B. This rule requires you to attach the AliyunAssumeRoleAccess permission policy to the worker role of Account A.
4.  Set the worker role of Account A to assume the RAM role of Account B. This rule requires you to specify the assumeRoleARN field in the acr-configuration ConfigMap.

1.  Create a RAM role of Account B. Specify **Alibaba Cloud accounts** as the trusted entity of the RAM role. Make sure that the created RAM role has the permissions to pull private images from Account B.

    1.  Create a RAM role.

        For more information, see [Create a RAM role for a trusted Alibaba Cloud account](/intl.en-US/RAM Role Management/Create a RAM role/Create a RAM role for a trusted Alibaba Cloud account.md).

    2.  Customize the permissions of the created RAM role and make sure that the created RAM role has the permissions to pull private images from Account B

        For more information, see [Modify a custom policy](/intl.en-US/Policy Management/Custom policies/Modify a custom policy.md).

        **Note:** Make sure that you have granted all **Container Registry- related** permissions to the RAM role of Account B.

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

2.  Modify the trust policy of the RAM role of Account B. This way, Account B allows the worker role of the ACK cluster created by Account A to assume the RAM role of Account B. This requires you to enter the Alibaba Cloud Resource Name \(ARN\) of the worker role into the Principal field of the trust policy.

    1.  Obtain the ARN of the worker role of the ACK cluster created by Account A.

        For more information, see [How do I find the ARN of a RAM role?](/intl.en-US/FAQ/FAQ about RAM roles and STS tokens.md).

    2.  Modify the trust policy of the RAM role of Account B.

        1.  Go to the [RAM console](https://ram.console.aliyun.com/). In the left-side navigation pane, choose **RAM Roles**. On the RAM roles page, find and click the RAM role of Account B.
        2.  On the details page of the RAM role of Account B, click the **Trust Policy Management** tab, and enter the ARN of the worker role into the Principal field of the trust policy.

            ![RamRoleARN](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9175359951/p95493.png)

3.  Check whether the worker role of Account A has the AssumeRole permission.

    ![AssumeRole permission](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9175359951/p95494.png)

    For more information, see [View the basic information about a policy](/intl.en-US/Policy Management/View the basic information about a policy.md).

4.  Add the assumeRoleARN field to the configuration file of the aliyun-acr-credential-helper component.

    Set the value of the assumeRoleARN field to the ARN of the RAM role of Account B. For more information about how to obtain the ARN information, see [How do I find the ARN of a RAM role?](/intl.en-US/FAQ/FAQ about RAM roles and STS tokens.md). The following YAML file is an example. For more information about how to configure the component, see the preceding [Configure the component](#step_85j_3yv_akc) section.

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


**Pull images across accounts by using the AccessKey information of other accounts**

1.  Create a RAM role for your account and grant the RAM role the permissions to pull images across accounts.

    For more information about how to grant the permissions to pull images across accounts, see [Step 1](#step_5b4_2v8_wr8) in the Pull images across accounts by role assuming section.

2.  Configure the acr-configuration ConfigMap in the **kube-system** namespace. Specify the AccessKey ID and AccessKey secret of the created RAM role.

    This way, the acr-configuration component can assume the RAM role to pull private images. For more information about how to obtain the AccessKey information, see [View the basic information about AccessKey pairs](/intl.en-US/Security Settings/AccessKey pairs/View the basic information about AccessKey pairs.md).

    The following code block provides an example: For more information about how to configure the component, see the preceding [Configure the component](#step_85j_3yv_akc) section.

    ```
    data:   
        service-account: "default"
        watch-namespace: "all"
        expiring-threshold: "15m"
        notify-email: "cs@aliyuncs.com"
        acr-registry-info: |
        - instanceId: ""
          customAccessKey: "xxxxx" // Enter the AccessKey ID of the created RAM role.
          customAccessKeySecret: "xxxxxx" // Enter the AccessKey secret of the created RAM role.
    ```


## Scenario 2: Pull images under the current account

If you want to pull images under the current account, you must check whether the current account has the permissions to pull images from Container Registry instances.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the **details** page of the cluster click the **Cluster Resources** tab. On the Cluster Resources tab, click the name of the **worker RAM role**.

5.  On the page that appears, click the **Permissions** tab, and find and click the name of the attached **policy** in the Policy column.

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

    The following figure shows where the content is added.

    ![The RAM role is granted the Container Registry-related permissions](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9175359951/p99793.png)


**Note:** If you fail to pull images without a password under the current account, you need to check whether the image specified in the pod exists in the Container Registry repository.

-   If the image exists in the Container Registry repository but is not found on the Container Registry instances created by the current account, you need to pull the image across accounts. This is similar to Scenario 1.
-   If the image is not found on the Container Registry instances created by the current account, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).
-   If the image is not found in the Container Registry repository, you need to check whether the image can be found on Container Registry. If the image is found on Container Registry, you need to synchronize the external image to Container Registry or change the image status so that the image can be pulled over the Internet.

## Scenario 3: Pull images across regions

If you want to pull images from Container Registry instances that are deployed in different regions, you must modify the **acr-configuration**ConfigMap.

For example, if you want to pull images from Container Registry instances that are deployed in the China \(Beijing\) and China \(Hangzhou\) regions at the same time, modify acr-configuration as shown in the following code block. For more information about how to configure the component, see the preceding [Configure the component](#step_85j_3yv_akc) section.

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

## Scenario 4: Pull private images from instances of Container Registry Enterprise Edition and default instances

If you need to pull private images from instances of Container Registry Enterprise Edition and default instances at the same time, modify the **acr-configuration**ConfigMap as shown in the following code block. For more information about how to configure the component, see the preceding [Configure the component](#step_85j_3yv_akc) section.

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

