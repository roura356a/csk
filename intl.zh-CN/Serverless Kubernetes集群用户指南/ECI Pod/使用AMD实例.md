---
keyword: AMD实例
---

# 使用AMD实例

AMD实例依托神龙架构，将大量虚拟化功能卸载到专用硬件，降低虚拟化开销，提供稳定可预期的超高性能。本文介绍如何使用AMD实例。

## 适用场景

-   视频编解码
-   高网络包收发场景
-   Web前端服务器
-   大型多人在线游戏（MMO）前端
-   测试开发，例如DevOps

## 规格说明

ECI支持通过指定ECS AMD规格进行实例的创建，支持ECS计算型实例规格族**c6a**，例如：`ecs.c6a.large`。更多信息，请参见[实例规格族](/intl.zh-CN/实例/实例规格族.md)。

## 使用方法

Pod声明中增加`annotations: k8s.aliyun.com/eci-use-specs`。

在Pod的`metadata`中添加指定规格的`annotations`。

```
apiVersion: apps/v1
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
      annotations:
        k8s.aliyun.com/eci-use-specs : "ecs.c6a.xlarge"  #根据需要替换ECS规格
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```

