---
keyword: AMD instances
---

# Use AMD instances

AMD instances are built on the SHENLONG architecture and offloads virtualization features to dedicated hardware. This provides predictable and consistent ultra-high performance and reduces virtualization overheads. This topic describes how to use AMD instances.

## Scenarios

-   Video encoding and decoding
-   Receiving and sending large numbers of network packets
-   Web frontend servers
-   Frontend servers of massively multiplayer online \(MMO\) games
-   Application testing and development, such as DevOps

## Specifications

Elastic Container Instance allows you to specify an Elastic Compute Service \(ECS\) instance type to create AMD instances. The **c6a** compute-optimized instance family is supported. For example, you can specify the `ecs.c6a.large` instance type. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md).

## Usage notes

Add `annotations: k8s.aliyun.com/eci-use-specs` to the pod configuration.

Add the following `annotations` to the `metadata` field of the pod configuration to specify the instance type.

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
        k8s.aliyun.com/eci-use-specs : "ecs.c6a.xlarge"  #Replace the value with the required ECS instance type.
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```

