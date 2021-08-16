# FAQ about cluster management

This topic provides answers to some frequently asked questions about cluster management.

-   [Can I add nodes that support Intel Software Guard Extensions \(Intel SGX\) to an existing cluster?](#section_f5n_8ir_imk)
-   [Are ACK clusters that run Alibaba Cloud Linux 2 compatible with container images that are based on CentOS?](#section_g8z_g18_h2h)
-   [How do I troubleshoot cluster creation failures?](~~86762~~)
-   [Troubleshoot cluster scale-out errors](~~178936~~)
-   [How do I troubleshoot cluster deletion failures?](~~86763~~)
-   [Timeout errors in cluster management by using Cloud Shell](~~141857~~)
-   [Can I change the container runtime after a cluster is created?](#section_4hy_0ag_uhv)

## Can I add nodes that support Intel Software Guard Extensions \(Intel SGX\) to an existing cluster?

To add nodes that support Intel SGX to an existing cluster, the cluster must meet the following conditions:

-   The Kubernetes version is 1.14.0 or later.
-   The network plug-in is **Flannel**.
-   The operating system is set to AliyunLinux 2.xxxx when you create the cluster. Do not select custom images when you add nodes to the cluster.

Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com/). On the Clusters page, find a cluster that meets the preceding requirements and choose **More** \> **Upgrade System Components** in the **Actions** column. On the Add-ons page, install Intel SGX Architectural Enclave Service Manager \(Intel SGX AESM\) and sgx-device-plugin.

## Are ACK clusters that run Alibaba Cloud Linux 2 compatible with container images that are based on CentOS?

Yes, ACK clusters that run Alibaba Cloud Linux 2 are fully compatible with container images that are based on CentOS. For more information, see [Use Alibaba Cloud Linux 2](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Operating system/Use Alibaba Cloud Linux 2.md).

## Can I change the container runtime after a cluster is created?

Issue: After a cluster that uses containerd as the container runtime is created, how do I change the container runtime to Docker.

Solution: After a cluster is created, you cannot change the container runtime used by the cluster. However, you can create a node pool that uses a different container runtime in the cluster. The container runtime used by a node pool can be different from those used by other node pools in the cluster. For more information, see [Node pool overview](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Node pool overview.md).

