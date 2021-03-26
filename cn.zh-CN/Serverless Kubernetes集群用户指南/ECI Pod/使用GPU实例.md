---
keyword: [GPU实例, ECI GPU实例, ECI Pod]
---

# 使用GPU实例

GPU实例内置了对应的Docker镜像，因此使用ECI GPU实例时无需安装Tensorflow、CUDA Toolkit等软件。本文介绍如何使用ECI GPU实例。

当前ECI GPU支持的驱动版本是NVIDIA 410.79，支持的CUDA Toolkit版本是10.0。

ECI支持通过指定ECS GPU规格来创建ECI GPU实例。支持的ECS GPU规格，如下所示：

-   GPU计算型实例规格族gn6v（NVIDIA V100\)，例如：ecs.gn6v-c8g1.2xlarge。
-   GPU计算型实例规格族gn6i（NVIDIA T4\)，例如：ecs.gn6i-c4g1.xlarge。
-   GPU计算型实例规格族gn5（NVIDIA P100\)，例如：ecs.gn5-c4g1.xlarge。
-   GPU计算型实例规格族gn5i（NVIDIA P4\)，例如：ecs.gn5i-c2g1.large。

完整的ECS GPU规格定义，请参见[实例规格族](/cn.zh-CN/实例/实例规格族.md)。

## 使用方法

在Pod定义中增加`annotations: k8s.aliyun.com/eci-use-specs`。

-   在Pod的`metadata`中添加指定规格的`annotations`。
-   在Container的`resources`中声明GPU资源。

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

