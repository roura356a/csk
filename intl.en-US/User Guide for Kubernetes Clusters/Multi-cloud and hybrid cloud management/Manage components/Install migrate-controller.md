---
keyword: [migrate-controller, install and use]
---

# Install migrate-controller

The migrate-controller component is developed by Alibaba Cloud based on the open source project Velero. You can use this component to migrate Kubernetes applications. This topic describes how to install and use migrate-controller.

Velero is a cloud native tool that can be used to back up, restore, and migrate cluster applications. Velero is written in Golang. This tool ensures data security when you use it to back up, restore, and migrate applications and persistent volumes \(PVs\) in ACK clusters. For more information about source code of the Velero project, see [velero](https://github.com/vmware-tanzu/velero). For more information about the source code of the Velero plug-in for Alibaba Cloud, see [velero-plugin](https://github.com/AliyunContainerService/velero-plugin).

**Note:** Velero requires Kubernetes version later than V1.10.

## Step 1: Make preparations

Before you install and use migrate-controller, perform the following steps:

1.  Create a Resource Access Management \(RAM\) user and create an AccessKey pair for the RAM user.

    Before you can install the component in a registered cluster, you must set the AccessKey information to grant the registered cluster the permissions to access Alibaba Cloud resources. Before you set the AccessKey information, create a RAM user and grant the RAM user the permissions to access Alibaba Cloud resources.

    1.  Create a RAM user.

        For more information, see [Create a RAM user](/intl.en-US/RAM User Management/Create a RAM user.md).

    2.  Create a permission policy.

        For more information, see [Create a custom policy](/intl.en-US/Policy Management/Custom policies/Create a custom policy.md).

        The following code block shows the content of the permission policy for Velero:

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

        If you want to grant the RAM user only the read and write permissions on the mybackups resource in an Object Storage Service \(OSS\) bucket, create a permission policy based on the following content:

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

        For information about how to regulate finer-grained access to an OSS bucket, see [Use RAM to manage OSS permissions](/intl.en-US/Tutorials/Use RAM to manage OSS permissions.md).

    3.  Grant permissions to the RAM user.

        For more information, see [Grant permissions to a RAM user](/intl.en-US/RAM User Management/Grant permissions to a RAM user.md).

    4.  Create an AccessKey pair for the RAM user.

        For more information, see [Obtain an AccessKey pair]().

2.  Create a Secret in the registered cluster.

    To ensure that the AccessKey information is used only within the registered cluster, you must first use the AccessKey pair to deploy a Secret named alibaba-addon-secret in the cluster. This reduces the risk of information leakage. Like the Velero open source community, Container Service for Kubernetes \(ACK\) installs migrate-controller in the velero namespace. If the namespace does not exist, you must first create a namespace and name it velero. Then, use the AccessKey pair to create the alibaba-addon-secret Secret in the namespace. Command:

    ```
    kubectl create ns velero
    kubectl -n velero create secret generic alibaba-addon-secret --from-literal='access-key-id=<your AccessKey ID>' --from-literal='access-key-secret=<your AccessKey Secret>'
    ```

    **Note:**

    Replace `<your AccessKey ID>` and `<your AccessKey Secret>` in the preceding command with the AccessKey ID and AccessKey secret [obtained in Step 1](#substep_onn_mg9_lx0).

3.  Install the Velero client in the registered cluster.

    Velero provides client-side binary files that enable you to submit backup and recovery tasks to the Velero server in an easy and efficient manner.

    You must install the Velero client on a machine that allows you to access your registered cluster by using the kubeconfig file. [Download the Velero client](https://github.com/vmware-tanzu/velero/releases) based on the operating system of your machine.


After you complete the preceding steps, perform the following operations to install and use migrate-controller \(the Velero server\).

## Step 2: Install migrate-controller

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and choose **More** \> **Manage System Components** in the **Actions** column.

4.  On the **Add-ons** page, find the **migrate-controller** component and click **Install**.


## Step 3: Configure an OSS bucket

You must create a resource of the BackupStorageLocation type in an OSS bucket. The resource is used to specify the storage location for Velero backups.

1.  Create a file named BackupStorageLocation.yaml and copy the following content to the file:

    ```
    apiVersion: velero.io/v1
    kind: BackupStorageLocation
    metadata:
      labels:
        component: velero
      name: default
      namespace: velero
    spec:
      config:
        region: cn-hangzhou
      objectStorage:
        bucket: velero-bucket
      provider: alibabacloud
    ```

    Parameter descriptions:

    **Note:**

    -   Set `spec.config.region` to the region where the OSS bucket is created.
    -   Set `spec.objectStorage.bucket` to the name of the OSS bucket.
    -   If data is transmitted to an internal OSS endpoint, add `spec.config.network: internal` to the file. This way, ACK transmits data over the Internet by default.
    -   To use a specified subdirectory of the OSS bucket, add `spe.objectStorage.prefix: <your prefix>` to the file. This way, ACK uses the root directory of the OSS bucket by default.
2.  Run the following command to create a BackupStorageLocation resource:

    ```
    kubectl apply -f BackupStorageLocation.yaml
    ```


**Related topics**  


[migrate-controller](/intl.en-US/Release notes/System Component change Records/Other SQL statements/migrate-controller.md)

[Use migrate-controller to back up and restore applications](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Manage components/Use migrate-controller to back up and restore applications.md)

[Use migrate-controller to migrate applications across clusters](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Manage components/Use migrate-controller to migrate applications across clusters.md)

