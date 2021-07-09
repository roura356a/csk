---
keyword: [Windows容器, GPU加速, DirectX]
---

# 在Windows容器中使用基于DirectX的GPU加速

对于Windows节点的工作负载，GPU相比于CPU可提供更大规模的并行计算能力，且能够将操作速度提高几个数量级，从而降低成本并提高吞吐量。Windows容器支持对基于DirectX构建的所有框架进行GPU加速。本文介绍在Windows节点如何安装DirectX设备插件以及在Windows容器中如何使用基于DirectX构建的GPU加速功能。

-   已创建Kubernetes集群，且集群版本不低于v1.20.4。具体操作，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
    -   Windows节点仅支持部署Docker及Containerd两种容器运行时。更多信息，请参见[如何选择容器运行时？](/cn.zh-CN/Kubernetes集群用户指南/安全沙箱/如何选择Docker运行时、Containerd运行时、或者安全沙箱运行时？.md)。
    -   Windows节点的实例规格必须是GPU虚拟化型且需要使用已激活License的GRID驱动的ECS镜像。更多信息，请参见[GPU虚拟化型]()。
-   已创建Windows节点池。具体操作，请参见[创建Windows节点池](/cn.zh-CN/Kubernetes集群用户指南/Windows容器/创建Windows节点池.md)。
-   已通过kubectl连接ACK集群。具体操作，请参见[通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl管理Kubernetes集群.md)。

DirectX（Direct eXtension，简称DX）是一种应用程序接口（API）。DirectX可以使以Windows为平台的游戏和多媒体程序获得更高的执行效率，加强3D图形和声音效果，并向设计人员提供一个共同的硬件驱动标准，降低安装及设置硬件的复杂度。DirectX可以允许GPU从事更多的通用计算工作，同时减轻过载，鼓励开发人员更好地将GPU作为并行处理器使用。

## 步骤一：为Windows节点安装DirectX设备插件

将DirectX设备插件以DaemonSet方式部署到Windows节点上。

1.  使用以下内容创建directx-device-plugin-windows.yaml文件。

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
              # 根据不同集群的地域，您需修改以下镜像地址中的地域<cn-hangzhou>信息。
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

2.  执行以下命令安装DirectX设备插件。

    ```
    kubectl create -f directx-device-plugin-windows.yaml
    ```


## 步骤二：部署使用基于DirectX的GPU加速的Windows工作负载

DirectX设备插件可以为Windows容器自动添加`class/<interface class GUID>`设备，以支持调用ECS实例主机的DirectX服务。更多信息，请参见[Windows上的容器中的设备](https://docs.microsoft.com/zh-cn/virtualization/windowscontainers/deploy-containers/hardware-devices-in-containers)。在需要使用GPU加速的Windows工作负载内添加以下resources资源信息并重新部署：

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

**说明：** 上述配置不会将整个ECS实例主机的GPU资源专门分配给容器，也不会阻止ECS实例主机上的GPU被别的应用访问。相反，GPU资源在ECS实例主机和容器之间动态调度，这表示您可以在主机上运行多个Windows容器，且每个容器都可以使用支持硬件加速的DirectX功能。

关于Windows容器中的GPU加速的更多信息，请参见[Windows容器中的GPU加速](https://docs.microsoft.com/zh-cn/virtualization/windowscontainers/deploy-containers/gpu-acceleration)。

## 验证在Windows工作负载是否成功使用GPU加速功能

在Windows节点上添加DirectX设备插件后，使用以下示例应用验证DirectX设备插件是否成功部署到Windows节点上。

1.  使用以下内容创建gpu-job-windows.yaml文件。

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
              # 根据不同集群的地域，您需修改以下镜像地址中的地域<cn-hangzhou>信息。
              image: registry-vpc.cn-hangzhou.aliyuncs.com/acs/sample-gpu-windows:v1.0.0
              imagePullPolicy: IfNotPresent
              resources:
                limits:
                  windows.alibabacloud.com/directx: "1"
                requests:
                  windows.alibabacloud.com/directx: "1"
    ```

    **说明：**

    -   镜像`registry-vpc.{region}.aliyuncs.com/acs/sample-gpu-windows`是阿里云容器服务提供的Windows GPU加速容器示例镜像（该镜像基于microsoft-windows制作。更多信息，请参见[mcr.microsoft.com/windows](https://hub.docker.com/_/microsoft-windows)）。
    -   因镜像文件较大（文件大小为15.3 GB），所以在部署应用时拉取镜像需要较长时间，请耐心等待。
    -   该示例通过[WinMLRunner](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Tools/WinMLRunner)产生模拟输入数据，对`gpu-job-windows`任务使用GPU加速后，通过[Tiny YOLOv2](https://github.com/onnx/models/tree/master/vision/object_detection_segmentation/tiny-yolov2)模型进行100次评估，最终输出相应的性能测量数据。
2.  执行以下命令创建示例应用。

    ```
    kubectl create -f directx-device-plugin-windows.yaml
    ```

3.  执行以下命令查看示例应用**gpu-job-windows**的日志信息。

    ```
    kubectl logs -f gpu-job-windows
    ```

    预期输出：

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

    从预期输出可得，在示例应用**gpu-job-windows**中已成功使用GPU加速功能。


