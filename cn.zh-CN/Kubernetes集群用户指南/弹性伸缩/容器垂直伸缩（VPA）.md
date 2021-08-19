---
keyword: [kubernetes, 垂直伸缩, VPA \(vertical-pod-autoscaler\)]
---

# 容器垂直伸缩（VPA）

通过在ACK集群上部署安装VPA（vertical-pod-autoscaler），ACK可以提供垂直的容器伸缩的功能。VPA会基于Pod的资源使用情况自动为集群设置资源占用的限制，从而让集群将Pod调度到有足够资源的最佳节点上。VPA也会保持最初容器定义中资源request和limit的占比。本文介绍如何配置YAML实现容器的垂直伸缩。

请确保您已完成以下操作：

-   已创建一个Kubernetes集群，且ACK Kubernetes版本高于1.12。具体操作请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   已使用命令行工具连接集群。具体操作请参见[t16645.md\#](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl工具连接集群.md)。
-   已卸载集群中已经部署的VPA，以避免新安装的VPA与旧版VPA冲突而导致VPA不可用。

**说明：** 容器垂直伸缩功能目前处于试验阶段，请谨慎使用。

-   更新正在运行的Pod资源配置是VPA的一项试验性功能，会导致Pod的重建和重启，而且有可能被调度到其他的节点上。
-   VPA不会驱逐没有在副本控制器管理下的Pod。目前对于这类Pod，Auto模式等同于Initial模式。
-   目前VPA不能和监控CPU和内存度量的Horizontal Pod Autoscaler （HPA）同时运行，除非HPA只监控其他定制化的或者外部的资源度量。
-   VPA使用admission webhook作为其准入控制器。如果集群中有其他的admission webhook，需要确保它们不会与VPA发生冲突。准入控制器的执行顺序定义在API Server的配置参数中。
-   VPA会处理出现的绝大多数OOM（Out Of Memory）的事件，但不保证所有的场景下都有效。
-   VPA的性能还没有在大型集群中测试过。
-   VPA对Pod资源requests的修改值可能超过实际的资源上限，例如节点资源上限、空闲资源或资源配额，从而造成Pod处于Pending状态无法被调度。同时使用集群自动伸缩（ClusterAutoscaler）可以一定程度上解决这个问题。
-   多个VPA同时匹配同一个Pod会造成未定义的行为。

## 安装vertical-pod-autoscaler

1.  通过以下命令创建vertical-pod-autoscaler的CRD。

    CRD可以提高Kubernetes的扩展能力，详情请参见[Extend the Kubernetes API with CustomResourceDefinitions](https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/)。

    ```
    kubectl apply -f crd.yaml
    ```

    以下为crd.yaml的定义。

    ```
    ---
    apiVersion: apiextensions.k8s.io/v1beta1
    kind: CustomResourceDefinition
    metadata:
      name: verticalpodautoscalers.autoscaling.k8s.io
      annotations:
        "api-approved.kubernetes.io": "https://github.com/kubernetes/kubernetes/pull/63797"
    spec:
      group: autoscaling.k8s.io
      scope: Namespaced
      names:
        plural: verticalpodautoscalers
        singular: verticalpodautoscaler
        kind: VerticalPodAutoscaler
        shortNames:
          - vpa
      version: v1beta1
      versions:
        - name: v1beta1
          served: false
          storage: false
        - name: v1beta2
          served: true
          storage: true
        - name: v1
          served: true
          storage: false
      validation:
        # openAPIV3Schema is the schema for validating custom objects.
        openAPIV3Schema:
          type: object
          properties:
            spec:
              type: object
              required: []
              properties:
                targetRef:
                  type: object
                updatePolicy:
                  type: object
                  properties:
                    updateMode:
                      type: string
                resourcePolicy:
                  type: object
                  properties:
                    containerPolicies:
                      type: array
                      items:
                        type: object
                        properties:
                          containerName:
                            type: string
                          controlledValues:
                            type: string
                            enum: ["RequestsAndLimits", "RequestsOnly"]
                          mode:
                            type: string
                            enum: ["Auto", "Off"]
                          minAllowed:
                            type: object
                          maxAllowed:
                            type: object
                          controlledResources:
                            type: array
                            items:
                              type: string
                              enum: ["cpu", "memory"]
    ---
    apiVersion: apiextensions.k8s.io/v1beta1
    kind: CustomResourceDefinition
    metadata:
      name: verticalpodautoscalercheckpoints.autoscaling.k8s.io
      annotations:
        "api-approved.kubernetes.io": "https://github.com/kubernetes/kubernetes/pull/63797"
    spec:
      group: autoscaling.k8s.io
      scope: Namespaced
      names:
        plural: verticalpodautoscalercheckpoints
        singular: verticalpodautoscalercheckpoint
        kind: VerticalPodAutoscalerCheckpoint
        shortNames:
          - vpacheckpoint
      version: v1beta1
      versions:
        - name: v1beta1
          served: false
          storage: false
        - name: v1beta2
          served: true
          storage: true
        - name: v1
          served: true
          storage: false
    ```

2.  安装vertical-pod-autoscaler的组件。

    vertical-pod-autoscaler的组件包括：admission-controller、recommender、updater。




## 验证安装VPA

1.  使用以下YAML定义创建名为nginx-deployment-basic的Deployment和名为nginx-deployment-basic-vpa的VPA资源。

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-deployment-basic
      labels:
        app: nginx
    spec:
      replicas: 2
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:1.7.9
            ports:
            - containerPort: 80
    ---
    apiVersion: autoscaling.k8s.io/v1
    kind: VerticalPodAutoscaler
    metadata:
      name: nginx-deployment-basic-vpa
    spec:
      targetRef:
        apiVersion: "apps/v1"
        kind:       Deployment
        name:       nginx-deployment-basic
      updatePolicy:
        updateMode: "Off"
    ```

    **说明：** 设置updateMode为Off，并且将Deployment中的资源requests和limits留空。

2.  执行以下命令可以查询VPA为Deployment推荐的CPU和内存资源的requests值。

    ```
    kubectl describe vpa nginx-deployment-basic-vpa
    ```

    执行命令后可以看到以下VPA为Deployment推荐的值。

    ```
    recommendation:
        containerRecommendations:
        - containerName: nginx
          lowerBound:
            cpu: 50m
            memory: 300144k
          target:
            cpu: 50m
            memory: 300144k
          upperBound:
            cpu: 8031m
            memory: 800000k
    ```

    您可以根据推荐值来实际配置Deployment中资源的requests。VPA会持续的监控应用资源的使用情况，并提供优化建议。


**相关文档**  


[容器水平伸缩（HPA）](/cn.zh-CN/Kubernetes集群用户指南/弹性伸缩/容器水平伸缩（HPA）.md)

