---
keyword: [指定容器规格, 指定Pod规格, 创建ECI Pod]
---

# 通过指定CPU和内存创建ECI Pod

在创建ECI Pod时，您可以指定单个容器或ECI Pod的CPU与内存规格。本文分别介绍如何指定ECI实例内容器规格和ECI Pod规格。

## 指定ECI实例内容器规格

通过Kubernetes标准方式配置单个容器的CPU和内存，ECI的资源则是Pod内所有容器所需资源的总和。

每个ECI实例支持最多20个容器，实例内每个容器的资源支持自定义配置，但汇总到ECI实例级别需要满足CPU和内存约束。

-   对于未满足的情况，ECI会执行自动规整操作，计费按照规整后CPU和内存值进行计费。
-   对于大于4 vCPU的情况，为了减少因操作失误造成规整，浪费计费资源，您需要严格声明规格资源，否则接口会返回规格非法错误。

ECI Pod支持的CPU和内存规格，如下表所示。

|vCPU|内存|
|----|--|
|0.25 vCPU|0.5 GB, 1 GB|
|0.5 vCPU|1 GB, 2 GB|
|1 vCPU|2 GB, 4 GB, 8 GB|
|2 vCPU|2 GB, 4 GB, 8 GB, 16 GB|
|4 vCPU|4 GB, 8 GB, 16 GB, 32 GB|
|8 vCPU|8 GB, 16 GB, 32 GB, 64 GB|
|12 vCPU|12 GB, 24 GB, 48 GB, 96 GB|
|16 vCPU|16 GB, 32 GB, 64 GB, 128 GB|
|24 vCPU|48 GB, 96 GB, 192 GB|
|32 vCPU|64 GB, 128 GB, 256 GB|
|52 vCPU|96 GB, 192 GB, 384 GB|
|64 vCPU|128 GB, 256 GB, 512 GB|

使用示例

基于Kubernetes原生方式，直接定义对应`container`的`request`即可。

```
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: nginx
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
        resources:
          requests:
            cpu: "500m"
            memory: "1024Mi"
      - name: busybox
        image: busybox:latest
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: "500m"
            memory: "1024Mi"
```

## 指定ECI Pod规格

在指定ECI Pod级别的CPU和内存模式下，对于指定的CPU和内存规格，ECI会尝试使用多种ECS规格进行支撑，以提供比ECS单规格更好的库存和弹性能力。这具有以下优势：

-   ECI实例内容器可以不用限制资源上限。在定义实例内容器资源时，可以不用指定`request`和`limit`，各容器可以最大程度的共享申请的资源。
-   在基因计算和Istio场景下，业务框架会自动给Pod添加Sidecar容器。通过显式指定ECI实例规格，ECI可以无缝的对接这类业务框架。

使用示例

通过在Pod定义中设置annotations: k8s.aliyun.com/eci-use-specs，可以配置多个规格，以逗号分割。

```
apiVersion: apps/v1beta2
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
      annotations:
        k8s.aliyun.com/eci-use-specs : "2-4Gi"
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
      - name: busybox
        image: busybox:latest
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: "500m"
            memory: "1024Mi"
```

