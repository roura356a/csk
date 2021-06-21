---
keyword: [GPU-accelerated instance fails, change of GPU IDs, restart or replace a GPU-accelerated instance]
---

# Fix the issue that the IDs of GPUs are changed after a GPU-accelerated ECS instance is restarted or replaced

After a GPU-accelerated instance fails, the IDs of the GPUs on the instance may be changed. If the GPU IDs are changed, the containers cannot be launched as normal. GPUOps is used to detect whether the IDs of the GPUs on a GPU-accelerated instance are the same as those stored in the /var/lib/kubelet/device-plugins/kubelet\_internal\_checkpoint file. If the GPU IDs are not the same, GPUOps deletes the `checkpoint` file. Then, Kubelet generates another `checkpoint` file. This ensures that the GPU IDs stored in the checkpoint file are the same as the actual GPU IDs. This topic describes how to fix the issue that the IDs of the GPUs on a GPU-accelerated instance are changed after the instance fails.

-   [Create a managed Kubernetes cluster with GPU-accelerated nodes](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/Create heterogeneous computing clusters/Create a managed Kubernetes cluster with GPU-accelerated nodes.md) or [Create a dedicated Kubernetes cluster with GPU-accelerated nodes](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/Create heterogeneous computing clusters/Create a dedicated Kubernetes cluster with GPU-accelerated nodes.md).
-   A jump server that uses SSH is added to the cluster. The jump server has access to the Internet. For more information, see [Configure SNAT entries for existing ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Enable an existing ACK cluster to access the Internet by using SNAT.md).

-   After a failed GPU-accelerated instance is restarted or replaced, the IDs of the GPUs on the instance may be changed. If the GPU IDs are different from those stored in the /var/lib/kubelet/device-plugins/kubelet\_internal\_checkpoint file, containers on the GPU-accelerated instance cannot be launched as normal.

    **Note:** The ID of a GPU may be changed in the following scenarios:

    -   A failed GPU-accelerated instance is restarted or replaced.
    -   You manually restart a GPU-accelerated instance.
-   GPUOps is a binary program that can run on Linux. For more information about how to download GPUOps, see [GPUOps](http://tpsservice-files-inner.cn-hangzhou.oss-cdn.aliyun-inc.com/files/resources/124309/ec31624aaa468726c71536a0e620fad3.zip).
-   A GPU-accelerated instance can be equipped with multiple GPUs. Each GPU has a unique ID.

## Step 1: Deploy GPUOps

**Deploy GPUOps on a single node**

1.  Run the following command to copy GPUOps to the /usr/local/bin/ directory and grant executable permissions to GPUOps:

    ```
    cp ./gpuops /usr/local/bin/
    chmod +x gpuops
    ```

2.  Run the following command to enable GPUOps to automatically start up with the instance:

    ```
    cat > /etc/systemd/system/gpuops.service <<EOF
    [Unit]
    Description=Gpuops: check kubelet checkpoint gpu status
    After=network-online.target
    Wants=network-online.target
    
    [Service]
    Type=oneshot
    RemainAfterExit=yes
    ExecStart=/usr/local/bin/gpuops check
    
    [Install]
    WantedBy=multi-user.target
    EOF
    
    systemctl enable gpuops.service
    ```


**Deploy GPUOps on multiple nodes at a time**

In the Kubernetes cluster with GPU-accelerated instances, deploy a DaemonSet by using the following YAML template:

```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: gpuops-deploy
  labels:
    k8s-app: gpuops-deploy
spec:
  selector:
    matchLabels:
      name: gpuops-deploy
  template:
    metadata:
      labels:
        name: gpuops-deploy
    spec:
      hostPID: true
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
              - matchExpressions:
                  - key: aliyun.accelerator/nvidia_name
                    operator: Exists
      containers:
        - name: gpuops
          image: registry.cn-beijing.aliyuncs.com/acs/gpuops:latest
          command:
          securityContext:
            privileged: true
          volumeMounts:
            - name: hostbin
              mountPath: /workspace/host/usr/local/bin
            - name: hostsystem
              mountPath: /workspace/host/etc/systemd/system
      terminationGracePeriodSeconds: 30
      volumes:
        - name: hostbin
          hostPath:
            path: /usr/local/bin
        - name: hostsystem
          hostPath:
            path: /etc/systemd/system
```

After the DaemonSet is deployed, the Kubernetes cluster with GPU-accelerated instances performs the following operations on all of the worker nodes:

-   Copies GPUOps to the /usr/local/bin/ directory and makes GPUOps executable.
-   Enables GPUOps to automatically start up with the instance.

When a failed GPU-accelerated instance in the cluster is restarted or replaced, GPUOps ensures that the GPU IDs stored in the checkpoint file are the same as the actual GPU IDs.

## Step 2: Verify that GPUOps has fixed the GPU ID issue

1.  After GPUOps is deployed, create a pod in the Kubernetes cluster with GPU-accelerated instances.

    The following YAML template is used to create the pod:

    ```
    apiVersion: apps/v1beta1
    kind: StatefulSet
    metadata:
      name: app-3g-v1
      labels:
        app: app-3g-v1
    spec:
      replicas: 1
      serviceName: "app-3g-v1"
      podManagementPolicy: "Parallel"
      selector: # define how the deployment finds the pods it manages
        matchLabels:
          app: app-3g-v1
      template: # define the pods specifications
        metadata:
          labels:
            app: app-3g-v1
        spec:
          containers:
          - name: app-3g-v1
            image: registry.cn-shanghai.aliyuncs.com/tensorflow-samples/cuda-malloc:3G
            resources:
              limits:
                nvidia.com/gpu: 1
    ```

2.  Run the following command to query the status of the pod:

    ```
    kubectl get pod
    ```

    Expected output:

    ```
    NAME                                  READY   STATUS       RESTARTS   AGE
    app-3g-v1-0                           1/1     Running      0          22s
    ```

3.  Log on to the GPU-accelerated instance where the pod runs and run the following command to restart the instance.

    **Note:** For more information about how to log on to a GPU-accelerated instance, see [Connect to a Linux instance by using password authentication](/intl.en-US/Instance/Connect to instances/Connect to an instance by using VNC/Connect to a Linux instance by using password authentication.md).

    ```
    sudo reboot
    ```

4.  Run the following command to print the log of GPUOps:

    ```
    journalctl -u gpuops
    ```

    -   If the following output is returned, the GPU ID stored in the `checkpoint` file is the same as the actual GPU ID. You do not need to perform any actions.

        ```
        June 28 14:49:00 iZ2vc1mysgx8bqdv3oyji9Z gpuops[21976]: {"level":"info","msg":"check gpu start...","time":"2020-06-28T14:49:00+08:00"}
        June 28 14:49:00 iZ2vc1mysgx8bqdv3oyji9Z gpuops[21976]: {"level":"info","msg":"find nvidia gpu: [\"GPU-383cd6a5-00a6-b455-62c4-c163d164b837\"]","time":"2020-06-28T14:49:00+08:00"}
        June 28 14:49:00 iZ2vc1mysgx8bqdv3oyji9Z gpuops[21976]: {"level":"info","msg":"read checkpoint: {\"Data\":{\"PodDeviceEntries\":[{\"PodUID\":\"300f47eb-e5c3-4d0b-9a87-925837b67732\",\"ContainerName\":\"app-3g-v1\",\"ResourceName\":\"aliyun.com/g
        June 28 14:49:00 iZ2vc1mysgx8bqdv3oyji9Z systemd[1]: Started Gpuops: check kubelet checkpoint gpu status.
        June 28 14:49:00 iZ2vc1mysgx8bqdv3oyji9Z gpuops[21976]: {"level":"info","msg":"find registered gpu: [\"GPU-383cd6a5-00a6-b455-62c4-c163d164b837\"]","time":"2020-06-28T14:49:00+08:00"}
        June 28 14:49:00 iZ2vc1mysgx8bqdv3oyji9Z gpuops[21976]: {"level":"info","msg":"find pod gpu: null","time":"2020-06-28T14:49:00+08:00"}
        June 28 14:49:00 iZ2vc1mysgx8bqdv3oyji9Z gpuops[21976]: {"level":"info","msg":"cached gpu info in checkpoint is up to date, check gpu finished","time":"2020-06-28T14:49:00+08:00"}
        ```

    -   If the output contains `warning` messages, the GPU ID is changed. In this case, GPUOps deletes the `checkpoint` file and Kubernetes generates another `checkpoint` file to store the new GPU ID.

        ```
        June 28 14:41:16 iZ2vc1mysgx8bqdv3oyji9Z systemd[1]: Starting Gpuops: check kubelet checkpoint gpu status...
        June 28 14:41:16 iZ2vc1mysgx8bqdv3oyji9Z gpuops[951]: {"level":"info","msg":"check gpu start...","time":"2020-06-28T14:41:16+08:00"}
        June 28 14:41:17 iZ2vc1mysgx8bqdv3oyji9Z gpuops[951]: {"level":"info","msg":"find nvidia gpu: [\"GPU-68ce64fb-ea68-5ad6-7e72-31f3f07378df\"]","time":"2020-06-28T14:41:17+08:00"}
        June 28 14:41:17 iZ2vc1mysgx8bqdv3oyji9Z gpuops[951]: {"level":"info","msg":"read checkpoint: {\"Data\":{\"PodDeviceEntries\":[{\"PodUID\":\"300f47eb-e5c3-4d0b-9a87-925837b67732\",\"ContainerName\":\"app-3g-v1\",\"ResourceName\":\"aliyun.com/gpu
        June 28 14:41:17 iZ2vc1mysgx8bqdv3oyji9Z gpuops[951]: {"level":"info","msg":"find registered gpu: [\"GPU-7fd52bfc-364d-1129-a142-c3f10e053ccb\"]","time":"2020-06-28T14:41:17+08:00"}
        June 28 14:41:17 iZ2vc1mysgx8bqdv3oyji9Z gpuops[951]: {"level":"info","msg":"find pod gpu: [\"GPU-7fd52bfc-364d-1129-a142-c3f10e053ccb\"]","time":"2020-06-28T14:41:17+08:00"}
        June 28 14:41:17 iZ2vc1mysgx8bqdv3oyji9Z gpuops[951]: {"level":"warning","msg":"the registered gpu uuid not equal with nvidia gpu","time":"2020-06-28T14:41:17+08:00"}
        June 28 14:41:17 iZ2vc1mysgx8bqdv3oyji9Z gpuops[951]: {"level":"warning","msg":"cached gpu info in checkpoint is out of date","time":"2020-06-28T14:41:17+08:00"}
        June 28 14:41:17 iZ2vc1mysgx8bqdv3oyji9Z gpuops[951]: {"level":"info","msg":"delete checkpoint file success","time":"2020-06-28T14:41:17+08:00"}
        June 28 14:41:17 iZ2vc1mysgx8bqdv3oyji9Z gpuops[951]: {"level":"info","msg":"kubelet restart success","time":"2020-06-28T14:41:17+08:00"}
        June 28 14:41:17 iZ2vc1mysgx8bqdv3oyji9Z gpuops[951]: {"level":"info","msg":"check gpu finished","time":"2020-06-28T14:41:17+08:00"}
        June 28 14:41:17 iZ2vc1mysgx8bqdv3oyji9Z systemd[1]: Started Gpuops: check kubelet checkpoint gpu status.
        ```

        -   Run the following command to query the actual GPU ID:

            ```
            nvidia-smi -L
            ```

            Expected output:

            ```
            GPU 0: Tesla T4 (UUID: GPU-0650a168-e770-3ea8-8ac3-8a1d419763e0)
            ```

        -   Run the following command to query the GPU ID stored in the `checkpoint` file:

            ```
            cat /var/lib/kubelet/device-plugins/kubelet_internal_checkpoint
            ```

            Expected output:

            ```
            {"Data":{"PodDeviceEntries":null,"RegisteredDevices":{"nvidia.com/gpu":["GPU-0650a168-e770-3ea8-8ac3-8a1d419763e0"]}},"Checksum":3952659280}
            ```

            The preceding output shows that the ID stored in the `checkpoint` file is the same as the actual GPU ID. This indicates that GPUOps has fixed the issue of inconsistent GPU IDs.


