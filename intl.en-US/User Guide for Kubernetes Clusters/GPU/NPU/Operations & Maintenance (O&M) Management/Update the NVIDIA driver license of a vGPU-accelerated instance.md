---
keyword: [an ACK cluster is added with vGPU-accelerated instances, an ACK cluster is not added with vGPU-accelerated instances, update the NVIDIA driver license of a vGPU-accelerated instance]
---

# Update the NVIDIA driver license of a vGPU-accelerated instance

Worker nodes can be deployed on vGPU-accelerated instances. A vGPU-accelerated instance is a lightweight GPU-accelerated instance. To use vGPU-accelerated instances, you must purchase NVIDIA driver licenses. Before you can update a license based on scenarios described in this topic, make sure that you have purchased an NVIDIA driver license.

## Scenario 1: An ACK cluster contains vGPU-accelerated instances

**Note:**

-   If no license is purchased, click [here](https://enterpriseproductregistration.nvidia.com/?LicType=EVAL&ProductFamily=vGPU) to purchase a license.
-   The scenarios described in this topic are suitable to dedicated, managed, and professional Kubernetes clusters.

1.  If your Container Service for Kubernetes \(ACK\) cluster contains vGPU-accelerated instances, log on to a vGPU-accelerated instance in your cluster and modify the following field settings in the /etc/nvidia/gridd.conf file:

    **Note:** For more information about how to log on to a GPU-accelerated node, see [Connect to a Linux instance by using password authentication](/intl.en-US/Instance/Connect to instances/Connect to an instance by using VNC/Connect to a Linux instance by using password authentication.md) and [Connect to a Windows instance by using password authentication](/intl.en-US/Instance/Connect to instances/Connect to an instance by using VNC/Connect to a Windows instance by using password authentication.md).

    ```
    ServerAddress=<your License Server address>
    ServerPort=<License Server port>
    ```

2.  Run the following command to restart vGPU:

    ```
    systemctl daemon-reload
    systemctl restart nvidia-gridd
    ```

3.  Run the following command to check whether the command is successfully run:

    ```
    nvidia-smi
    ```

    Expected output:

    ```
    Wed Jun  2 14:22:29 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 450.102.04   Driver Version: 450.102.04   CUDA Version: 11.0     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00000000:00:07.0 Off |                    0 |
    | N/A   28C    P8     8W /  70W |      0MiB / 15109MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    ```

    The output indicates that the `nvidia-smi` command is successfully run. This means that the NVIDIA driver license of the vGPU-accelerated instance is updated.


## Scenario 2: An ACK cluster does not contain vGPU-accelerated instances

If your ACK cluster does not contain vGPU-accelerated instances, you must first add a vGPU-accelerated instance to the cluster. To do this, you can manually add an existing vGPU-accelerated instance or enable the system to automatically add an existing vGPU-accelerated instance. Then, perform the steps as described in Scenario 1.

For more information about how to add an instance to an ACK cluster, see [Add existing ECS instances to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node/Add existing ECS instances to an ACK cluster.md).

