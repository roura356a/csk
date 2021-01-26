---
keyword: [Service流量拓扑, 边缘计算]
---

# 配置Service流量拓扑

原生Kubernetes Service的后端端点扁平分布在集群中任意节点。因此，跨跃不同分组节点的Service流量，会大概率出现访问不可达、或者访问效率低下的问题。Service流量拓扑支持边缘节点应用只能由相同节点池的节点访问，或者只能由本节点访问。本文为您介绍Service流量拓扑管理功能和配置Service流量拓扑方法。

在边缘计算场景下，边缘节点通常具备很强的区域性、地域性、或者其他逻辑上的分组特性（比如具有相同的CPU架构、运营商或云提供商），不同分组的节点间往往存在网络不互通、资源不共享、资源异构、应用独立等明显的隔离属性。

## Service流量拓扑管理实现原理

为了解决上述问题，ACK@Edge在原生的Service之上，增加了Endpoint的拓扑管理功能，即通过简单配置来限制Service后端Endpoint的访问范围。例如边缘节点应用只能由相同节点池的节点访问，或者只能由本节点访问。具体实现原理如下图所示。

![G-8](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6082221161/p224391.png)

-   Service1关联后端Pod2和Pod3两个实例，且Service1通过`annotation："openyurt.io/topologyKeys: kubernetes.io/zone"`配置了其拓扑节点池范围。
-   Pod2所在的节点2和Pod3所在的节点3分别属于两个不同的节点池A和节点池B。
-   因为Pod3和Pod1不在一个节点池，当Pod1访问Service1时，流量只会转发到Pod2上，访问Po3的流量被限制。

## 方式一：通过控制台配置Service拓扑

若您需要创建一个限制在本节点池内被访问的Service，只需要给Service添加注解即可。例如将**名称**配置为`openyurt.io/topologyKeys`，**值**配置为`kubernetes.io/zone`。关于创建服务的更多信息，请参见[管理服务](/intl.zh-CN/Kubernetes集群用户指南/网络管理/Service管理/管理服务.md)。

![G-9](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5438211161/p224982.png)

## 方式二：通过命令行配置Service拓扑

通过命令行配置Service拓扑有以下两种方式：

-   新建一个使用节点池拓扑域的Service，YAML样例如下。

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        openyurt.io/topologyKeys: kubernetes.io/zone
      name: my-service-nodepool
      namespace: default
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 8080
      selector:
        app: nginx
      sessionAffinity: None
      type: ClusterIP
    ```

-   通过执行以下命令行配置Service拓扑，该Service使用的是节点池拓扑域。

    ```
    kubectl annotate service xxx openyurt.io/topologyKeys='kubernetes.io/zone'
    ```


## 注解说明

通过在原生的Service上添加Annotation实现流量的拓扑配置，相关参数如下所示。

|annotation Key|annotation Value|说明|
|--------------|----------------|--|
|openyurt.io/topologyKeys|kubernetes.io/hostname|限制Service只能被本节点访问。|
|openyurt.io/topologyKeys|kubernetes.io/zone|限制Service只能被本节点池的节点访问可。|
|-|-|Service不做任何拓扑限制。|

