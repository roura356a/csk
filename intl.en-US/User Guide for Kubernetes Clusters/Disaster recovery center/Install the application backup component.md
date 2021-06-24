---
keyword: [application backup, backup service]
---

# Install the application backup component

Container Service for Kubernetes \(ACK\) allows you to back up and restore stateful applications deployed in an ACK cluster. This is an all-in-one solution to achieve crash consistency, application consistency, and cross-region disaster recovery for stateful applications in ACK clusters. You can install the application backup component in ACK clusters and then use the component to back up and restore applications. This topic describes how to install the application backup component.

-   The application backup feature is in public preview. To use this feature, you must [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to apply to be added to the whitelist.
-   A Kubernetes cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md), [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md), or [Create a cluster registration proxy and register an on-premises cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of external clusters/Create a cluster registration proxy and register an on-premises cluster.md).

    **Note:** The Kubernetes version of your cluster must be later than 1.10. Otherwise, the feature cannot function as expected.

-   [Connect to an ACK cluster by using kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to Kubernetes clusters by using kubectl.md)

A growing number of applications are running on Kubernetes. Therefore, it is important to back up applications periodically. You can use backups to restore applications that cannot be recovered after the applications are disrupted for a long period of time. Traditional backup solutions include single-server backups and disk backups. Compared with the traditional backup solutions, application backups allow you to back up applications and the relevant data, resource objects, configurations, and namespaces.

## Step 1: Grant OSS permissions to the cluster

The application backup feature can store application backups only in Object Storage Service \(OSS\). Before you use OSS, you must grant OSS permissions to your cluster.

**Grant OSS permissions to managed or dedicated Kubernetes clusters**

If you use a managed or dedicated Kubernetes cluster, you must grant the required OSS permissions to the cluster.

1.  Create a custom permission policy that is used to access OSS. For more information, see [Create a custom policy](/intl.en-US/Policy Management/Custom policies/Create a custom policy.md).

    **Note:** For more information about how to configure fine-grained access to OSS, see [Use RAM to manage OSS permissions](/intl.en-US/Tutorials/Use RAM to manage OSS permissions.md).

    To grant full OSS permissions, create a permission policy based on the following template:

    ```
    {
        "Version": "1",
        "Statement": [
            {
                "Action": [
                    "oss:PutObject",
                    "oss:GetObject",
                    "oss:DeleteObject",
                    "oss:GetBucket",
                    "oss:ListObjects",
                    "oss:ListBuckets"
                ],
                "Resource": [
                    "*"
                ],
                "Effect": "Allow"
            }
        ]
    }
    ```

    To grant only read and write permissions on a specified OSS bucket, create a permission policy based on the following template:

    ```
    {
        "Version": "1",
        "Statement": [
            {
                "Action": [
                    "oss:PutObject",
                    "oss:GetObject",
                    "oss:DeleteObject",
                    "oss:GetBucket",
                    "oss:ListObjects",
                    "oss:ListBuckets"
                ],
                "Resource": [
                    "acs:oss:*:*:mybackups",
                    "acs:oss:*:*:mybackups/*"
                ],
                "Effect": "Allow"
            }
        ]
    }
    ```

    Replace `mybackups` with the name of the OSS bucket that you want to use.

2.  Grant permissions to the Resource Access Management \(RAM\) role of the managed Kubernetes cluster.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  On the details page of the cluster, click the **Cluster Resources** tab and view the **worker RAM role** of the cluster.

    5.  Log on to the [RAM console](https://ram.console.aliyun.com/).

    6.  In the left-side navigation pane, click **Grants**.

    7.  On the Grants page, click **Grant Permission**. In the **Add Permissions** panel, set the parameters and click **OK**.

        |Parameter|Description|
        |---------|-----------|
        |Authorized Scope|Valid values: **Alibaba Cloud Account** and **Specific Resource Group**.|
        |Principal|Enter the worker RAM role that you obtained.|
        |Select Policy|Click **Custom Policy**, enter the name of the permission policy that is created in [Step 1](#step_5lw_hv6_b8p), and then click the name of the policy.|


**Grant OSS permissions to a registered Kubernetes cluster**

If your applications are deployed in a registered Kubernetes cluster, you must create a RAM user for the cluster, grant the RAM user the permissions to access cloud resources, and then create an AccessKey pair for the RAM user.

1.  Create a RAM user. For more information, see [Create a RAM user](/intl.en-US/RAM User Management/Basic operations/Create a RAM user.md).

2.  Create a custom permission policy that is used to access OSS. For more information, see [Step 1](#step_5lw_hv6_b8p).

3.  Grant permissions to the RAM user. For more information, see [Grant permissions to a RAM user](/intl.en-US/RAM User Management/Authorization management/Grant permissions to a RAM user.md).

4.  Create an AccessKey pair for the RAM user. For more information, see [Obtain an AccessKey pair]().

5.  Create a Secret in the registered Kubernetes cluster.

    To ensure that the AccessKey pair is used only within the registered cluster, you must use the AccessKey pair to create a Secret named **alibaba-addon-secret** in the cluster. This reduces the risk of information leakage.

    ACK installs migrate-controller in the **velero** namespace, which is inherited from the open source Velero project. If the **velero** namespace does not exist, you must create a namespace named velero. After you create the namespace, use the AccessKey pair to create a Secret named alibaba-addon-secret in the namespace.

    1.  Run the following command to create a namespace named **velero**:

        ```
        kubectl create ns velero
        ```

    2.  Run the following command to create a Secret named alibaba-addon-secret:

        ```
        kubectl -n velero create secret generic alibaba-addon-secret --from-literal='access-key-id=<your AccessKey ID>' --from-literal='access-key-secret=<your AccessKey Secret>'
        ```

        Replace your AccessKey ID and your AccessKey Secret with the AccessKey ID and AccessKey secret that are obtained in [Substep 4](#step_2ec_27e_p4q).


## Step 2: Install the application backup component

**Note:** Before you can use the application backup feature, you must install the application backup component. If the application backup component is already installed, skip this step.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Application Backup \(Public Preview\)**.

5.  On the Application Backup page, click **Install**.

    **Note:** If the velero namespace does not exist, the system automatically creates a namespace named velero when the system installs the component. Do not delete this namespace when you back up applications.

    After the component is installed, the page in the following figure appears.

    ![The application backup component](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2337284161/p214169.png)


**Related topics**  


[Back up and restore applications](/intl.en-US/User Guide for Kubernetes Clusters/Disaster recovery center/Use migrate-controller to back up and restore applications.md)

[Migrate applications across clusters](/intl.en-US/User Guide for Kubernetes Clusters/Disaster recovery center/Use migrate-controller to migrate applications across clusters.md)

