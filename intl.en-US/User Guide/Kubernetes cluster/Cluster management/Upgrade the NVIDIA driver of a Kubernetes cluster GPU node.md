# Upgrade the NVIDIA driver of a Kubernetes cluster GPU node {#concept_ndf_t2p_mgb .concept}

This topic describes how to upgrade the NVIDIA driver of a Kubernetes cluster GPU node where services are running or no service runs.

## Prerequisites {#section_rxk_1fp_mgb .section}

-   You have created a Kubernetes GPU cluster. For more information, see [Configure a Kubernetes GPU cluster to support GPU scheduling](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Configure a Kubernetes GPU cluster to support GPU scheduling.md#).
-   You have connected to the Kubernetes GPU cluster by using kubectl, see [Connect to a Kubernetes cluster by using kubectl](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#).

## Upgrade the NVIDIA driver of a GPU node where services are running {#section_fyj_pfp_mgb .section}

1.  Run the following command to disable scheduling for the target GPU node:

    ```
    kubectl cordon node-name
    ```

    **Note:** 

    -   Only the NVIDIA drivers of Worker nodes can be upgraded.
    -   The node-name parameter must be in the format of your-region-name.node-id.

        -   your-region-name indicates the name of the region where your cluster is located.
        -   node-id indicates the ID of the ECS instance where the target node is located.
        You can run the following command to view node-name:

        ```
        kubectl get node
        ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/112078/155007462337636_en-US.png)

2.  Run the following command to migrate the pods on the target node to other nodes:

    ```
    kubectl drain node-name --grace-period=120 --ignore-daemonsets=true
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/112078/155007462337635_en-US.png)

3.  Run the following command to log on to the target node:

    ```
    ssh root@xxx.xxx.x.xx
    ```

4.  On the target node, run the following command to view the driver version:

    ```
    nvidia-smi
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/112078/155007462337639_en-US.png)

5.  Run the following commands to remove the existing driver:

    **Note:** 

    -   If the existing driver is v384.111, you can directly run the commands in this step.
    -   If the existing driver is not v384.111, you must download the correct driver version from NVIDIA Website before running the commands in this step.
    ```
    cd /tmp
    ```

    ```
    curl -O https://cn.download.nvidia.cn/tesla/384.111/NVIDIA-Linux-x86_64-384.111.run
    ```

    ```
    chmod u+x NVIDIA-Linux-x86_64-384.111.run
    ```

    ```
    . /NVIDIA-Linux-x86_64-384.111.run --uninstall -a -s -q
    ```

6.  Run the following command to restart the target node:

    ```
    reboot
    ```

7.  Download the driver version that you want from the NVIDIA Website. This example uses v 410.79.
8.  Run the following command to install the downloaded NVIDIA driver in the directory where the driver is downloaded:

    ```
    sh . /NVIDIA-Linux-x86_64-410.79.run -a -s -q
    ```

9.  Run the following commands to add the following settings to the NVIDIA driver:

    ```
    nvidia-smi -pm 1 || true
    ```

    ```
    nvidia-smi -acp 0 || true
    ```

10. Run the following command to update two device plugins:

    ```
    mv /etc/kubernetes/manifests/nvidia-device-plugin.yml /
    ```

    ```
    mv /nvidia-device-plugin.yml /etc/kubernetes/manifests/
    ```

11. In any path of the Master node, run the following command to enable scheduling for the target node:

    ```
    kubectl uncordon node-name
    ```


**Verify the results**

Run the following command on the Master node. Then check the driver version for the target GPU node. The system displays that the driver is v410.79, indicating the node driver has been upgraded.

**Note:** You need to replace the node-name parameter with your target node name.

```
kubectl exec -n kube-system -t nvidia-device-plugin-node-name nvidia-smi
```

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/112078/155007462337730_en-US.png)

## Upgrade the NVIDIA driver of a GPU node where no service runs {#section_z1n_btj_ngb .section}

1.  Run the following command to log on to the target GPU node:

    ```
    ssh root@xxx.xxx.x.xx
    ```

2.  On the target node, run the following command to view the driver version:

    ```
    nvidia-smi
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/112078/155007462337639_en-US.png)

3.  Run the following commands to remove the existing driver:

    **Note:** 

    -   If the existing driver is v384.111, you can directly run the commands in this step.
    -   If the existing driver is not v384.111, you must download the correct driver version from NVIDIA Website before running the commands in this step.
    ```
    cd /tmp
    ```

    ```
    curl -O https://cn.download.nvidia.cn/tesla/384.111/NVIDIA-Linux-x86_64-384.111.run
    ```

    ```
    chmod u+x NVIDIA-Linux-x86_64-384.111.run
    ```

    ```
    . /NVIDIA-Linux-x86_64-384.111.run --uninstall -a -s -q
    ```

4.  Run the following command to restart the target node:

    ```
    reboot
    ```

5.  Download the driver version that you want from the NVIDIA Website. This example uses v 410.79.
6.  Run the following command to install the downloaded NVIDIA driver in the directory where the driver is downloaded:

    ```
    sh . /NVIDIA-Linux-x86_64-410.79.run -a -s -q
    ```

7.  Run the following commands to add the following settings to the NVIDIA driver:

    ```
    nvidia-smi -pm 1 || true
    ```

    ```
    nvidia-smi -acp 0 || true
    ```


**Verify the results**

Run the following command on the Master node. Then check the driver version for the target GPU node. The system displays that the driver is v410.79, indicating the node driver has been upgraded.

**Note:** You need to replace the node-name parameter with your target node name.

```
kubectl exec -n kube-system -t nvidia-device-plugin-node-name nvidia-smi
```

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/112078/155007462337730_en-US.png)

