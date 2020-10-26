---
keyword: [ACK Pro clusters, ACK Pro clusters, introduction to ACK Pro clusters]
---

# ACK Pro clusters

Clusters of Alibaba Cloud Container Service for Kubernetes \(ACK\) Pro are developed based on managed ACK clusters. ACK Pro clusters are covered by the service-level agreement \(SLA\) that supports compensation clauses and enhances the reliability and security of clusters.

ACK Pro clusters inherit all benefits of managed ACK clusters. For example, master nodes are also highly available and managed by ACK. In addition, ACK Pro clusters provide higher reliability, security, and schedulability. ACK Pro clusters are covered by SLA that supports compensation clauses. Therefore, ACK Pro clusters are suitable for enterprise users with large-scale business that requires higher stability and security.

## Scenarios

-   Internet enterprises. These enterprises deploy their business on a large scale and require business management with high stability, security, and observability.
-   Big data computing enterprises. These enterprises deploy large-scale data computing services, high-performance data processing services, and other services with high elasticity. These services require clusters with high stability, high performance, and efficient computing capabilities.
-   Foreign enterprises who have their business in China hold the standard compensation SLA and security in high regard.
-   Financial enterprises need to provide the standard compensation SLA.

## Features

-   Managed master nodes with higher reliability: The API server can automatically scale a cluster to a large number of nodes without service disruption. etcd is a reliable store for disaster recovery and data restoration. etcd uses cold backups and hot backups to ensure the availability of databases for ACK Pro clusters. Key metrics are collected for you to gain insights from control components. This allows you to predict potential risks on master node management.
-   Clusters with higher security: In the control plane, etcd uses encrypted disks by default. In the data plane, kms-plugin is installed to encrypt Kubernetes secrets. ACK provides security management for this type of cluster, especially in terms of inspecting and automatically repairing running containers.
-   More intelligent pod scheduling capability: kube-scheduler is integrated to provide better pod scheduling capability. This allows you to schedule pods in bulk, set multiple scheduling algorithms, and schedule pods to NPU-accelerated nodes. This also enhances the pod scheduling capability in scenarios where large-scale data computing or high-performance data processing is required.
-   SLA guarantees: ACK Pro clusters are covered by the SLA that supports compensation clauses. A level of 99.95% uptime is guaranteed for the cluster API server.

## Comparison

The following table compares ACK Pro clusters and ACK Standard clusters.

**Note:** Managed ACK clusters are renamed ACK Standard clusters.

|Category|ACK Managed Edition|
|ACK Pro cluster|ACK Standard cluster|
|--------|-------------------|
|---------------|--------------------|
|Cluster size|Up to 5,000 nodes.|Up to 100 nodes for each new cluster. Up to 5,000 nodes can be deployed in an existing ACK Standard cluster. Existing ACK Standard cluster can be upgraded to ACK Pro cluster.|
|SLA|99.95% \(supports compensation\).|99.90% \(does not support compensation\).|
|API Server|-   Provides the automatic scaling feature.
-   Monitors availability.

|Does not support features that are provided by the ACK Pro cluster.|
|etcd|Supports frequent cold backups, frequent hot backups, and remote disaster recovery.|Does not support features that are provided by the ACK Pro cluster.|
|Kube-scheduler|-   Supports multiple smart scheduling algorithms.
-   Supports the combination of multiple scheduling policies.

|Does not support features that are provided by the ACK Pro cluster.|
|Data encryption|Supports data encryption by using kms-plugin.|Does not support data encryption.|
|Security management|Supports the professional edition. The basic edition is optional.|Supports only the basic edition.|

