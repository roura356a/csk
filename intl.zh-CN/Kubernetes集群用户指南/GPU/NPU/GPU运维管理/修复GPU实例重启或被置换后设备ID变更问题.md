---
keyword: [GPU实例宕机, GPU设备ID变更, 重启或置换GPU实例]
---

# 修复GPU实例重启或被置换后设备ID变更问题

GPU实例宕机后，GPU设备ID可能会变化，会导致容器无法正常启动。GPUOps检测GPU实例的GPU设备ID与/var/lib/kubelet/device-plugins/kubelet\_internal\_checkpoint中存储的GPU设备ID是否一致。如果不一致，GPUOps会删除`checkpoint`文件，由Kubelet生成新的`checkpoint`文件，保证存储的GPU设备ID与真实的GPU设备ID一致。本文介绍如何修复由GPU实例宕机引起的设备ID变更问题。

-   [创建托管GPU集群](/intl.zh-CN/Kubernetes集群用户指南/GPU/NPU/创建异构计算集群/创建托管GPU集群.md)或[创建专有GPU集群](/intl.zh-CN/Kubernetes集群用户指南/GPU/NPU/创建异构计算集群/创建专有GPU集群.md)。
-   集群有一台启动SSH，且公网可访问的跳板机。具体操作，请参见[如何为已有集群开启SNAT](/intl.zh-CN/Kubernetes集群用户指南/网络/容器网络CNI/为已有集群开启SNAT公网访问能力.md)。

-   GPU实例宕机后，重启或置换GPU实例时，GPU设备ID可能会变化。该ID如果与/var/lib/kubelet/device-plugins/kubelet\_internal\_checkpoint中存储的GPU设备ID信息不一致，会导致容器无法正常启动。

    **说明：** 以下场景都有可能引起GPU设备ID变化：

    -   GPU实例宕机后，重启或置换GPU实例时。
    -   手动重启GPU实例时。
-   GPUOps是一个可执行的二进制程序，在Linux上可直接执行。具体下载地址，请参见[GPUOps](http://tpsservice-files-inner.cn-hangzhou.oss-cdn.aliyun-inc.com/files/resources/124309/ec31624aaa468726c71536a0e620fad3.zip)。
-   一个GPU实例上可以有多个GPU卡，每个GPU卡都有一个GPU设备ID。

## 步骤一：部署GPUOps

**单机部署GPUOps**

1.  执行以下命令，将GPUOps复制到/usr/local/bin/目录下，并赋予可执行权限。

    ```
    cp ./gpuops /usr/local/bin/
    chmod +x gpuops
    ```

2.  执行以下命令，把GPUOps添加到开机自动启动的服务。

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


**批量部署GPUOps**

在GPU集群中，使用以下YAML文件样例部署DaemonSet。

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

成功部署该DaemonSet后，GPU集群会自动在所有Worker节点执行以下操作：

-   将GPUOps复制到/usr/local/bin/目录下，并赋予可执行权限。
-   把GPUOps添加到开机自动启动的服务。

以后集群中的GPU实例宕机后，重启或置换GPU实例时，GPUOps会保证存储的GPU设备ID与真实的GPU设备ID一致。

## 步骤二：验证GPUOps修复GPU设备ID变更情况

1.  GPUOps安装完成后，在GPU集群部署1个Pod。

    部署Pod的YAML文件样例如下：

    ```
    apiVersion: apps/v1
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

2.  执行以下命令，验证Pod部署情况。

    ```
    kubectl get pod
    ```

    预期输出：

    ```
    NAME                                  READY   STATUS       RESTARTS   AGE
    app-3g-v1-0                           1/1     Running      0          22s
    ```

3.  登录到该GPU实例，执行以下命令，重启GPU节点。

    **说明：** 关于如何登录到GPU实例，请参见[通过密码认证登录Linux实例](/intl.zh-CN/实例/连接实例/使用VNC连接实例/通过密码认证登录Linux实例.md)。

    ```
    sudo reboot
    ```

4.  执行以下命令，查看GPUOps的执行日志信息。

    ```
    journalctl -u gpuops
    ```

    -   如果输出的日志内容如下，说明此时`checkpoint`文件中的GPU设备ID和真实的GPU设备ID是一致的，无需处理。

        ```
        6月 28 14:49:00 iZ2vc1mysgx8bqdv3oyji9Z gpuops[21976]: {"level":"info","msg":"check gpu start...","time":"2020-06-28T14:49:00+08:00"}
        6月 28 14:49:00 iZ2vc1mysgx8bqdv3oyji9Z gpuops[21976]: {"level":"info","msg":"find nvidia gpu: [\"GPU-383cd6a5-00a6-b455-62c4-c163d164b837\"]","time":"2020-06-28T14:49:00+08:00"}
        6月 28 14:49:00 iZ2vc1mysgx8bqdv3oyji9Z gpuops[21976]: {"level":"info","msg":"read checkpoint: {\"Data\":{\"PodDeviceEntries\":[{\"PodUID\":\"300f47eb-e5c3-4d0b-9a87-925837b67732\",\"ContainerName\":\"app-3g-v1\",\"ResourceName\":\"aliyun.com/g
        6月 28 14:49:00 iZ2vc1mysgx8bqdv3oyji9Z systemd[1]: Started Gpuops: check kubelet checkpoint gpu status.
        6月 28 14:49:00 iZ2vc1mysgx8bqdv3oyji9Z gpuops[21976]: {"level":"info","msg":"find registered gpu: [\"GPU-383cd6a5-00a6-b455-62c4-c163d164b837\"]","time":"2020-06-28T14:49:00+08:00"}
        6月 28 14:49:00 iZ2vc1mysgx8bqdv3oyji9Z gpuops[21976]: {"level":"info","msg":"find pod gpu: null","time":"2020-06-28T14:49:00+08:00"}
        6月 28 14:49:00 iZ2vc1mysgx8bqdv3oyji9Z gpuops[21976]: {"level":"info","msg":"cached gpu info in checkpoint is up to date, check gpu finished","time":"2020-06-28T14:49:00+08:00"}
        ```

    -   如果输出的日志有以下`warning`级别的日志时，则说明GPU设备ID发生了变化，GPUOps会删除`checkpoint`，Kubernetes会重新生成`checkpoint`文件，保证设备信息同步。

        ```
        6月 28 14:41:16 iZ2vc1mysgx8bqdv3oyji9Z systemd[1]: Starting Gpuops: check kubelet checkpoint gpu status...
        6月 28 14:41:16 iZ2vc1mysgx8bqdv3oyji9Z gpuops[951]: {"level":"info","msg":"check gpu start...","time":"2020-06-28T14:41:16+08:00"}
        6月 28 14:41:17 iZ2vc1mysgx8bqdv3oyji9Z gpuops[951]: {"level":"info","msg":"find nvidia gpu: [\"GPU-68ce64fb-ea68-5ad6-7e72-31f3f07378df\"]","time":"2020-06-28T14:41:17+08:00"}
        6月 28 14:41:17 iZ2vc1mysgx8bqdv3oyji9Z gpuops[951]: {"level":"info","msg":"read checkpoint: {\"Data\":{\"PodDeviceEntries\":[{\"PodUID\":\"300f47eb-e5c3-4d0b-9a87-925837b67732\",\"ContainerName\":\"app-3g-v1\",\"ResourceName\":\"aliyun.com/gpu
        6月 28 14:41:17 iZ2vc1mysgx8bqdv3oyji9Z gpuops[951]: {"level":"info","msg":"find registered gpu: [\"GPU-7fd52bfc-364d-1129-a142-c3f10e053ccb\"]","time":"2020-06-28T14:41:17+08:00"}
        6月 28 14:41:17 iZ2vc1mysgx8bqdv3oyji9Z gpuops[951]: {"level":"info","msg":"find pod gpu: [\"GPU-7fd52bfc-364d-1129-a142-c3f10e053ccb\"]","time":"2020-06-28T14:41:17+08:00"}
        6月 28 14:41:17 iZ2vc1mysgx8bqdv3oyji9Z gpuops[951]: {"level":"warning","msg":"the registered gpu uuid not equal with nvidia gpu","time":"2020-06-28T14:41:17+08:00"}
        6月 28 14:41:17 iZ2vc1mysgx8bqdv3oyji9Z gpuops[951]: {"level":"warning","msg":"cached gpu info in checkpoint is out of date","time":"2020-06-28T14:41:17+08:00"}
        6月 28 14:41:17 iZ2vc1mysgx8bqdv3oyji9Z gpuops[951]: {"level":"info","msg":"delete checkpoint file success","time":"2020-06-28T14:41:17+08:00"}
        6月 28 14:41:17 iZ2vc1mysgx8bqdv3oyji9Z gpuops[951]: {"level":"info","msg":"kubelet restart success","time":"2020-06-28T14:41:17+08:00"}
        6月 28 14:41:17 iZ2vc1mysgx8bqdv3oyji9Z gpuops[951]: {"level":"info","msg":"check gpu finished","time":"2020-06-28T14:41:17+08:00"}
        6月 28 14:41:17 iZ2vc1mysgx8bqdv3oyji9Z systemd[1]: Started Gpuops: check kubelet checkpoint gpu status.
        ```

        -   执行以下命令，查看真实的GPU设备ID。

            ```
            nvidia-smi -L
            ```

            预期输出：

            ```
            GPU 0: Tesla T4 (UUID: GPU-0650a168-e770-3ea8-8ac3-8a1d419763e0)
            ```

        -   执行以下命令，查看`checkpoint`文件里存储的GPU设备ID。

            ```
            cat /var/lib/kubelet/device-plugins/kubelet_internal_checkpoint
            ```

            预期输出：

            ```
            {"Data":{"PodDeviceEntries":null,"RegisteredDevices":{"nvidia.com/gpu":["GPU-0650a168-e770-3ea8-8ac3-8a1d419763e0"]}},"Checksum":3952659280}
            ```

            从上述输出信息，可知`checkpoint`文件里存储的GPU设备ID与真实的GPU设备ID一致，说明GPUOps成功修复了设备ID不一致的问题。


