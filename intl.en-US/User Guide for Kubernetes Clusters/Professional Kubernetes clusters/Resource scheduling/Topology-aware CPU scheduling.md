---
keyword: [resource scheduling, topology-aware CPU scheduling, kube-scheduler, CPU manager]
---

# Topology-aware CPU scheduling

Container Service for Kubernetes \(ACK\) provides the topology-aware CPU scheduling feature based on the new Kubernetes scheduling framework. This feature can improve the performance of CPU-sensitive workloads. This topic describes how to enable topology-aware CPU scheduling.

-   A professional managed Kubernetes cluster is created. For more information, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md).

    **Note:** Topology-aware CPU scheduling is available for only professional managed Kubernetes clusters. To enable topology-aware CPU scheduling for dedicated Kubernetes clusters, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to add your account to the whitelist.

-   Before you enable topology-aware CPU scheduling, you must deploy resource-controller. For more information, see [Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Upgrade cluster/Manage system components.md).
-   The following table describes the system component versions that are required for topology-aware CPU scheduling.

    |Component|Required version|
    |---------|----------------|
    |Kubernetes|V1.16 and later|
    |Helm|V3.0 and later|
    |Docker|19.03.5|
    |Operating system|CentOS 7.6, CentOS 7.7, Ubuntu 16.04, Ubuntu 18.04, and Alibaba Cloud Linux 2.|


Multiple pods may run on a node in a Kubernetes cluster and some pods may belong to CPU-intensive workloads. In this case, pods compete for CPU resources. When this situation becomes intensive, the CPU cores that are allocated to each pod may be frequently changed. The situation intensifies when the Non-Uniform Memory Access \(NUMA\) nodes are used. These changes degrade the performance of the workloads. The Kubernetes CPU manager provides a CPU scheduling solution to fix this issue within a node. However, the Kubernetes CPU manager cannot find an optimal solution for allocating CPU cores within a cluster. The Kubernetes CPU manager works only on guaranteed pods and does not apply to all types of pods. In a guaranteed pod, each container is configured with requests and limits on CPU resources. In addition, the requests and limits are set to the same value.

Topology-aware CPU scheduling applies to the following scenarios:

-   The workload is compute-intensive.
-   The application is CPU-sensitive.
-   The workload is deployed on multi-core nodes, such as Elastic Compute Service \(ECS\) Bare Metal instances.

## Enable topology-aware CPU scheduling

Before you enable topology-aware CPU scheduling, you must set the annotations and containers parameters when you configure pods. Set the parameters in the following ways:

-   annotation: Set cpuset-scheduler to true to enable topology-aware CPU scheduling.
-   containers: Set resources.limit.cpu to an integer.

The following code block shows how to enable topology-aware CPU scheduling for pods:

```
apiVersion: v1
kind: Pod
metadata:
  name: cal-pi
  annotations: 
    cpuset-scheduler: 'true' #Add this annotation to enable topology-aware CPU scheduling.
spec:
  restartPolicy: Never
  containers:
  - image: registry.cn-zhangjiakou.aliyuncs.com/xianlu/java-pi
    name: cal-pi
    resources:
      requests:
        cpu: 4
      limits:
        cpu: 4  #Specify the value of resources.limit.cpu.
    env:
    - name: limit
      value: "20000"
    - name: threadNum
      value: "3000"
```

The following code block shows how to enable topology-aware CPU scheduling for Deployments:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: go-demo
spec:
  replicas: 4
  selector:
    matchLabels:
      app: go-demo
  template:
    metadata:
      annotations:
        cpuset-scheduler: "true" #Add this annotation to enable topology-aware CPU scheduling.
      labels:
        app: go-demo
    spec:
      containers:
      - name: go-demo
        image: registry.cn-hangzhou.aliyuncs.com/haoshuwei24/go-demo:1k
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
        resources:
          requests:
            cpu: 1
          limits: 
            cpu: 4  #Specify the value of resources.limit.cpu.
```

