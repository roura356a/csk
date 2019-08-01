# Pull an image without a password {#task_qgt_5zk_ngb .task}

This topic describes how to pull a private image without a password from the Alibaba Cloud container image repository.

You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](reseller.en-US//Create a Kubernetes cluster.md#).

-   You can only pull a private image from an Alibaba Cloud container image repository that belongs to your account.
-   You can pull a private image from a cross-region Alibaba Cloud container image repository.
-   You can only perform this operation in multiple namespaces.
-   You can pull a private image from an image repository of the Enterprise Edition of Alibaba Cloud Container Registry.
-   Kubernetes clusters that support this function include:
    -   Dedicated Kubernetes clusters
    -   Managed Kubernetes clusters
    -   Serverless Kubernetes clusters
-   The following are Kubernetes cluster versions that support this function:
    -   Dedicated Kubernetes cluster versions that are not earlier than v1.11.2 support this function by default. If the dedicated Kubernetes cluster version is earlier than v1.11.2, follow the procedures described in this topic.
    -   All versions of managed Kubernetes clusters support this function.
    -   All versions of serverless Kubernetes clusters support this function.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Kubernetes, click **Clusters**.
3.  Click the target cluster name to view the cluster details.
4.  In the Cluster Resources area, click **Worker RAM Role**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/115357/156464864737782_en-US.png)

    **Note:** 

    This topic uses the latest version of the RAM console.

    If you use an earlier version of the RAM console, you can modify the target policy document by using either of the following two methods:

    **Method 1**

    1.  In the left-side navigation pane, click **Roles**, and then enter the **Worker RAM Role** name in the **Role Name** box. Click the target **Role Name**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/115357/156464864737792_en-US.png)

    2.  In the **Basic Information** area, click **Edit Basic Information** in the upper-right corner.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/115357/156464864737793_en-US.png)

    **Method 2**

    In the lower-right corner of the RAM dashboard page, click **New Version** to switch to the latest version of the RAM console. In the Container Service console, click **Worker RAM Role** to log on to the RAM console.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/115357/156464864737796_en-US.png)

5.  On the RAM Roles page, click the policy name in the Permission area to view the policy details.
6.  On the Policies page, click **Modify Policy Document** in the Policy Document area. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/115357/156464864737815_en-US.png)

7.  In the Policy Document area, add the following fields and then click **OK**. 

    ``` {#codeblock_sho_xxs_2qb}
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

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/115357/156464864837816_en-US.png)

8.  Create the aliyun-acr-credential-helper service to refresh a temporary token of Container Registry. 

    ``` {#codeblock_uma_pxk_tpt}
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: acr-configuration
      namespace: kube-system
    data:
        #For information about configuration description, see the following table.
        acr-api-version: "2018-12-01"
        #acr-registry: "xxx-registry.*.cr.aliyuncs.com,xxx-registry-vpc.*.cr.aliyuncs.com"
        #watch-namespace: "all"
        #expiring-threshold: "15m"
    ---
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: aliyun-acr-credential-helper
      namespace: kube-system
    ---
    apiVersion: rbac.authorization.k8s.io/v1beta1
    kind: ClusterRole
    metadata:
      name: aliyun-acr-credential-helper
    rules:
      - apiGroups:
          - ""
        resources:
          - namespaces
          - configmaps
        verbs:
          - get
          - list
          - watch
      - apiGroups:
          - ""
        resources:
          - serviceaccounts
          - secrets
        verbs:
          - create
          - update
          - patch
          - get
          - list
          - watch
    ---
    apiVersion: rbac.authorization.k8s.io/v1beta1
    kind: ClusterRoleBinding
    metadata:
      name: aliyun-acr-credential-helper
    roleRef:
      apiGroup: rbac.authorization.k8s.io
      kind: ClusterRole
      name: aliyun-acr-credential-helper
    subjects:
      - kind: ServiceAccount
        name: aliyun-acr-credential-helper
        namespace: kube-system
    ---
    apiVersion: apps/v1beta2
    kind: Deployment
    metadata:
      name: aliyun-acr-credential-helper
      namespace: kube-system
      annotations:
        component.version: "v19.01.28"
        component.revision: "v1"
      labels:
        app: aliyun-acr-credential-helper
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: aliyun-acr-credential-helper
      template:
        metadata:
          labels:
            app: aliyun-acr-credential-helper
        spec:
          serviceAccountName: aliyun-acr-credential-helper
          containers:
          - name: aliyun-acr-credential-helper
            image: registry.cn-hangzhou.aliyuncs.com/acs/aliyun-acr-credential-helper:v19.01.28.0-f063330-aliyun
            imagePullPolicy: Always
            env:
              - name: POD_NAME
                valueFrom:
                  fieldRef:
                    fieldPath: metadata.name
              - name: POD_NAMESPACE
                valueFrom:
                  fieldRef:
                    fieldPath: metadata.namespace
            volumeMounts:
            - name: localtime
              mountPath: /etc/localtime
              readOnly: true
          volumes:
            - name: localtime
              hostPath:
                path: /etc/localtime
                type: File
          nodeSelector:
            beta.kubernetes.io/os: linux
    ```

    **Note:** The component Credential Helper of Alibaba Cloud Container Registry \(ACR\) is set with a ConfigMap. The settings of this component automatically take effect.

    |Setting|Description|Default value|
    |-------|-----------|-------------|
    |`acr-api-version`|The version of the ACR API. **Note:** The API of the Enterprise Edition of Alibaba Cloud Container Registry is supported.

 |2018-12-01|
    |`acr-registry`|The target registry from which you pull an image without a password.|registry.\*.aliyuncs.com,registry-vpc.\*.aliyuncs.com,xxx-registry.\*.cr.aliyuncs.com,xxx-registry-vpc.\*.cr.aliyuncs.com **Note:** To set multiple registries, use commas \(,\) to separate them.

 |
    |`watch-namespace`|The namespace that you use to pull an image without a password.|default **Note:** 

If you set this parameter as All, then all namespaces can be used to pull an image without a password.

To set multiple namespaces, use commas \(,\) to separate them.

 |
    |`expiring-threshold`|The valid period before a temporary token in your local cache expires. **Note:** A temporary token of Container Registry is located in your local cache and is automatically refreshed at intervals of this valid period.

 |15m|


