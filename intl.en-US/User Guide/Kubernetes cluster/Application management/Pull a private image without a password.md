# Pull a private image without a password {#task_qgt_5zk_ngb .task}

This topic describes how to pull a private image without a password from the Alibaba Cloud container image repository.

You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).

Function overview

-   You can only pull a private image from an Alibaba Cloud container image repository that belongs to your account.
-   You can pull a private image from a cross-region Alibaba Cloud container image repository.
-   You can only perform this operation in the default namespace.
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

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/115357/155007474037782_en-US.png)

    **Note:** 

    This topic uses the latest version of the RAM console.

    If you use an earlier version of the RAM console, you can modify the target policy document by using either of the following two methods:

    **Method 1**

    1.  In the left-side navigation pane, click **Roles**, and then enter the **Worker RAM Role** name in the **Role Name** box. Click the target **Role Name**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/115357/155007474037792_en-US.png)

    2.  In the **Basic Information** area, click **Edit Basic Information** in the upper-right corner.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/115357/155007474037793_en-US.png)

    **Method 2**

    In the lower-right corner of the RAM dashboard page, click **New Version** to switch to the latest version of the RAM console. In the Container Service console, click **Worker RAM Role** to log on to the RAM console.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/115357/155007474137796_en-US.png)

5.  On the RAM Roles page, click the policy name in the Permission area to view the policy details. 
6.  On the Policies page, click **Modify Policy Document** in the Policy Document area. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/115357/155007474137815_en-US.png)

7.  In the Policy Document area, add the following fields and then click **OK**. 

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

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/115357/155007474137816_en-US.png)

8.  Create the aliyun-acr-credential-helper service to refresh the temporary token of Container Registry at intervals. 

    ```
    apiVersion: v1
    kind: ServiceAccount
    metadata:
         name: aliyun-acr-credential-helper
         namespace: kube-system
     ---
    apiVersion: rbac.authorization.k8s.io/v1beta1
    kind: ClusterRoleBinding
    metadata:
         name: aliyun-acr-credential-helper-rolebinding
         namespace: kube-system
    roleRef:
         apiGroup: rbac.authorization.k8s.io
         kind: ClusterRole
         name: cluster-admin
    subjects:
         - kind: ServiceAccount
           name: aliyun-acr-credential-helper
           namespace: kube-system
     ---
     #kubectl create secret docker-registry acr-image-pull-secret-public --docker-server=cr-tmp-xxx --docker-username=cr-temp-xxx --docker-password=cr-temp-xxx --docker-email=cr-temp-xxx
    apiVersion: v1
    data:
         .dockerconfigjson: eyJhdXRocyI6eyJjci10bXAteHh4Ijp7InVzZXJuYW1lIjoiY3ItdGVtcC14eHgiLCJwYXNzd29yZCI6ImNyLXRlbXAteHh4IiwiZW1haWwiOiJjci10ZW1wLXh4eCIsImF1dGgiOiJZM0l0ZEdWdGNDMTRlSGc2WTNJdGRHVnRjQzE0ZUhnPSJ9fX0=
    kind: Secret
    metadata:
         name: aliyun-acr-credential-a
         namespace: default
    type: kubernetes.io/dockerconfigjson
     ---
     #kubectl create secret docker-registry acr-image-pull-secret-vpc --docker-server=cr-tmp-xxx --docker-username=cr-temp-xxx --docker-password=cr-temp-xxx --docker-email=cr-temp-xxx
    apiVersion: v1
    data:
         .dockerconfigjson: eyJhdXRocyI6eyJjci10bXAteHh4Ijp7InVzZXJuYW1lIjoiY3ItdGVtcC14eHgiLCJwYXNzd29yZCI6ImNyLXRlbXAteHh4IiwiZW1haWwiOiJjci10ZW1wLXh4eCIsImF1dGgiOiJZM0l0ZEdWdGNDMTRlSGc2WTNJdGRHVnRjQzE0ZUhnPSJ9fX0=
    kind: Secret
    metadata:
         name: aliyun-acr-credential-b
         namespace: default
    type: kubernetes.io/dockerconfigjson
     ---
    apiVersion: apps/v1beta2
    kind: Deployment
    metadata:
         name: aliyun-acr-credential-helper
         namespace: kube-system
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
             serviceAccount: aliyun-acr-credential-helper
             containers:
             - name: aliyun-acr-credential-helper
               image: registry.cn-shanghai.aliyuncs.com/acs/aliyun-acr-credential-helper:v18.10.29.0-1a28f02-aliyun
               imagePullPolicy: Always
             terminationGracePeriodSeconds: 0
    ```


