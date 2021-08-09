---
keyword: [Windows container, GPU acceleration, DirectX]
---

# Enable GPU acceleration for DirectX in Windows containers

GPUs provide higher parallel computing power than CPUs for workloads on Windows nodes and can accelerate operations by orders of magnitude. This reduces costs and improves throughput. Windows containers support GPU acceleration for Direct eXtension \(DirectX\) and all the frameworks that are built on top of DirectX. This topic describes how to install the DirectX device plug-in on Windows nodes and how to enable GPU acceleration for DirectX.

-   A Container Service for Kubernetes \(ACK\) cluster is created and the Kubernetes version is 1.20.4 or later. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
    -   Windows nodes support only the Docker and containerd runtimes. For more information, see [Comparison of Docker, containerd, and Sandboxed-Container](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Comparison of Docker, containerd, and Sandboxed-Container.md).
    -   The instance types of the Windows nodes belong to the GPU-accelerated virtualization instance family and the Elastic Compute Service \(ECS\) images of the nodes are pre-installed with a GRID driver that has an activated license. For more information, see [GPU-accelerated virtualization instance families]().
-   A Windows node pool is created. For more information, see [Create a Windows node pool](/intl.en-US/User Guide for Kubernetes Clusters/Windows container/Create a Windows node pool.md).
-   A kubectl client is connected to the ACK cluster. For more information, see [Connect to an ACK cluster by using kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md).

DirectX is a type of API. DirectX can enable Windows-based games and multimedia programs to achieve higher execution efficiency, enhance 3D graphics and sound effects, and provide designers with a common hardware driver standard. This reduces the complexity of installing and setting up hardware. DirectX can allow GPUs to perform more general-purpose computing. It also reduces overload and encourages developers to better use GPUs as parallel processors.

## Step 1: Install the DirectX device plug-in on Windows nodes

Deploy the DirectX device plug-in as a DaemonSet on Windows nodes.

1.  Create a file named directx-device-plugin-windows.yaml and copy the following code to the file:

    ```
    apiVersion: apps/v1
    kind: DaemonSet
    metadata:
      labels:
        k8s-app: directx-device-plugin-windows
      name: directx-device-plugin-windows
      namespace: kube-system
    spec:
      revisionHistoryLimit: 10
      selector:
        matchLabels:
          k8s-app: directx-device-plugin-windows
      template:
        metadata:
          annotations:
            scheduler.alpha.kubernetes.io/critical-pod: ""
          labels:
            k8s-app: directx-device-plugin-windows
        spec:
          tolerations:
            - operator: Exists
          # since 1.18, we can specify "hostNetwork: true" for Windows workloads, so we can deploy an application without NetworkReady.
          hostNetwork: true
          affinity:
            nodeAffinity:
              requiredDuringSchedulingIgnoredDuringExecution:
                nodeSelectorTerms:
                  - matchExpressions:
                      - key: type
                        operator: NotIn
                        values:
                          - virtual-kubelet
                      - key: beta.kubernetes.io/os
                        operator: In
                        values:
                          - windows
                      - key: windows.alibabacloud.com/deployment-topology
                        operator: In
                        values:
                          - "2.0"
                      - key: windows.alibabacloud.com/directx-supported
                        operator: In
                        values:
                          - "true"
                  - matchExpressions:
                      - key: type
                        operator: NotIn
                        values:
                          - virtual-kubelet
                      - key: kubernetes.io/os
                        operator: In
                        values:
                          - windows
                      - key: windows.alibabacloud.com/deployment-topology
                        operator: In
                        values:
                          - "2.0"
                      - key: windows.alibabacloud.com/directx-supported
                        operator: In
                        values:
                          - "true"
          containers:
            - name: directx
              command:
                - pwsh.exe
                - -NoLogo
                - -NonInteractive
                - -File
                - entrypoint.ps1
              # Replace <cn-hangzhou> in the following image address with the ID of the region where the cluster is deployed. 
              image: registry-vpc.cn-hangzhou.aliyuncs.com/acs/directx-device-plugin-windows:v1.0.0
              imagePullPolicy: IfNotPresent
              volumeMounts:
                - name: host-binary
                  mountPath: c:/host/opt/bin
                - name: wins-pipe
                  mountPath: \\.\pipe\rancher_wins
          volumes:
            - name: host-binary
              hostPath:
                path: c:/opt/bin
                type: DirectoryOrCreate
            - name: wins-pipe
              hostPath:
                path: \\.\pipe\rancher_wins
    ```

2.  Run the following command to install the DirectX device plug-in:

    ```
    kubectl create -f directx-device-plugin-windows.yaml
    ```


## Step 2: Deploy a Windows workload that has GPU acceleration enabled for DirectX

The DirectX device plug-in can automatically add the `class/<interface class GUID>` device for Windows containers. This enables Windows containers to access DirectX services on the ECS host. For more information, see [Devices in containers on Windows](https://docs.microsoft.com/zh-cn/virtualization/windowscontainers/deploy-containers/hardware-devices-in-containers). Add the following resources parameter for the Windows workload that requires GPU acceleration and redeploy the workload.

```
spec:
  ...
  template:
    ...
    spec:
      ...
      containers:
        - name: gpu-user
          ...
+         resources:
+           limits:
+             windows.alibabacloud.com/directx: "1"
+           requests:
+             windows.alibabacloud.com/directx: "1"
```

**Note:** The preceding configuration does not allocate all GPU resources on the ECS host to the containers, or prevent other applications from accessing the GPUs on the ECS host. The GPU resources are dynamically scheduled between the ECS host and containers. This means that you can run multiple Windows containers on the ECS host and each container can use DirectX hardware acceleration.

For more information about GPU acceleration in Windows containers, see [GPU acceleration in Windows containers](https://docs.microsoft.com/zh-cn/virtualization/windowscontainers/deploy-containers/gpu-acceleration).

## Check whether GPU acceleration is enabled for the Windows workload

Use the following sample application to check whether the DirectX device plug-in is successfully deployed on Windows nodes.

1.  Create a file named gpu-job-windows.yaml and copy the following code to the file:

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      labels:
        k8s-app: gpu-job-windows
      name: gpu-job-windows
      namespace: default
    spec:
      parallelism: 1
      completions: 1
      backoffLimit: 3
      manualSelector: true
      selector:
        matchLabels:
          k8s-app: gpu-job-windows
      template:
        metadata:
          labels:
            k8s-app: gpu-job-windows
        spec:
          restartPolicy: Never
          affinity:
            nodeAffinity:
              requiredDuringSchedulingIgnoredDuringExecution:
                nodeSelectorTerms:
                  - matchExpressions:
                      - key: type
                        operator: NotIn
                        values:
                          - virtual-kubelet
                      - key: beta.kubernetes.io/os
                        operator: In
                        values:
                          - windows
                  - matchExpressions:
                      - key: type
                        operator: NotIn
                        values:
                          - virtual-kubelet
                      - key: kubernetes.io/os
                        operator: In
                        values:
                          - windows
          tolerations:
            - key: os
              value: windows
          containers:
            - name: gpu
              # Replace <cn-hangzhou> in the following image address with the ID of the region where the cluster is deployed. 
              image: registry-vpc.cn-hangzhou.aliyuncs.com/acs/sample-gpu-windows:v1.0.0
              imagePullPolicy: IfNotPresent
              resources:
                limits:
                  windows.alibabacloud.com/directx: "1"
                requests:
                  windows.alibabacloud.com/directx: "1"
    ```

    **Note:**

    -   Image `registry-vpc.{region}.aliyuncs.com/acs/sample-gpu-windows` is a sample image for GPU acceleration in Windows containers provided by ACK. This image is built on top of microsoft-windows. For more information, see [mcr.microsoft.com/windows](https://hub.docker.com/_/microsoft-windows).
    -   The image file is 15.3 GB in size and may require a long time to download when you use it to deploy applications.
    -   In this example, [WinMLRunner](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Tools/WinMLRunner) is used to generate simulated input data. After GPU acceleration is enabled for the `gpu-job-windows` Job, 100 evaluations are performed based on the [Tiny YOLOv2](https://github.com/onnx/models/tree/master/vision/object_detection_segmentation/tiny-yolov2) model to output the final performance data.
2.  Run the following command to create the sample application:

    ```
    kubectl create -f directx-device-plugin-windows.yaml
    ```

3.  Run the following command to query the log of the **gpu-job-windows** application:

    ```
    kubectl logs -f gpu-job-windows
    ```

    Expected output:

    ```
    INFO: Executing model of "tinyyolov2-7" 100 times within GPU driver ...
    
    Created LearningModelDevice with GPU: NVIDIA GRID T4-8Q
    Loading model (path = c:\data\tinyyolov2-7\model.onnx)...
    =================================================================
    Name: Example Model
    Author: OnnxMLTools
    Version: 0
    Domain: onnxconverter-common
    Description: The Tiny YOLO network from the paper 'YOLO9000: Better, Faster, Stronger' (2016), arXiv:1612.08242
    Path: c:\data\tinyyolov2-7\model.onnx
    Support FP16: false
    
    Input Feature Info:
    Name: image
    Feature Kind: Image (Height: 416, Width:  416)
    
    Output Feature Info:
    Name: grid
    Feature Kind: Float
    ```

    The output shows that GPU acceleration is enabled for the **gpu-job-windows** application.


