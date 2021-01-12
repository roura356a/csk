---
keyword: [GPU-accelerated ECI, GPU-accelerated ECI, ECI Pod]
---

# Use GPU-accelerated ECIs

GPU-accelerated elastic container instances \(ECIs\) are pre-installed with Docker images. Therefore, when you use GPU-accelerated ECIs, you do not need to install software such as TensorFlow and CUDA Toolkit. This topic describes how to use a GPU-accelerated ECI.

The GPU driver version supported by GPU-accelerated ECIs is NVIDIA 410.79. The CUDA Toolkit version supported by GPU-accelerated ECIs is V10.0.

You can specify GPU-accelerated Eastic Compute Service \(ECS\) instance types to create GPU-accelerated ECIs. Supported GPU-accelerated ECS instance types include:

-   gn6v, GPU \(NVIDIA V100\)-accelerated and compute optimized instance family, for example, ecs.gn6v-c8g1.2xlarge.
-   gn6i, GPU \(NVIDIA T4\)-accelerated and compute optimized instance family, for example, ecs.gn6i-c4g1.xlarge.
-   gn5, GPU \(NVIDIA P100\)-accelerated and compute optimized instance family, for example, ecs.gn5-c4g1.xlarge.
-   gn5i, GPU \(NVIDIA P4\)-accelerated and compute optimized instance family, for example, ecs.gn5i-c2g1.large.

For more information about GPU-accelerated ECS instance types, see [Instance families](/intl.en-US/Instance/Instance families.md).

## Procedure

Add `annotations: k8s.aliyun.com/eci-use-specs` to the pod configuration.

-   Add annotations of the specified instance type to the metadata field of the pod configuration.
-   Specify GPU resources in the resources field of the container configuration.

```
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: nginx-gpu-demo
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
        k8s.aliyun.com/eci-use-specs: ecs.gn5i-c4g1.xlarge
    spec:
      containers:
      - name: nginx
        image: registry-vpc.cn-beijing.aliyuncs.com/eci_open/nginx:1.15.10
        resources:
            limits:
              nvidia.com/gpu: '1'
        ports:
        - containerPort: 80
```

