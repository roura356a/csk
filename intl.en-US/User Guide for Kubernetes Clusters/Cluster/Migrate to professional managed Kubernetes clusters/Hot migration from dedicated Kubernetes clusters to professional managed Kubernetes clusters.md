---
keyword: [dedicated Kubernetes cluster, Kubernetes cluster migration, professional managed Kubernetes cluster]
---

# Hot migration from dedicated Kubernetes clusters to professional managed Kubernetes clusters

Container Service for Kubernetes \(ACK\) supports hot migration from dedicated Kubernetes clusters to professional managed Kubernetes clusters. You can dynamically migrate your workloads from existing dedicated Kubernetes clusters to professional managed Kubernetes clusters. This way, you can benefit from the features that are provided by professional managed Kubernetes clusters. This topic describes how to migrate workloads from a dedicated Kubernetes cluster to a professional managed Kubernetes cluster.

-   A dedicated Kubernetes cluster is created. For more information, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).
-   A Cloud Assistant client is installed on the master nodes of the cluster. For more information, see [Overview](/intl.en-US/Deployment & Maintenance/Cloud assistant/Overview.md) and [Install the Cloud Assistant client](/intl.en-US/Deployment & Maintenance/Cloud assistant/Configure the Cloud Assistant client/Install the Cloud Assistant client.md).
-   The configuration of the internal-facing Server Load Balancer \(SLB\) instance of the cluster must be the same as that of slb.s1.small or higher.
-   Make sure that the following pods are migrated to worker nodes: pods of control plane components, such as the API server, kube-controller-manager, the cloud controller manager, and kube-scheduler, and pods in the kube-system namespace, excluding DaemonSet pods. Control plane components are replaced by managed components after the migration is completed.
-   An Object Storage Service \(OSS\) bucket is created. For more information, see [Create buckets](/intl.en-US/Console User Guide/Manage buckets/Create buckets.md).

## Usage notes

-   To use this feature, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to apply to be added to a whitelist.
-   Make sure that the Kubernetes version of your dedicated Kubernetes cluster is 1.16 or later. Otherwise, you must first upgrade the Kubernetes version. For more information about how to upgrade the Kubernetes version, see [Upgrade a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Manage clusters/Upgrade a cluster.md).
-   If the internal-facing SLB instance of your cluster is a shared-resource instance, the instance configuration must be the same as that of slb.s1.small or higher. For more information about how to upgrade an SLB instance, see [Change the configurations of a pay-as-you-go SLB instance]().
-   After the migration is completed, take note of the following limits:
    -   The master nodes no longer belong to the cluster and change to the **Unknown** state.
    -   You cannot roll back the migration.
    -   The Elastic Compute Service \(ECS\) instances in the dedicated cluster are not automatically deleted. You must manually delete these ECS instances.

## Migrate workloads from a dedicated Kubernetes cluster to a professional managed Kubernetes cluster

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the dedicated Kubernetes cluster that you want to migrate and click **Migrate to Professional Managed Kubernetes** in the **Actions** column.

4.  In the **Migrate to Professional Managed Kubernetes** dialog box, grant the permissions that are required to perform the migration and then click **OK**.

    To migrate workloads from a dedicated cluster to a professional managed cluster, you must perform the following operations to grant the required permissions.

    1.  In the **Migrate to Professional Managed Kubernetes** dialog box, click **RAM console**.

        ![migrate](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/6261403261/p269274.png)

    2.  On the details page of the master Resource Access Management \(RAM\) role, click the policy whose name starts with **k8sMasterRolePolicy**.

        ![master role](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/8260651261/p263338.png)

    3.  On the details page of the policy, click **Modify Policy Document** on the **Policy Document** tab.

    4.  In the **Modify Policy Document** panel, add the following content to the **Statement** section and then click **OK**.

        ```
        ,
                {
                    "Action": [
                        "oss:PutObject",
                        "oss:GetObject"
                    ],
                    "Effect": "Allow",
                    "Resource": [
                        "acs:oss:*:*:<YOUR_BUCKET_NAME>/*"  # Replace <YOUR_BUCKET_NAME> with the name of the OSS bucket that you selected in the Migrate to Professional Managed Kubernetes dialog box. 
                    ]
                }
        ```

        The following figure shows the modified policy content.

        ![Policy](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9260651261/p263342.png)

    After the migration is completed, the master nodes change to the **Unknown** state. You can perform the following steps to check the node states:

    1.  On the Clusters page, find the cluster that you want to manage and click **Details** in the **Actions** column.
    2.  In the left-side navigation pane of the details page, choose **Nodes** \> **Nodes**.
    3.  On the Nodes page, check the states of master nodes in the **Role/Status** column.

## Delete master nodes after the hot migration is completed

After you migrate workloads from a dedicated cluster to a professional managed cluster, you can delete the master nodes from the cluster. ACK does not allow you to delete master nodes in the ACK console. You must use kubectl to delete master nodes.

Before you run the commands, make sure that a kubectl client is connected to the cluster. For more information about how to use kubectl to connect to a cluster, see [Connect to ACK clusters by using kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md).

1.  Run the following command to query the names of master nodes that you want to delete:

    ```
    kubectl get node | grep master
    ```

2.  Run the following command to delete a master node:

    ```
    kubectl delete node <MASTER_NAME>
    ```

    **Note:** Replace <MASTER\_NAME\> with the name of the master node that you obtained in the previous step.


If cGPU Basic Edition is installed in a dedicated Kubernetes cluster, you must upgrade to cGPU Professional Edition in the professional managed Kubernetes cluster after the migration is completed. For more information, see [Upgrade cGPU Basic Edition to cGPU Professional Edition in a professional Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/cGPU Professional Edition/Upgrade cGPU Basic Edition to cGPU Professional Edition in a professional Kubernetes cluster.md).

