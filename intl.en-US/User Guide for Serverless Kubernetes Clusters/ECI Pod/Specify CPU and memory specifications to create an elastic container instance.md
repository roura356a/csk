---
keyword: [specify container specifications, pod specifications, create elastic container instances]
---

# Specify CPU and memory specifications to create an elastic container instance

When you create an elastic container instance, you can specify CPU and memory specifications for the instance or for each container in the instance. This topic describes how to specify the container specifications and pod specifications of an elastic container instance.

## Specify container specifications for an elastic container instance

You can specify the CPU and memory specifications for each container. The amount of resources required by an elastic container instance refers to the total amount of resources required by all containers in the elastic container instance.

Each elastic container instance supports up to 20 containers. You can customize the resource specifications for each container. The total amount of resources that are specified for the elastic container instance cannot exceed the upper limits of CPU and memory resources.

-   If the total amount of resources that are specified for the elastic container instance exceeds the upper limits of CPU and memory resources, the elastic container instance automatically adjusts the CPU and memory resources as required. Resource fees are charged based on the adjusted CPU and memory resources.
-   For containers that are allocated more than four vCPUs, you must strictly declare the specifications of the CPU and memory resources to avoid resource allocation adjustment and waste of compute resources. Otherwise, an error message is returned, which indicates that the specifications are invalid.

The following table describes the supported CPU and memory specifications.

|vCPU|Memory|
|----|------|
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

Examples

For a Kubernetes-native container, you can directly configure the resource `request` of the `container`.

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

## Specify the pod specifications for an elastic container instance

When you specify CPU and memory specifications for an elastic container instance, the instance attempts to use multiple Elastic Compute Service \(ECS\) instance types to match the specifications. This allows you to improve the scalability and success rate of creation than with only one ECS instance type. This provides the following benefits:

-   You do not need to limit resources for containers in your elastic container instance. When you specify container resources for an elastic container instance, you do not need to configure the resource `request` or `limit`. This allows the containers in the elastic container instance to share the requested resources to the maximum extent.
-   In genomics computing and Istio scenarios, the service framework automatically adds sidecar containers to pods. You can seamlessly integrate the elastic container instance with the service framework by explicitly specifying the instance specifications.

Examples

You can set annotations: k8s.aliyun.com/eci-use-specs in the pod configuration to specify multiple ECS instance types. Separate multiple ECS instance types with commas \(,\).

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

