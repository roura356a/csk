---
keyword: [GPU拓扑感知调度, Kubernetes调度, 深度学习]
---

# 分布式训练场景下激活GPU拓扑感知调度

ACK基于Scheduling Framework机制，实现GPU拓扑感知调度，即在节点的GPU组合中选择具有最优训练速度的组合。本文主要介绍如何激活GPU拓扑感知调度，且举例说明未激活GPU拓扑感知调度的使用示例，让您有更好的用户体验。

## 应用限制

-   仅支持提交作业的所有Pod对资源（CPU、GPU等资源）请求一致的场景。
-   只有当提交作业的所有Pod对资源请求都满足条件时，才能创建Pod并启动作业，否则请求会处于资源等待状态。

## 节点配置

您需执行以下命令设置节点label，显式激活节点GPU拓扑感知调度。

```
kubectl label node <Your Node Name>  ack.node.gpu.schedule=topology
```

**说明：** 当节点激活GPU拓扑感知调度后，不再支持普通GPU资源调度。可执行以下命令更改label，恢复普通GPU资源调度功能。

```
kubectl label node <Your Node Name>  ack.node.gpu.schedule=default --overwrite
```

## 示例一：未激活GPU拓扑感知调度

**说明：** 测试以下用例需要预装[Arena](https://github.com/kubeflow/arena)或者单独部署[Pytorch-Operator](https://github.com/kubeflow/pytorch-operator)。

在Pytorch中未开启GPU拓扑感知调度的样例如下。

```
apiVersion: "kubeflow.org/v1"
kind: "PyTorchJob"
metadata:
  name: "pytorch-dist-nccl"
spec:
  pytorchReplicaSpecs:
    Master:
      replicas: 1
      restartPolicy: OnFailure
      template:
        metadata:
          annotations:
            sidecar.istio.io/inject: "false"
        spec:
          containers:
            - name: pytorch
              image: registry.cn-hangzhou.aliyuncs.com/kubernetes-image-hub/pytorch_dist_mnist:1.3-gpu-py3
              command:
              - python
              args: ["/opt/mnist/src/mnist.py", "--backend", "nccl"]
              resources: 
                limits:
                  nvidia.com/gpu: 1
              env:
                - name: NCCL_DEBUG
                  value: "INFO"
                - name: NCCL_DEBUG_SUBSYS
                  value: "GRAPH"
    Worker:
      replicas: 1
      restartPolicy: OnFailure
      template:
        metadata:
          annotations:
            sidecar.istio.io/inject: "false"
        spec:
          containers:
            - name: pytorch
              image: registry.cn-hangzhou.aliyuncs.com/kubernetes-image-hub/pytorch_dist_mnist:1.3-gpu-py3
              command:
              - python
              args: ["/opt/mnist/src/mnist.py", "--backend", "nccl"]
              resources: 
                limits:
                  nvidia.com/gpu: 1
              env:
                - name: NCCL_DEBUG
                  value: "INFO"
                - name: NCCL_DEBUG_SUBSYS
                  value: "GRAPH"
```

1.  创建作业后，执行以下命令检查作业创建情况。

    ```
    kubectl get pods
    ```

    ```
    NAME                         READY   STATUS      RESTARTS   AGE
    pytorch-dist-nccl-master-0   1/1     Running   0          1m29s
    pytorch-dist-nccl-worker-0   1/1     Running   0          1m29s
    ```

2.  执行以下命令查看日志，从下图可以看出不同Pod的GPU之间未使用NVL进行通信。

    ```
    kubectl logs -f pytorch-dist-nccl-master-0
    ```

    ![image 6](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5611026061/p183761.png)


## 示例二：激活GPU拓扑感知调度作业配置

在Pytorch中开启了GPU拓扑感知调度的样例如下。

```
apiVersion: "kubeflow.org/v1"
kind: "PyTorchJob"
metadata:
  name: "pytorch-dist-nccl"
spec:
  pytorchReplicaSpecs:
    Master:
      replicas: 1
      restartPolicy: OnFailure
      template:
        metadata:
          labels:
            gpu-topology: pytorch-dist-nccl # 标识分布式训练的Job名称。
            gpu-topology-replica: "2" # 标识分布式训练的副本总数。
          annotations:
            sidecar.istio.io/inject: "false"
        spec:
          hostIPC: true  # 开启hostIPC
          hostNetwork: true # 开启hostNetwork
          hostPID: true # 开启hostPID
          dnsPolicy: ClusterFirstWithHostNet # dnsPolicy设置为ClusterFirstWithHostNet。
          containers:
            - name: pytorch
              image: registry.cn-hangzhou.aliyuncs.com/kubernetes-image-hub/pytorch_dist_mnist:1.3-gpu-py3
              command:
              - python
              args: ["/opt/mnist/src/mnist.py", "--backend", "nccl"]
              resources: 
                limits:
                  aliyun.com/gpu: 1
    Worker:
      replicas: 1
      restartPolicy: OnFailure
      template:
        metadata:
          labels:
            gpu-topology: pytorch-dist-nccl # 标识分布式训练的Job名称。
            gpu-topology-replica: "2" # 标识分布式训练的副本总数。
          annotations:
            sidecar.istio.io/inject: "false"
        spec:
          hostIPC: true  # 开启hostIPC
          hostNetwork: true # 开启hostNetwork
          hostPID: true # 开启hostPID
          dnsPolicy: ClusterFirstWithHostNet # dnsPolicy设置为ClusterFirstWithHostNet。
          containers:
            - name: pytorch
              image: registry.cn-hangzhou.aliyuncs.com/kubernetes-image-hub/pytorch_dist_mnist:1.3-gpu-py3
              command:
              - python
              args: ["/opt/mnist/src/mnist.py", "--backend", "nccl"]
              resources: 
                limits:
                  aliyun.com/gpu: 1 # 资源请求改为aliyun.com/gpu。
```

1.  通过设置labels的形式配置gpu-topology和gpu-topology-replica的值，具体命令如下所示。

    ```
    labels:
        gpu-topology: pytorch-dist-nccl
        gpu-topology-replica: "2"
    ```

    -   gpu-topology： 表示分布式训练作业的名称。
    -   gpu-topology-replica：表示分布式训练Master和Worker的所有的副本总数。
2.  开启主机共享IPC、主机共享PID、共享主机网络权限，且将DNS配置为ClusterFirstWithHostNet，具体命令如下所示。

    ```
    spec:
      hostIPC: true 
      hostNetwork: true
      hostPID: true
      dnsPolicy: ClusterFirstWithHostNet
    ```

3.  将资源申请名称由nvidia.com/gpu改为aliyun.com/gpu，具体命令如下所示。

    ```
    resources: 
      limits:
        aliyun.com/gpu: 1
    ```

4.  作业创建结果验证。

    1.  执行以下命令检查作业创建情况。

        ```
        kubectl get pods
        ```

        ```
        NAME                         READY   STATUS    RESTARTS   AGE
        pytorch-dist-nccl-master-0   1/1     Running   0          55s
        pytorch-dist-nccl-worker-0   1/1     Running   0          55s
        ```

    2.  执行以下命令查看日志，从下图可以看出不同Pod的GPU之间使用NVL进行通信。

        ```
        kubectl logs -f pytorch-dist-nccl-master-0
        ```

        ![GPUNVL](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5611026061/p184817.png)


[背景概述](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/GPU拓扑感知调度/GPU拓扑感知调度背景概述.md)

[安装ack-ai-installer组件](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/GPU拓扑感知调度/安装ack-ai-installer组件.md)

