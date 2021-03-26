---
keyword: 运行Job任务
---

# 通过ASK运行Job任务

在ASK集群中，您可以按需按量创建Pod。当Pod结束后停止收费，无需为Job任务预留计算资源，从而摆脱集群计算力不足和扩容的烦扰，同时结合抢占式实例可以降低Job任务的计算成本。本文主要为您介绍如何通过ASK按需创建Job任务。

-   [创建Serverless Kubernetes集群](/cn.zh-CN/Serverless Kubernetes集群用户指南/快速入门/创建Serverless Kubernetes集群.md)
-   [通过kubectl连接Kubernetes集群](/cn.zh-CN/Serverless Kubernetes集群用户指南/集群/管理和访问集群/通过kubectl连接Kubernetes集群.md)

## 操作步骤

1.  通过kubectl客户端创建job.yaml文件，并拷贝以下内容到该文件。

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: pi
    spec:
      template:
        spec:
          containers:
          - name: pi
            image: perl
            command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
            resources:
              requests:
                cpu: 16
                memory: 32Gi
          restartPolicy: Never
      backoffLimit: 4
    ```

2.  执行以下命令部署一个Job任务。

    ```
    kubectl apply -f job.yaml
    ```

3.  执行以下命令，查看Pod的运行状态。

    查看Pod的状态。

    ```
    kubectl get pod
    ```

    预期输出：

    ```
    NAME       READY   STATUS      RESTARTS   AGE
    pi-4f7w5   0/1     Completed   0          80s
    ```

    查看Pod的具体运行状态。

    ```
    kubectl describe pod
    ```

    预期输出：

    ```
    Name:               pi-4f7w5
    Namespace:          default
    Priority:           0
    PriorityClassName:  <none>
    Node:               virtual-kubelet-cn-hongkong-b/10.10.66.169
    ...
    Events:
      Type    Reason                 Age   From          Message
      ----    ------                 ----  ----          -------
      Normal  SuccessfulMountVolume  114s  kubelet, eci  MountVolume.SetUp succeeded for volume "default-token-8k4jz"
      Normal  Pulling                113s  kubelet, eci  pulling image "perl"
      Normal  Pulled                 64s   kubelet, eci  Successfully pulled image "perl"
      Normal  Created                64s   kubelet, eci  Created container
      Normal  Started                64s   kubelet, eci  Started container
    ```

4.  通过给Pod加上抢占式实例的Annotation，使用抢占式实例。

    关于抢占式实例Annotation的用法，请参见[使用抢占式实例](/cn.zh-CN/Serverless Kubernetes集群用户指南/ECI Pod/使用抢占式实例.md)。

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: pi
    spec:
      template:
        metadata:
          annotations:
            k8s.aliyun.com/eci-spot-strategy: SpotAsPriceGo
        spec:
          containers:
          - name: pi
            image: perl
            command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
            resources:
              requests:
                cpu: 16
                memory: 32Gi
          restartPolicy: Never
      backoffLimit: 4
    ```


