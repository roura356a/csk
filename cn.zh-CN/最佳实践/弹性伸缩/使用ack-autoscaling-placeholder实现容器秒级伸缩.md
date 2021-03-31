---
keyword: [ack-autoscaling-placeholder, 容器秒级伸缩]
---

# 使用ack-autoscaling-placeholder实现容器秒级伸缩

ack-autoscaling-placeholder为集群的自动扩展提供了缓冲区，它适用于工作负载需要快速启动而无需考虑节点资源不足的使用场景。本文介绍如何使用ack-autoscaling-placeholder实现容器秒级伸缩。

您已为ACK集群开通自动伸缩。开通自动伸缩的详情，请参见[节点自动伸缩](/cn.zh-CN/Kubernetes集群用户指南/弹性伸缩/节点自动伸缩.md)。

## 操作步骤

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  找到并单击**ack-autoscaling-placeholder**，设置目标命名集群和命名空间，然后单击**创建**。

4.  配置ack-autoscaling-placeholder。

    1.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

    2.  在集群管理页面的左侧导航栏中，选择**应用** \> **Helm**。

    3.  找到**ack-autoscaling-placeholder**，然后单击其右侧的**更新**。

        ```
        nameOverride: ""
        fullnameOverride: ""
        ##
        priorityClassDefault:
          enabled: true
          name: default-priority-class
          value: -1
        
        ##
        deployments:
           - name: ack-place-holder
             replicaCount: 1
             containers:
               - name: placeholder
                 image: registry-vpc.cn-shenzhen.aliyuncs.com/acs/pause:3.1
                 pullPolicy: IfNotPresent
                 resources:
                   requests:
                     cpu: 4                  #资源占位4C8G
                     memory: 8               
             imagePullSecrets: {}
             annotations: {}
             nodeSelector:                   #节点选择
               demo: "yes"  
             tolerations: []
             affinity: {}
             labels: {}
        ```

    4.  部署工作负载的PriorityClass。

        本文示例定义一个优先级较高的PriorityClass。

        ```
        kubectl apply -f priorityClass.yaml
        ```

        预期输出：

        ```
        apiVersion: scheduling.k8s.io/v1
        kind: PriorityClass
        metadata:
          name: high-priority
        value: 1000000              #配置优先级。
        globalDefault: false
        description: "This priority class should be used for XYZ service pods only."
        ```

    5.  部署实际的工作负载。

        ```
        kubectl apply -f workload.yaml
        ```

        ```
        apiVersion: apps/v1 
        kind: Deployment
        metadata:
          name: placeholder-test
          labels:
            app: nginx
        spec:
          replicas: 1
          selector:
            matchLabels:
              app: nginx
          template:
            metadata:
              labels:
                app: nginx
            spec:
              nodeSelector:                        #节点选择
                demo: "yes"
              priorityClassName: high-priority     #这里写入第3步配置的PriorityClass名称。
              containers:
              - name: nginx
                image: nginx:1.7.9 
                ports:
                - containerPort: 80
                resources:       
                  requests:      
                    cpu: 3                         #实际负载的资源需求。
                    memory: 5
        ```

        从下图可以看到，实际工作负载由于Pod配置了高优先级的PriorityClass。当节点资源不足时，会将占位容器placeHolder进行驱逐，此时占位容器placeHolder处于Pending状态。由于集群开通了自动伸缩，此状态会触发ACK集群进行扩容。实现了工作负载的**秒级弹出**。

        ![pending](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8102021061/p170802.png)

        ![run](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9102021061/p170803.png)


## 实现原理

使用优先级非常低（负数）的占位容器来超额配置，以保留其他Pod可以使用的资源。如果集群没有可用的资源，真正的工作负载也会将占位容器所占用的资源抢占，实现快速启动，然后结合同时使用Cluster-Autoscaler，迫使集群进行节点维度的扩展。

