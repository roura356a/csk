---
keyword: ECI弹性调度
---

# 使用ECI弹性调度

ECI弹性调度是阿里云提供的弹性调度策略。您可以在部署服务时通过添加Annotations来申明只使用ECS或ECI弹性资源，或者是在ECS资源不足时自动申请ECI资源。通过ECI弹性调度可以满足您在不同工作负载的场景下对弹性资源的不同需求。

-   已创建ACK Pro版集群，且集群的Kubernetes版本不低于1.18。具体操作步骤，请参见[创建ACK Pro版集群](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建ACK Pro版集群.md)。
-   已在ACK Pro版集群中部署**ack-virtual-node**。具体操作，请参见[ACK使用ECI]()。

## 操作步骤

**说明：** 您可以通过添加Annotations来申明弹性资源的类型。`alibabacloud.com/burst-resource`的取值有以下选择：

-   默认不填Annotations时：只使用集群现有的ECS资源。
-   **eci**：当前集群ECS资源不足时，使用ECI弹性资源。
-   **eci\_only**: 只使用ECI弹性资源，不使用集群的ECS资源。

1.  创建并拷贝以下内容到nginx-deployment.yaml文件中。

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      replicas: 4
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          name: nginx
          annotations:
            alibabacloud.com/burst-resource: eci #添加注解，选择弹性调度的资源类型。
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx
            resources:
              limits:
                cpu: 2
              requests:
                cpu: 2
    ```

2.  执行以下命令，创建使用ECI弹性调度的Pod。

    ```
    kubectl apply -f nginx-deployment.yaml
    ```


