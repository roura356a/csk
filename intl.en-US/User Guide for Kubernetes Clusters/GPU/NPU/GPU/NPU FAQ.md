# GPU/NPU FAQ

-   [How do I upgrade the kernel of a GPU node?](https://www.alibabacloud.com/help/zh/faq-detail/123756.htm)
-   [How do I fix a container startup exception on a GPU node?](https://www.alibabacloud.com/help/zh/faq-detail/123771.htm)
-   [Troubleshoot issues in GPU monitoring](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/Operations & Maintenance (O&M) Management/Troubleshoot issues in GPU monitoring.md)
-   [The number of available GPUs is less than the actual number of GPUs](~~144735~~)
-   [Errors in GPU nodes when kubelet or Docker restarts](~~123771~~)
-   [Fix the issue that the IDs of GPUs are changed after a GPU-accelerated ECS instance is restarted or replaced](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/Operations & Maintenance (O&M) Management/Fix the issue that the IDs of GPUs are changed after a GPU-accelerated ECS instance is restarted or replaced.md)
-   [Does ACK support vGPU-accelerated instances?](#section_ug2_7io_hhw)

## Does ACK support vGPU-accelerated instances?

A vGPU-accelerated instance can work as normal only when an NVIDIA GRID license is purchased and a GRID license server is set up. However, Alibaba Cloud does not provide GRID license servers. As a result, after a Container Service for Kubernetes \(ACK\) cluster that contains vGPU-accelerated instances is created, you cannot directly use the vGPU-accelerated instances in the cluster. Therefore, ACK no longer allows you to select vGPU-accelerated instances when you create clusters in the ACK console.

If your workloads are highly dependent on vGPU-accelerated instances, you can purchase NVIDIA GRID licenses and set up GRID license servers on your own.

**Note:**

-   GRID license servers are required to renew the NVIDIA driver licenses of vGPU-accelerated instances.
-   You must purchase vGPU-accelerated Elastic Compute Service \(ECS\) instances and familiarize yourself with the NVIDIA documentation about how to set up GRID license servers. For more information, see the [NVIDIA official website](https://www.nvidia.com/object/nvidia-enterprise-account.html?spm=a2c4g.11186623.2.10.3fac7d8fHWP47f).

After you have set up a GRID license server, perform the following steps to add a vGPU-accelerated instance to your ACK cluster.

**Add a vGPU-accelerated instance to your ACK cluster**

1.  [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to apply to be allowed to use custom images.
2.  Create a custom image that is based on CentOS 7.X or Alibaba Cloud Linux 2. The custom image must be installed with the NVIDIA GRID driver and configured with an NVIDIA license. For more information, see [Create a custom image from an instance](/intl.en-US/Images/Custom image/Create custom image/Create a custom image from an instance.md) and [Install a NVIDIA GRID driver on a GPU-accelerated Linux instance](/intl.en-US/Instance/Instance type families/Compute optimized type family with GPU/Install a NVIDIA GRID driver on a GPU-accelerated Linux instance.md).
3.  Create a node pool by using the custom image created in the preceding step. For more information, see [Manage node pools](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Manage node pools.md).
4.  Add your vGPU-instance to the node pool created in Step 3. For more information, see [Add existing ECS instances to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node/Add existing ECS instances to an ACK cluster.md).

**What to do next: Renew the NVIDIA driver license of a vGPU-accelerated instance in an ACK cluster**

For more information about how to renew the NVIDIA driver license of a vGPU-accelerated instance in an ACK cluster, see [Renew the NVIDIA driver license of a vGPU-accelerated instance in an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/Operations & Maintenance (O&M) Management/Renew the NVIDIA driver license of a vGPU-accelerated instance in an ACK cluster.md).

