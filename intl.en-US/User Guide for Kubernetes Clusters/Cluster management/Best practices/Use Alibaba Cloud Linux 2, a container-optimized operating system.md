# Use Alibaba Cloud Linux 2, a container-optimized operating system

Container Service for Kubernetes \(ACK\) allows you to create nodes that run the Alibaba Cloud Linux 2 operating system and provides optimizations for multiple scenarios based on the high-performance kernel of Alibaba Cloud Linux 2. This topic describes the optimizations that are provided by Alibaba Cloud Linux 2 on ACK to meet the requirements of different scenarios.

Alibaba Cloud Linux 2 is a next-generation proprietary Linux distribution that is developed by Alibaba Cloud. It provides a safe, stable, and high-performance customized environment for applications on Elastic Compute Service \(ECS\) instances. Alibaba Cloud Linux 2 is optimized for the cloud infrastructure and aims to deliver a better runtime experience. Alibaba Cloud Linux 2 images are free of charge, and Alibaba Cloud provides long-term technical support \(LTS\) for Alibaba Cloud Linux 2.

## Benefits of Alibaba Cloud Linux 2

Alibaba Cloud Linux 2 is intended for hosting and running containers on the Alibaba Cloud IaaS platform and provides various features specific to the Alibaba Cloud environment. Alibaba Cloud Linux 2 provides the following features:

-   The Linux distribution with the fastest boot time on Alibaba Cloud.
-   Deeply optimized for high-specification virtual machines and bare metal servers. This is particularly suitable for multi-task scenarios that involve large-sized instances.
-   Pre-installed with commonly used software such as Alibaba Cloud CLI and cloud-init. This reduces the costs of cloud resource management.
-   A streamlined kernel that minimizes the attack surface and system resource occupation.
-   An optimized technical support system that offers multi-channel technical support on Alibaba Cloud.
-   Fixes vulnerabilities at the earliest opportunity based on Common Vulnerabilities and Exposures \(CVE\).
-   Supports live patching of the kernel. This ensures service continuity when vulnerabilities are being fixed.

Alibaba Cloud Linux 2 provides the following benefits for the performance of applications that run Alibaba Cloud Linux 2:

-   Significantly reduces the system boot time of ECS instances. Scales out computing resources when system overloading is detected. Reduces the system boot time by 29% compared with CentOS 7.
-   Provides optimizations for ECS instances in multi-task scenarios and improves the performance of large-sized instances by 16%.
-   Improves the efficiency of system scheduling by 11%.
-   Optimizes the Linux networking stack to improve the network performance by 7.8% compared with CentOS 7.
-   By default, Alibaba Cloud Linux 2 is compiled with Bottleneck Bandwidth and Round-trip Propagation Time \(BBR\), a congestion control algorithm. In scenarios where frequent Internet access is required, you can change the congestion control algorithm of containers to BBR to improve the bandwidth stability for Internet access.
-   Optimizes the TLS encryption protocol.
-   Supports the Budget Fair Queueing \(BFQ\) I/O scheduler to reduce the disk latency.

## Optimizations provided by Alibaba Cloud Linux 2 on ACK in different scenarios

ACK provides kernel optimizations to allow containerized workloads to support more densely deployed container tasks without service interruptions. Based on the optimizations provided by Alibaba Cloud Linux 2 and its kernel. ACK offers further optimizations to improve the speed and stability of containerized workloads in various scenarios.

-   Container network optimization

    Alibaba Cloud Linux 2 is compiled with kernel 4.19. The Terway network plug-in supports the IPVLAN driver, which improves the network performance in short-packet communications by 40% compared with the traditional bridge mode and policy-based routing \(PBR\). By default, Alibaba Cloud Linux 2 has compiled the BBR congestion control algorithm. In scenarios where frequent Internet access is required, you can change the congestion control algorithm of containers to BBR to improve the bandwidth stability of Internet access. This significantly improves the efficiency when containers communicate with the Internet or ACK pulls images from the Internet.

-   Support and optimizations for Sandboxed-Container

    Alibaba Cloud has established partnerships with the Kata Containers and Clear Linux communities. You can seamlessly deploy the Kata Containers solution on ECS Bare Metal instances. ACK also reduces the boot time of runV containers. This ensures that the Kata Containers solution can work as expected. ACK also provides clusters that have sandboxed containers deployed. This type of cluster offers similar user experience as normal clusters. You can deploy applications in lightweight sandboxed environments to isolate workloads among multiple tenants. In addition, you can use sandboxed containers to isolate untrusted applications. Sandboxed-Container improves security and has little impact on application performance.

-   AutoScaler optimization

    Alibaba Cloud Linux 2 significantly reduces the system boot time of ECS instances. The average system boot time is reduced by 29% compared with CentOS 7. Based on the auto scaling feature provided by ACK, ACK clusters can create and launch ECS instance-based nodes when the system is overloaded. Then, ACK schedules and starts application instances accordingly. Alibaba Cloud Linux 2 enables nodes to quickly start up and scale out to withstand traffic spikes.

-   CoreDNS optimization

    When pods are densely deployed in an ACK cluster, Domain Name Service \(DNS\) resolution may fail because UDP connections account for excessive entries in the conntrack table. Alibaba Cloud Linux 2 has optimized the conntrack table to reduce table entries by 50% for the same DNS query rate. This significantly improves the success rate of DNS queries.

-   More fine-grained resource monitoring and control

    The kernel of Alibaba Cloud Linux 2 provides fine-grained visualization and control capabilities for containers, such as PSI pressure metrics, per-cgroup kswapd, and memory priority. In ACK clusters that run Alibaba Cloud Linux 2, you can use CGroup Controllers to adopt these capabilities, and achieve fine-grained resource configurations and dynamic updates. These resources include BufferIO Control, TCP, CPUSet, Mem, and NUMA. This provides a step-by-step improvement in resource utilization and minimizes the interference between applications.

-   Acceleration of AI-assisted computing and data processing

    Alibaba Cloud Linux 2 enables large-sized instances to handle high-performance computing tasks much faster. It also optimizes streaming reads and writes to improves the efficiency of reading and writing large model files. This significantly accelerates AI-assisted and high-performance computing. The following data is recorded in the staging environment:

    -   Test: Use Alluxio to load 1,152 files that add up to 144 GB of Object Storage Service \(OSS\) data through 64 threads. CentOS requires 3 minutes and 25 seconds while Alibaba Cloud Linux 2 requires only 2 minutes and 19.037 seconds. Alibaba Cloud Linux 2 is 1.6 times faster than CentOS.
    -   Test: Train the ResNet50 model with a batch size of 128 and cache data to Alluxio. In CentOS with NVIDIA V100, the speed is 5,212.00 images/s. In Alibaba Cloud Linux 2 with NVIDIA V100, the speed is 8,746.59 images/s. Alibaba Cloud Linux 2 is 1.7 times faster than CentOS.
-   Others
    -   Alibaba Cloud Linux 2 is compiled with Linux kernel 4.19. Alibaba Cloud Linux 2 is powered with core capabilities of Alibaba Cloud and provides better support for containerization.
    -   Reduces the performance loss of OverlayFS and minimizes the loss caused by containerization in storage.
    -   In most cases, sysctls are namespaced. In Kernel 4.19, most sysctls can be separately set on pods. This allows you to set different TCP timeout values and retransmit timeout values for different applications. You cannot modify these parameters in CentOS 7. In Alibaba Cloud Linux 2, you can set these parameters on pods.

## Set the operating system image of a node to Alibaba Cloud Linux 2

When you create an ACK cluster in the ACK console, set **Operating System** to **Alibaba Cloud Linux 2.1903**. This way, you can use Alibaba Cloud Linux 2 as the operating system image of the nodes in the cluster. For more information, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).

![linux](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0635359951/p96758.png)

**Note:**

After you select Alibaba Cloud Linux 2, ACK automatically checks for security patches of Alibaba Cloud Linux 2 and installs the patches when it creates the cluster, expands the cluster, or scales nodes.

**Related topics**  


[Product page of Alibaba Cloud Linux 2](https://www.alibabacloud.com/zh/products/alinux)

[Alibaba Cloud Kernel official website](https://alibaba.github.io/cloud-kernel/)

