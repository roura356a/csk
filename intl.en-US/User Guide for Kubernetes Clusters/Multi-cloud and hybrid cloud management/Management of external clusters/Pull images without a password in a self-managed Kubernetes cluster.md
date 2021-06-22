---
keyword: [self-managed Kubernetes cluster, component for password-free image pulls, pull container images]
---

# Pull images without a password in a self-managed Kubernetes cluster

You can use the aliyun-acr-credential-helper component to pull private images without a password from instances of Container Registry Personal Edition and Enterprise Edition in a self-managed Kubernetes cluster. This topic describes how to use aliyun-acr-credential-helper to pull a private image without a password in two scenarios.

-   A cluster registration proxy is created and a self-managed Kubernetes cluster is connected to the cluster registration proxy. For more information, see [Create a cluster registration proxy and register an on-premises cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of external clusters/Create a cluster registration proxy and register an on-premises cluster.md).
-   A kubectl client is connected to the self-managed cluster. For more information, see [Connect to Kubernetes clusters by using kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to Kubernetes clusters by using kubectl.md).

## Limits

aliyun-acr-credential-helper supports the following images and clusters:

-   Supported images
-   Supported clusters
    -   You can use the component to pull images without a password for clusters that contain multiple namespaces.
    -   The Kubernetes version of the cluster must be 1.11.2 or later. Otherwise, you must upgrade the Kubernetes version of the cluster. For more information, see [Upgrade a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Upgrade cluster/Upgrade a cluster.md).

## Precautions

-   To use aliyun-acr-credential-helper, do not manually specify the imagePullSecret field. If the imagePullSecret field is specified in the template of a Kubernetes resource, such as a Deployment, the component becomes invalid.
-   If a Kubernetes resource, such as a Deployment, uses custom service accounts, you must modify the service-account field in the configuration file of the component. Then, the component is authorized to pull images with the custom service accounts.
-   After you create a service account in a cluster, it takes some time for aliyun-acr-credential-helper to renew the token of the service account. The new token for pulling private images is generated based on the default permissions of the ACK cluster. Applications with the service account can use the token to pull images only after the token is renewed. If you create an application immediately after you create a service account, the application will fail to pull images because it is unauthorized.
-   By default, the configuration of aliyun-acr-credential-helper overwrites the imagePullSecret field of default service accounts in all namespaces. These service accounts are automatically modified when the service-account field of the **acr-configuration** ConfigMap in the kube-system namespace is changed.
-   When you modify the **acr-configuration** ConfigMap in the kube-system namespace, make sure that you use the same indentation as the example in this topic. We recommend that you paste the YAML code provided in this topic to the editor, replace the corresponding values, and apply the configuration. This ensures that the format of the ConfigMap is valid.

## Configure the aliyun-acr-credential-helper component in the self-managed cluster

**Step 1: Configure RAM permissions for the component**

Before you can install the component in an external cluster, you must set the AccessKey pair to grant the external cluster the permissions to access Alibaba Cloud resources. Before you set the AccessKey pair, create a Resource Access Management \(RAM\) user and grant the RAM user the permissions to access Alibaba Cloud resources.

1.  Create a RAM user. For more information, see [Create a RAM user](/intl.en-US/RAM User Management/Basic operations/Create a RAM user.md).

2.  Create a permission policy. For more information, see [Create a custom policy](/intl.en-US/Policy Management/Custom policies/Create a custom policy.md).

    Use the following template to create a custom permission policy:

    ```
    {
        "Version": "1",
        "Statement": [
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
        ]
    }
    ```

3.  Grant permissions to the RAM user. For more information, see [Grant permissions to a RAM user](/intl.en-US/RAM User Management/Authorization management/Grant permissions to a RAM user.md).

4.  Create an AccessKey pair for the RAM user. For more information, see [Obtain an AccessKey pair]().

5.  Use the AccessKey pair to create a Secret named alibaba-addon-secret in the self-managed cluster.

    The system automatically uses the AccessKey pair to access cloud resources when you install the aliyun-acr-credential-helper component.

    ```
    kubectl -n kube-system create secret generic alibaba-addon-secret --from-literal='access-key-id=<your access key id>' --from-literal='access-key-secret=<your access key secret>'
    ```

    **Note:** Replace `<your access key id>` and `<your access key secret>` with the AccessKey pair that you obtained.


**Step 2: Upgrade and configure the component**

Before you can use the component, you may need to upgrade and configure the component.

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


## Scenario 1: Pull private images from instances of Container Registry Personal Edition and Enterprise Edition

ACK allows you to pull private images concurrently from Container Registry Enterprise Edition and Personal Edition, only from Container Registry Enterprise Edition, or only from Container Registry Personal Edition. Modify the **acr-configuration** ConfigMap based on your business requirements. For more information, see [Configure the component](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of external clusters/Pull images without a password in a self-managed Kubernetes cluster.md). The following code block shows a sample configuration:

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


## Scenario 2: Pull images across regions

If you want to pull images from Container Registry instances that are deployed in different regions, you must modify the **acr-configuration** ConfigMap.

For example, you want to pull images from Container Registry instances that are deployed in the China \(Beijing\) and China \(Hangzhou\) regions at the same time. In this case, modify acr-configuration as shown in the following code block. For more information, see [Configure the component](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of external clusters/Pull images without a password in a self-managed Kubernetes cluster.md).

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

