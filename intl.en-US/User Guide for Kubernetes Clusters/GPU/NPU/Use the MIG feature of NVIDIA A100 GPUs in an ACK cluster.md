---
keyword: [GPU partitioning, MIG for NVIDIA A100 GPUs]
---

# Use the MIG feature of NVIDIA A100 GPUs in an ACK cluster

An NVIDIA A100 GPU provides higher performance over a V100 GPU and adds a third-generation Tensor Core. The Multi-Instance GPU \(MIG\) feature partitions an NVIDIA A100 GPU into separate GPU instances of different sizes. This delivers guaranteed quality of service \(QoS\) and achieves fault isolation. MIG turns an NVIDIA A100 GPU into an elastic resource pool. You can dynamically schedule GPU instances of different sizes in the pool to workloads. This topic describes how to use the MIG feature of an NVIDIA A100 GPU in a Container Service for Kubernetes \(ACK\) cluster.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

    **Note:** The MIG feature of NVIDIA A100 GPUs is supported by dedicated Kubernetes clusters, managed Kubernetes clusters, and professional Kubernetes clusters.

-   The Kubernetes version of the ACK cluster must be 1.18.8 or later.
-   The MIG feature has limits on zones and the stock of Elastic Compute Service \(ECS\) instances. If the **ecs.ebmgn7.26xlarge** instance type is unavailable when you create a node pool, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).
-   A jump server that uses SSH is added to the cluster. The jump server has access to the Internet. For more information, see [Configure SNAT entries for existing ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Enable an existing ACK cluster to access the Internet by using SNAT.md).

NVIDIA A100 GPUs are designed for scientific computing and analytics of cloud graphs and data.

**TensorCore**

An NVIDIA A100 GPU includes 19.5 teraflops of FP32 performance, 6,912 CUDA cores, and 40 GiB of memory. Each streaming multiprocessor \(SM\) includes 64 FP32 CUDA cores. An A100 GPU provides higher performance over a V100 GPU in terms of the following aspects:

-   The NVIDIA A100 GPU adds a third-generation Tensor Core. The throughput of sparse matrices for High Performance Computing \(HPC\) and deep learning on A100 GPUs is twice of that on V100 GPUs.
-   The third-generation Tensor Core can accelerate the computing of the following data types: FP16, BF16, TF32, FP64, INT8, INT4, and Binary.
-   An A100 GPU provides more options on precision. TF32 Tensor Core operations on an A100 GPU can accelerate FP32 input/output in deep learning frameworks and HPC. TF32 Tensor Core operations run 10 times faster than V100 FP32 FMA operations and 20 times faster with sparse matrices.
-   For FP16/FP32 mixed-precision deep learning, A100 Tensor Core is 2.5 times faster than V100 and 5 times faster with sparse matrices.
-   A100 sparse INT8 is 20 times faster than V100 INT8.
-   For HPC, A100 Tensor Core is 2.5 times faster than the FP64 performance of V100.

**MIG**

You can use the virtualization and GPU partitioning capabilities of MIG to partition an individual A100 GPU into up to seven GPU instances without extra cost.

MIG can partition an A100 GPU into multiple GPU instances. The SMs of each GPU instance have separate and isolated paths through the entire memory system. The on-chip crossbar ports, L2 cache banks, memory controllers, and DRAM address buses are all uniquely allocated to an individual GPU instance. This ensures that the workload of an individual user can run as normal. Even if the L2 caches and DRAM interfaces of one GPU instance are overloaded, other GPU instances are not affected. Each GPU instance is provided with guaranteed QoS and fault isolation.

![GPU20](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1169004261/p271760.png)

## Step 1: Create an ECS instance that is equipped with A100 GPUs

ACK allows you to add GPU-accelerated ECS instances that support MIG. You can purchase or add existing ECS instances equipped with A100 GPUs to an ACK cluster in the ACK console.

Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com). Find the cluster that you want to manage and create a node pool for the cluster. In the **Create Node Pool** dialog box, select **ecs.ebmgn7.26xlarge** in the **Instance Type** section.

For more information, see [Manage a node pool](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Manage a node pool.md).

## Step 2: Configure MIG

1.  Log on to the GPU-accelerated ECS instance and run the following command to query information about the GPU:

    ```
    nvidia-smi
    ```

    **Note:** For more information about how to log on to a GPU-accelerated ECS instance, see [Connect to a Linux instance by using password authentication](/intl.en-US/Instance/Connect to instances/Connect to an instance by using VNC/Connect to a Linux instance by using password authentication.md) or [Connect to a Windows instance by using password authentication](/intl.en-US/Instance/Connect to instances/Connect to an instance by using VNC/Connect to a Windows instance by using password authentication.md).

    Expected output:

    ![G12](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1169004261/p260643.png)

    The preceding figure shows that the ECS instance is equipped with eight A100 GPUs. Each A100 GPU runs independently and MIG is disabled for all GPUs.

2.  Run the following command to view the GPU partition sizes that are supported by a specified A100 GPU:

    ```
    nvidia-smi mig -i 0 -lgip
    ```

    Expected output:

    ![g14](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1169004261/p260650.png)

    The preceding figure shows the following information:

    -   The ID of the specified A100 GPU is 0. The A100 GPU supports the following GPU partition sizes: `1g.5gb`, `2g.10gb`, `3g.20gb`, `4g.20gb`, and `7g.40gb`. Each GPU partition corresponds to a different memory size. The total memory of each A100 GPU is not adjustable but the partition sizes are different. Therefore, the number of GPU instances that can be created varies based on the partition size that you choose.
    -   The IDs in the second column indicate the partition sizes.
3.  Run the following command to configure MIG for the A100 GPU whose ID is 0:

    ```
    nvidia-smi mig -i 0 -cgi 9,14,19,19
    ```

    Expected output:

    ```
    Successfully created GPU instance ID  2 on GPU  0 using profile MIG 3g.20gb (ID  9)
    Successfully created GPU instance ID  3 on GPU  0 using profile MIG 2g.10gb (ID 14)
    Successfully created GPU instance ID  9 on GPU  0 using profile MIG 1g.5gb (ID 19)
    Successfully created GPU instance ID 10 on GPU  0 using profile MIG 1g.5gb (ID 19)
    ```

    ```
    nvidia-smi mig -i 0 -cci
    ```

    Expected output:

    ```
    Successfully created compute instance ID  0 on GPU  0 GPU instance ID  9 using profile MIG 1g.5gb (ID  0)
    Successfully created compute instance ID  0 on GPU  0 GPU instance ID 10 using profile MIG 1g.5gb (ID  0)
    Successfully created compute instance ID  0 on GPU  0 GPU instance ID  3 using profile MIG 2g.10gb (ID  1)
    Successfully created compute instance ID  0 on GPU  0 GPU instance ID  2 using profile MIG 3g.20gb (ID  2)
    ```

    The preceding output indicates that the A100 GPU is partitioned into four GPU instances: one `3g.20gb` instance whose ID is 9, one `2g.10gb` instance whose ID is 14, and two `1g.5gb` instances whose IDs are both 19.

4.  Run the following command to view information about the GPU instances:

    ```
    nvidia-smi
    ```

    In the `MIG device` section, the following information about the GPU instances is displayed:

    ```
    +-----------------------------------------------------------------------------+
    | MIG devices:                                                                |
    +------------------+----------------------+-----------+-----------------------+
    | GPU  GI  CI  MIG |         Memory-Usage |        Vol|         Shared        |
    |      ID  ID  Dev |           BAR1-Usage | SM     Unc| CE  ENC  DEC  OFA  JPG|
    |                  |                      |        ECC|                       |
    |==================+======================+===========+=======================|
    |  0    2   0   0  |     11MiB / 20096MiB | 42      0 |  3   0    2    0    0 |
    |                  |      0MiB / 32767MiB |           |                       |
    +------------------+----------------------+-----------+-----------------------+
    |  0    3   0   1  |      7MiB /  9984MiB | 28      0 |  2   0    1    0    0 |
    |                  |      0MiB / 16383MiB |           |                       |
    +------------------+----------------------+-----------+-----------------------+
    |  0    9   0   2  |      3MiB /  4864MiB | 14      0 |  1   0    0    0    0 |
    |                  |      0MiB /  8191MiB |           |                       |
    +------------------+----------------------+-----------+-----------------------+
    |  0   10   0   3  |      3MiB /  4864MiB | 14      0 |  1   0    0    0    0 |
    |                  |      0MiB /  8191MiB |           |                       |
    +------------------+----------------------+-----------+-----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    ```

    You can use the preceding method to configure MIG for all of the A100 GPUs on the GPU-accelerated ECS instance.


## Step 3: Update the device plug-in

1.  After you configure MIG for all of the A100 GPUs on the node, run the following command to update the device plug-in and enable MIG:

    ```
    sed -i 's/"--pass-device-specs"/"--pass-device-specs", "--mig-strategy=mixed"/g' /etc/kubernetes/manifests/nvidia-device-plugin.yml
    ```

2.  Run the following command to check whether the device plug-in runs as normal:

    ```
    kubectl -n kube-system get po | grep nvidia-device
    ```

    Expected output:

    ```
    nvidia-device-plugin-cn-hangzhou.xxxxxx   1/1     Running   0          36m
    ```

3.  After you verify that the device plug-in runs as normal, run the following command to view the number of GPU instances and relevant resources:

    ```
    kubectl describe node <your node>
    ```

    Expected output:

    ```
    Capacity:
     cpu:                     104
     ephemeral-storage:       123722704Ki
     hugepages-1Gi:           0
     hugepages-2Mi:           0
     memory:                  791733364Ki
     nvidia.com/gpu:          0
     nvidia.com/mig-1g.5gb:   16
     nvidia.com/mig-2g.10gb:  8
     nvidia.com/mig-3g.20gb:  8
    ```


## Step 4: Deploy an application

Use the following YAML file to deploy an application that requests the **mig-2g.10gb** GPU instance:

```
kubectl apply -f - <<EOF
apiVersion: v1
kind: Pod
metadata:
  name: smi
spec:
  restartPolicy: OnFailure
  containers:
  - name: nvidia-smi
    command:
    - nvidia-smi 
    - -L
    image: nvidia/cuda:9.0-base
    resources:
      limits:
        nvidia.com/mig-2g.10gb: 1
      requests:
        nvidia.com/mig-2g.10gb: 1
EOF
```

Run the following command to print the application log:

```
kubectl logs smi
```

Expected output:

```
GPU 0: A100-SXM4-40GB (UUID: GPU-7780f282-99a1-7024-f7a4-65a55230****)
  MIG 2g.10gb Device 0: (UUID: MIG-GPU-7780f282-99a1-7024-f7a4-65a55230****/3/0)
```

