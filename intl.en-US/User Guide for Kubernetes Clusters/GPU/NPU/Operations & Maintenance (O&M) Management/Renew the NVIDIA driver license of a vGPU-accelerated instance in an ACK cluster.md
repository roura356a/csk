---
keyword: [vGPU-accelerated instance, GPU virtualization instance type, renew the NVIDIA driver licenses of vGPU-accelerated nodes]
---

# Renew the NVIDIA driver license of a vGPU-accelerated instance in an ACK cluster

If a Container Service for Kubernetes \(ACK\) cluster contains vGPU-accelerated nodes, you must purchase NVIDIA GRID licenses and set up GRID license servers to enable the nodes to work as normal. This topic describes how to renew the NVIDIA driver license of a vGPU-accelerated node in an ACK cluster.

-   An NVIDIA driver license is purchased. For more information, see [Purchase an NVIDIA driver license](https://enterpriseproductregistration.nvidia.com/?LicType=EVAL&ProductFamily=vGPU).
-   This topic is applicable to dedicated, managed, and professional Kubernetes clusters.

## An ACK cluster contains vGPU-accelerated nodes

After the license server is set up, perform the following steps to renew the GRID license of a vGPU-accelerated node.

**Note:** You must first purchase a vGPU-accelerated Elastic Compute Service \(ECS\) instance and familiarize yourself with the NVIDIA documentation about how to set up a GRID license server. For more information, see the [NVIDIA official website](https://www.nvidia.com/object/nvidia-enterprise-account.html?spm=a2c4g.11186623.2.10.3fac7d8fHWP47f).

1.  Log on to a vGPU-accelerated node and modify the following settings in the /etc/nvidia/gridd.conf file:

    **Note:** For more information about how to log on to a vGPU-accelerated node, see [Connect to a Linux instance by using password authentication](/intl.en-US/Instance/Connect to instances/Connect to an instance by using VNC/Connect to a Linux instance by using password authentication.md) and [Connect to a Windows instance by using password authentication](/intl.en-US/Instance/Connect to instances/Connect to an instance by using VNC/Connect to a Windows instance by using password authentication.md).

    ```
    ServerAddress=<your License Server address>
    ServerPort=<License Server port>
    ```

2.  Run the following commands to restart the NVIDIA driver:

    ```
    systemctl daemon-reload
    systemctl restart nvidia-gridd
    ```

3.  Run the following command and check the output:

    ```
    nvidia-smi
    ```

    Expected output:

    ![222](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3196737261/p291941.png)

    The output indicates that the `nvidia-smi` command is successfully run. This means that the NVIDIA driver license of the vGPU-accelerated instance is renewed.


