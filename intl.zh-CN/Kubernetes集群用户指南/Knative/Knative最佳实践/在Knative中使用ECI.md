---
keyword: [Knative, ECI, 按需使用资源]
---

# 在Knative中使用ECI

通过Knative和虚拟节点组件使用弹性容器实例ECI（Elastic Container Instance），可以实现按需使用资源。本文介绍两种方式说明如何在Knative中使用ECI。

-   [一键部署Knative](/intl.zh-CN/Kubernetes集群用户指南/Knative/Knative组件管理/一键部署Knative.md)
-   [步骤一：在ACK集群中部署ack-virtual-node组件](/intl.zh-CN/Kubernetes集群用户指南/弹性容器实例ECI/通过部署ACK虚拟节点组件创建ECI Pod.md)
-   [通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)

## 方式一：Knative中直接使用ECI

-   如果没有在namespace中配置Virtual Kubelet的标签，您只需在创建Knative Service资源对象时，定义以下配置直接创建ECI Pod：

    -   指定annotation规格。
    -   在节点亲和性nodeAffinity中指定virtual-kubelet。
    -   设置节点容忍标签为virtual-kubelet.io/provider。
    示例配置模板如下。

    ```
    apiVersion: serving.knative.dev/v1
    kind: Service
    metadata:
      name: helloworld-go
      namespace: vk
    spec:
      template:
        metadata:
          annotations:
            k8s.aliyun.com/eci-use-specs: "2-4Gi"
        spec:
          affinity:
            nodeAffinity:
              requiredDuringSchedulingIgnoredDuringExecution:
                nodeSelectorTerms:
                - matchExpressions:
                  - key: type
                    operator: In
                    values: 
                    - virtual-kubelet   
          tolerations:
          - key: virtual-kubelet.io/provider
            operator: Exists                
          containers:
          - env:
            - name: TARGET
              value: "Knative"
            image: registry.cn-hangzhou.aliyuncs.com/knative-sample/helloworld-go:73fbdd56
    ```

-   如果在namespace中配置了以下Virtual Kubelet标签，您只需配置annotation指定ECI规格，然后直接创建ECI Pod。

    ```
    kubectl label namespace vk virtual-node-affinity-injection=enabled
    ```

    示例模板如下。

    ```
    apiVersion: serving.knative.dev/v1
    kind: Service
    metadata:
      name: helloworld-go
      namespace: vk
    spec:
      template:
        metadata:
          annotations:
            k8s.aliyun.com/eci-use-specs: "2-4Gi"
        spec:         
          containers:
          - env:
            - name: TARGET
              value: "Knative"
            image: registry.cn-hangzhou.aliyuncs.com/knative-sample/helloworld-go:73fbdd56
    ```


## 方式二：Knative中指定ECS节点且支持自动创建ECI

通过设置节点亲和性nodeAffinity， 在Knative中可以指定添加标签的ECS节点，且Knative支持自动创建ECI，实现同一个服务的Pod既可以部署在ECS中，也可以部署在ECI中。

1.  给ECS节点添加标签。具体步骤请参见[管理节点标签](/intl.zh-CN/Kubernetes集群用户指南/节点与节点池/节点/管理节点标签.md)。

    本文给ECS节点添加标签示例为：resource-role:ecs。

2.  在Service资源对象模板中，配置节点亲和性nodeAffinity。

    通过配置nodeAffinity，指定添加标签的ECS和Virtual Kubelete。

    示例模板如下。

    ```
    apiVersion: serving.knative.dev/v1
    kind: Service
    metadata:
      name: helloworld-go
    spec:
      template:
        metadata:
          annotations:
            k8s.aliyun.com/eci-use-specs: "2-4Gi"
       spec:
          containerConcurrency: 10
          affinity:
            nodeAffinity:
              requiredDuringSchedulingIgnoredDuringExecution:
                nodeSelectorTerms:
                - matchExpressions:
                  - key: resource-role
                    operator: In
                    values:
                    - ecs
                - matchExpressions:
                  - key: type
                    operator: In
                    values: 
                    - virtual-kubelet
          tolerations:
          - key: virtual-kubelet.io/provider
            operator: Exists                 
          containers:
          - env:
            - name: TARGET
              value: "Knative"
            image: registry.cn-hangzhou.aliyuncs.com/knative-sample/helloworld-go:73fbdd56
    ```


执行以下命令，可以看到成功创建2个ECI Pod。

```
kubectl -n vk get pod -o wide
```

预期输出：

```
NAME                                              READY   STATUS    RESTARTS   AGE   IP             NODE                 NOMINATED NODE   READINESS GATES
helloworld-go-dqqhv-deployment-6d54c9c8dc-hkjwn   2/2     Running   0          40s   192.1xx.x.xx   virtual-node-eci-0   <none>           <none>
```

