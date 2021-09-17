# FAQ about cluster management

This topic provides answers to some frequently asked questions about cluster management.

-   [Can I add nodes that support Intel Software Guard Extensions \(Intel SGX\) to an existing cluster?](#section_f5n_8ir_imk)
-   [Are ACK clusters that run Alibaba Cloud Linux 2 compatible with container images that are based on CentOS?](#section_g8z_g18_h2h)
-   [How do I troubleshoot cluster creation failures?](~~86762~~)
-   [Troubleshoot cluster scale-out errors](~~178936~~)
-   [How do I troubleshoot cluster deletion failures?](~~86763~~)
-   [Timeout errors in cluster management by using Cloud Shell](~~141857~~)
-   [Can I change the container runtime after a cluster is created?](#section_4hy_0ag_uhv)
-   [What are the differences between Docker and Sandboxed-Container?](#section_qth_71w_zay)
-   [Is ACK certified for Level 3 Cybersecurity?](#section_vjv_8xu_ud2)
-   [Can I upgrade a dedicated Kubernetes cluster after I accidentally delete a master node of the cluster?](#section_4ee_drq_c2e)

## Can I add nodes that support Intel Software Guard Extensions \(Intel SGX\) to an existing cluster?

To add nodes that support Intel SGX to an existing cluster, the cluster must meet the following conditions:

-   The Kubernetes version is 1.14.0 or later.
-   The network plug-in is **Flannel**.
-   The operating system is set to AliyunLinux 2.xxxx when you create the cluster. Do not select custom images when you add nodes to the cluster.

Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com/). On the Clusters page, find a cluster that meets the preceding requirements and choose **More** \> **Upgrade System Components** in the **Actions** column. On the Add-ons page, install Intel SGX Architectural Enclave Service Manager \(Intel SGX AESM\) and sgx-device-plugin.

## Are ACK clusters that run Alibaba Cloud Linux 2 compatible with container images that are based on CentOS?

Yes, ACK clusters that run Alibaba Cloud Linux 2 are fully compatible with container images that are based on CentOS. For more information, see [Use Alibaba Cloud Linux 2](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Operating system/Use Alibaba Cloud Linux 2.md).

## Can I change the container runtime after a cluster is created?

Issue: I created a cluster that uses containerd as the container runtime and I want to change the container runtime to Docker.

Solution: After a cluster is created, you cannot change the container runtime used by the cluster. However, you can create node pools that use different container runtimes in the cluster. The container runtimes used by node pools in the cluster can be different. For more information, see [Node pool overview](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Node pool overview.md).

## What are the differences between Docker and Sandboxed-Container?

Sandboxed-Container is an alternative to the Docker runtime. Sandboxed-Container allows you to run applications in a sandboxed and lightweight virtual machine that has a dedicated kernel. This enhances resource isolation and improves security. Sandboxed-Container is suitable in scenarios such as untrusted application isolation, fault isolation, performance isolation, and load isolation among multiple users. Sandboxed-Container improves security and has minor impacts on application performance. In addition, Sandboxed-Container provides the same user experience as Docker in terms of logging, monitoring, and elastic scaling. Sandboxed-Container supports only Elastic Compute Service \(ECS\) bare metal instances. Other ECS instance types are not supported.

For more information about Docker and Sandboxed-Container, see [Comparison of Docker, containerd, and Sandboxed-Container](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Comparison of Docker, containerd, and Sandboxed-Container.md).

## Is ACK certified for Level 3 Cybersecurity?

ACK and other Alibaba Cloud services have been certified for Level 3 Cybersecurity. However, Alibaba Cloud does not have limits on the use of these cloud services. You must bear responsibility for any lost profits arising from the improper use of Alibaba Cloud services. The following lists describe the items for which Alibaba Cloud and you must bear responsibility to guarantee the security of Alibaba Cloud services and your business:

-   Items for which Alibaba Cloud bears responsibility:
    -   The security of infrastructure resources for Alibaba Cloud services.
    -   The security of etcd and nodes in the cluster control plane.
    -   The security compliance of components in the cluster control plane. Alibaba Cloud also accepts security inspections from third parties.
-   Items for which you bear responsibility:
    -   Security configurations of the data plane, including the configurations of security groups of virtual private clouds \(VPCs\).
    -   Configurations of nodes and pods.
    -   Operating systems of nodes, including upgrades and security patches.
    -   Other related software.
    -   Access control on devices and networks, such as firewall rules.
    -   Platform-level identity verification and access control by using Resource Access Management \(RAM\) or other services.
    -   Security of sensitive data.

## Can I upgrade a dedicated Kubernetes cluster after I accidentally delete a master node of the cluster?

No. After a master node of a dedicated Kubernetes cluster is deleted, you cannot add another master node or upgrade the Kubernetes version of the cluster.

