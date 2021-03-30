---
keyword: [standard managed Kubernetes cluster, professional managed Kubernetes cluster, real-time migration, cluster upgrade]
---

# Hot migration from standard managed Kubernetes clusters to professional managed Kubernetes clusters

Container Service for Kubernetes \(ACK\) supports hot migration from standard managed Kubernetes clusters to professional managed Kubernetes clusters. You can dynamically migrate your workloads from existing standard managed Kubernetes clusters to professional managed Kubernetes clusters. This allows you to benefit from the features that are provided by professional managed Kubernetes clusters. This topic describes how to migrate your workloads from a standard managed Kubernetes cluster to a professional managed Kubernetes cluster.

Professional managed Kubernetes clusters are developed based on standard managed Kubernetes clusters. Professional managed Kubernetes clusters are covered by the service level agreement \(SLA\) that supports compensation clauses. This type of cluster is suitable for enterprise users that require higher stability and security for large-scale production environments. For more information about professional managed Kubernetes clusters, see [ACK Pro overview](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/ACK Pro overview.md).

## Precautions

-   You can perform hot migration from standard managed Kubernetes clusters to professional managed Kubernetes clusters only if the version of the clusters is 1.16 or later.

-   However, you cannot migrate workloads from professional managed Kubernetes clusters to standard managed Kubernetes clusters.

## Migration process

The migration of workloads from a standard managed Kubernetes cluster to a professional managed Kubernetes cluster involves the following steps: cluster precheck and migration.

A standard managed Kubernetes cluster must pass the precheck before the workloads can be migrated.

-   If the cluster fails to pass the precheck, you can follow the instructions to go to the details page and view further details about the failure.
-   If the cluster passes the precheck, the migration process starts.

## Procedure

1.  On the **Clusters** page, find the standard managed Kubernetes cluster that you want to migrate and click **Migrate to Professional Managed Kubernetes** in the **Actions** column.

2.  In the **Migrate to Professional Managed Kubernetes** message, read the **notes** and click **OK**.

    ![Migrate a cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4660278061/p201671.png)

    After you click **OK**, ACK performs a precheck on the cluster. If the cluster fails to pass the precheck, you can follow the instructions to go to the details page and troubleshoot the issues. If the cluster passes the precheck, the migration process starts.

3.  After the cluster is migrated, click **OK** in the **Migrate to Professional Managed Kubernetes** message.

    On the Clusters page, the **cluster type** automatically changes from **Standard Managed Kubernetes** to **Professional Managed Kubernetes** after the cluster is migrated.


