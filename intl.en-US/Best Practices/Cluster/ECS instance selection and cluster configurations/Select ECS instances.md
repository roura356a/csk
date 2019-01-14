# Select ECS instances {#concept_yww_f2t_zfb .concept}

This topic describes the recommend ECS instances for creating a Kubernetes cluster.

## Overall cluster ECS instance selection {#section_fkg_sx4_1gb .section}

Low performance ECS instances have the following disadvantages:

-   The Worker nodes that run on low performance ECS instances can use only a limited number of network resources.
-   If one container consumes most of the resources provided by a low performance ECS instance, the remaining resources become idle because they are insufficient for operations such as creating new containers or restoring failed containers. If you set multiple low performance ECS instances, an excessive amount of resources will be wasted.

High performance ECS instances have the following advantages:

-   Large network bandwidth is available. For applications that require large bandwidth, resource usage is high.
-   More container communication occurs within one ECS instance, reducing data transmission over networks.
-   Images can be more efficiently pulled. For a cluster that uses high performance ECS instances, it only requires one attempt to pull an image and the pulled image then can be used by multiple containers. By contrast, for a cluster that uses low performance ECS instances, multiple attempts must be made to pull an image. Furthermore, scaling a cluster that uses low performance ECS instances takes much longer to perform.

## Select the Master node specification {#section_g2v_jry_nfb .section}

For Kubernetes clusters created through Alibaba Cloud Container Service, core components such as etcd, kube-apiserver, and kube-controller run on Mater nodes. These core components are critical for ensuring cluster stability. Generally, large clusters have higher requirements on the Master node specification.

**Note:** You can determine your cluster size by considering the following factors: the number of nodes, the number of pods, deployment frequency, and the number of visits. In this topic, only the number of nodes is used to determine the size of a cluster.

To select the Master node specification of a cluster of the standard size, see the following table. However, you can select the lower performance Master nodes for clusters in a test environment. The specifications recommended in the following table are designed to keep Master node loads low.

|Number of nodes|Master node specification|
|---------------|-------------------------|
|1 to 5|4 cores, 8 GiB \(We recommend that you do not select 2 cores with 4 GiB.\)|
|6 to 20|4 cores, 16 GiB|
|21 to 100|8 cores, 32 GiB|
|100 to 200|16 cores, 64 GiB|

## Select the Worker node specification {#section_nrx_q5p_1gb .section}

-   Determine the number of cores required by the cluster and the allowed core failure ratio

    For example, assume a cluster has 160 cores in total. If the allowed core failure ratio is 10%, you must select at least ten 16-core ECS instances and ensure that the upper limit of the cluster load is 160\*90%=144 cores. If the allowed core failure ratio is 20%, you must select at least five 32-core ECS instances and ensure that the upper limit of the cluster load is 160\*80%=128 cores. In either of these two cases, if one ECS instance fails, the remaining ECS instances can still support the cluster services.

-   Determine the CPU:memory ratio If you run applications that consume large amount of memory resource, for example, Java applications, we recommend that you select an ECS instance with a CPU:memory ratio of 1:8.

## Select the ECS Bare Metal Instance {#section_qyd_lwp_1gb .section}

We recommend that you select an ECS Bare Metal \(EBM\) Instance in the following two scenarios:

-   You cluster requires 1000 cores for daily operation. In this case, you can use about ten or eleven EBM instances to build your cluster because one EBM instance has a minimum of 96 cores.
-   You want to quickly scale out a large number of containers. For example, assume that you are prepared for a popular E-commerce product promotion. To handle the expected large amount of traffic, you can add EBM instances to your cluster because a single EBM instance can run multiple containers.

EBM instances provide the following benefits to your cluster:

-   Ultra-high network performance. Remote Direct Memory Access \(RDMA\) technology is used. Furthermore, the Terway plugin is designed for you to get the most from your hardware and provides a container bandwidth higher than 9 Gbit/s across hosts.
-   Zero jitter computing performance. EBM instances use chips developed by Alibaba Cloud to replace Hypervisor, meaning virtualization overhead or resource preemption concerns are no longer issues.
-   High security. EBM instances use physical level encryption, support the Intel SGX encryption, provide a reliable computing environment, and support blockchain applications.

