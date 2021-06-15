# Release notes for Kubernetes 1.18

Container Service for Kubernetes \(ACK\) strictly abides by the terms of the Certified Kubernetes Conformance Program. This topic describes the changes that are made to the Kubernetes 1.18 supported by ACK.

## Version upgrade

ACK has upgraded the components of Kubernetes 1.18.8 to provide enhanced features.

|Core component|Version|Upgrade notes|
|--------------|-------|-------------|
|Kubernetes|1.18.8|Some common API versions are deprecated in Kubernetes 1.18. Before you upgrade a cluster, we recommend that you upgrade the deprecated API versions that are listed in this topic.|
|Docker|19.03.5 \(containerd 1.2.10\)|None.|
|etcd|3.4.3|None.|
|CoreDNS|1.6.7|None.|

## Version details

-   **Resource changes and deprecation**

    The following APIs are deprecated in Kubernetes 1.18:

    -   The APIs apps/v1beta1 and apps/v1beta2 of all resources are replaced by **apps/v1**.
    -   The API extensions/v1beta1 of DaemonSets, Deployments, and ReplicaSets is replaced by **apps/v1**.
    -   The API extensions/v1beta1 of network policies is replaced by **networking.k8s.io/v1**.
    -   The API extensions/v1beta1 of pod security policies is replaced by **policy/v1beta1**.
    The labels that specify the regions of nodes are moved to the topology.kubernetes.io/region labels set. The labels that specify the zones of nodes are moved to the topology.kubernetes.io/zone labels set. We recommend that you update the related configurations for your workloads.

-   **Feature enhancements**
    -   [Server-side Apply](https://kubernetes.io/blog/2020/04/01/kubernetes-1.18-feature-server-side-apply-beta-2/) Beta 2 is introduced. You can view the relationships between the configuration items of a resource in the metadata.managedFields field of the resource.

    -   The [NodeLocal DNSCache](https://kubernetes.io/docs/tasks/administer-cluster/nodelocaldns/) feature is released to improve the DNS availability and performance of your cluster.

    -   The [Volume Snapshot](https://kubernetes.io/docs/concepts/storage/volume-snapshots/) feature is in the public preview phase and supports operations such as volume backup, recovery, and scheduled backup.

## Enhancements to Kubernetes 1.18.8

For Kubernetes 1.18.8, ACK enables the following feature in the kubelet configuration file: Users who use raw data volumes can upgrade clusters on the fly.

