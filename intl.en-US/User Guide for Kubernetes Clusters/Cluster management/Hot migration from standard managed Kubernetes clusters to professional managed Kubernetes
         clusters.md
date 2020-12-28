---
keyword: [standard managed Kubernetes cluster, professional managed Kubernetes cluster, real-time migration, cluster upgrade]
---

# Hot migration from standard managed Kubernetes clusters to professional managed Kubernetes clusters

Container Service for Kubernetes \(ACK\) supports hot migration from standard managed Kubernetes clusters to professional managed Kubernetes clusters. You can dynamically migrate your workloads from existing standard managed Kubernetes clusters to professional managed Kubernetes clusters. This allows you to benefit from the features that are provided by professional managed Kubernetes clusters. This topic describes how to migrate your workloads from a standard managed Kubernetes cluster to a professional managed Kubernetes cluster.

Professional managed Kubernetes clusters are developed based on standard managed Kubernetes clusters. Professional managed Kubernetes clusters are covered by the service-level agreement \(SLA\) that supports compensation clauses. This type of cluster is suitable for enterprise users that require higher stability and security for large-scale production environments. For more information about professional managed Kubernetes clusters, see [ACK Pro clusters](/intl.en-US/User Guide for Kubernetes Clusters/Introduction/ACK Pro clusters.md).

## Migration process

The migration of workloads from a standard managed Kubernetes cluster to a professional managed Kubernetes involves the following steps: cluster pre-check and migration.

The cluster must pass the pre-check before the workloads are migrated.

-   If the cluster fails to pass the pre-check, you can follow the instructions to go to the details page and view further details about the failure.
-   If the cluster passes the pre-check, the migration process starts.

## Procedure

1.  On the **Clusters** page, find the standard managed Kubernetes cluster that you want to migrate and click **Migrate to Pro** in the **Actions** column.

2.  In the **Migrate to Pro** message, read the **notes** and click **OK**.

    ![Migrate a cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4660278061/p201671.png)

    Click **OK**. ACK first performs a pre-check on the cluster. If the cluster fails to pass the pre-check, you can follow the instructions to go to the details page and view further details about the failure. If the cluster passes the pre-check, the migration process starts.

3.  After the migration is completed, click **OK** in the **Migrate to Pro** message.

    On the Clusters page, the **cluster type** automatically changes from **Standard Managed Kubernetes** to **Professional Managed Kubernetes** after the cluster is migrated.


