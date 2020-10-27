# Upgrade the NVIDIA driver on a GPU node

This topic describes how to upgrade the NVIDIA driver on a GPU node when workloads are deployed on the node and when workloads are not deployed on the node.

-   A Kubernetes cluster is created. For more information, see [Configure a Kubernetes GPU cluster to support GPU scheduling](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU management/GPU resource scheduling/Configure a Kubernetes GPU cluster to support GPU scheduling.md).
-   The kubectl client is connected to the Kubernetes cluster. For more information, see [Use kubectl to connect to a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to a cluster.md).

## Upgrade the NVIDIA driver on a GPU node where workloads are deployed

1.  In the command-line interface \(CLI\), run the following command to set the node that you want to manage to the unschedulable state:

    ```
    kubectl cordon node-name
    ```

    **Note:**

    -   Only the NVIDIA driver on worker nodes can be upgraded.
    -   The value of node-name must be in the format of your-region-name.node-id.

        -   your-region-name specifies the region where the cluster that you want to manage is deployed.
        -   node-id specifies the ID of the Elastic Compute Service \(ECS\) instance on which the node is deployed.
        You can run the following command to check the value of node-name.

        ```
        kubectl get node
        ```

    ![Set to unschedulable](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5535359951/p37636.png)

2.  In the CLI, run the following command to migrate pods from the node in Step 1 to other nodes:

    ```
    kubectl drain node-name --grace-period=120 --ignore-daemonsets=true
    ```

    ![Migrate pods](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5535359951/p37635.png)

3.  In the CLI, run the following command to log on to the node that you specified in Step 1:

    ```
    ssh root@xxx.xxx.x.xx
    ```

4.  In the CLI, run the following command to check the current NVIDIA driver version:

    ```
    nvidia-smi
    ```

    ![Check the driver version](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5535359951/p37639.png)

5.  Download the required driver version and use one of the following methods to uninstall the existing driver version.

    -   If the driver runs version 384.111, run the following command:

        ```
        cd /tmp
        ```

    -   If the driver does not run version 384.111, download the required driver from the official NVIDIA website and run the following commands:

        ```
        curl -O https://cn.download.nvidia.cn/tesla/384.111/NVIDIA-Linux-x86_64-384.111.run
        ```

        ```
        chmod u+x NVIDIA-Linux-x86_64-384.111.run
        ```

        ```
        . /NVIDIA-Linux-x86_64-384.111.run --uninstall -a -s -q
        ```

6.  In the CLI, run the following command to restart the node.

    ```
    reboot
    ```

7.  Download the driver that you want to use from the [official NVIDIA website](https://www.nvidia.cn/). In this example, version 410.79 is used.

8.  In the CLI, run the following command to install the downloaded driver in the directory to which the driver is saved:

    ```
    sh . /NVIDIA-Linux-x86_64-410.79.run -a -s -q
    ```

9.  In the CLI, run the following commands to configure the driver:

    ```
    nvidia-smi -pm 1 || true
    ```

    ```
    nvidia-smi -acp 0 || true
    ```

10. In the CLI, run the following commands to update device-plugin:

    ```
    mv /etc/kubernetes/manifests/nvidia-device-plugin.yml /
    ```

    ```
    mv /nvidia-device-plugin.yml /etc/kubernetes/manifests/
    ```

11. Log on to a master node and run the following command to set the node to the schedulable state:

    ```
    kubectl uncordon node-name
    ```


Result

In the CLI, run the following command on a master node to check the NVIDIA driver version on the node. The driver runs version 410.79.

**Note:** Replace the value of node-name with the required node name.

```
kubectl exec -n kube-system -t nvidia-device-plugin-node-name nvidia-smi
```

![Upgrade the driver](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5535359951/p37730.png)

## Upgrade the NVIDIA driver on a GPU node where no workload is deployed

1.  In the CLI, run the following command to log on to the node that you want to manage:

    ```
    ssh root@xxx.xxx.x.xx
    ```

2.  In the CLI, run the following command to check the current NVIDIA driver version:

    ```
    nvidia-smi
    ```

    ![Check the driver version](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5535359951/p37639.png)

3.  Use one of the following methods to uninstall the existing driver:

    -   If the driver runs version 384.111, run the following command:

        ```
        cd /tmp
        ```

    -   If the driver does not run version 384.111, download the required driver from the official NVIDIA website and run the following commands:

        ```
        curl -O https://cn.download.nvidia.cn/tesla/384.111/NVIDIA-Linux-x86_64-384.111.run
        ```

        ```
        chmod u+x NVIDIA-Linux-x86_64-384.111.run
        ```

        ```
        . /NVIDIA-Linux-x86_64-384.111.run --uninstall -a -s -q
        ```

4.  In the CLI, run the following command to restart the node.

    ```
    reboot
    ```

5.  Download the driver that you want to use from the [official NVIDIA website](https://www.nvidia.cn/). In this example, version 410.79 is used.

6.  In the CLI, run the following command to install the downloaded driver in the directory to which the driver is saved:

    ```
    sh . /NVIDIA-Linux-x86_64-410.79.run -a -s -q
    ```

7.  In the CLI, run the following commands to configure the driver:

    ```
    nvidia-smi -pm 1 || true
    ```

    ```
    nvidia-smi -acp 0 || true
    ```


## Result

In the CLI, run the following command on a master node to check the NVIDIA driver version on the node. The driver runs version 410.79.

**Note:** Replace the value of node-name with the required node name.

```
kubectl exec -n kube-system -t nvidia-device-plugin-node-name nvidia-smi
```

![Upgrade the driver](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5535359951/p37730.png)

