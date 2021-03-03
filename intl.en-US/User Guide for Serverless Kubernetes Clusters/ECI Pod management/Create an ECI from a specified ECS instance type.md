---
keyword: [specify ECS instance types, create an ECI]
---

# Create an ECI from a specified ECS instance type

In some scenarios, custom needs are required, such as GPU capabilities, enhanced network capabilities, high clock speed, and local storage. You can create an Elastic Container Instance \(ECI\) from a specified Elastic Compute Service \(ECS\) instance type. This topic describes how to create an ECI from a specified ECS instance type.

## Descriptions of ECS instance types

For detailed information about ECI-specific ECS instance types, see the descriptions of ECS instance types. The unit price of an ECI is the same as that of the corresponding ECS instance. Both are billed on a per-second basis. For more information, see [ECS price calculator](https://www.alibabacloud.com/pricing-calculator#/add/980738/vm_intl/vm_intl).

You can check the ECS instance types supported in each region and zone in [Overview of ECS instance types available to purchase in each region and zone](https://ecs-buy.aliyun.com/instanceTypes#/instanceTypeByRegion). The following ECS instance families are supported:

-   g6 and g5, general purpose instance families \(the CPU-to-memory ratio is 1:4\). sn2ne, general purpose instance family \(the CPU-to-memory ratio is 1:4\) with enhanced network performance
-   c6 and c5, compute optimized instance families \(the CPU-to-memory ratio is 1:2\). sn1ne: compute optimized instance family \(the CPU-to-memory ratio is 1:2\) with enhanced network performance
-   r6 and r5, memory optimized instance families \(the CPU-to-memory ratio is 1:8\). se1ne, memory optimized instance family \(the CPU-to-memory ratio is 1:8\) with enhanced network performance
-   ic5, compute intensive instance family \(the CPU-to-memory ratio is 1:1\)
-   hfc6 and hfc5, compute optimized instance families with high clock speed \(the CPU-to-memory ratio is 1:2\)
-   hfg6 and hfg5, general purpose instance families with high clock speed \(the CPU-to-memory ratio is 1:4\)
-   gn6i, gn6v, gn5i, and gn5, compute optimized instance families with GPU capabilities \(local storage is not supported\)
-   t5 and t6, burstable instance families

## Examples

You can set annotations: k8s.aliyun.com/eci-use-specs in the definition of a pod to specify multiple ECS instance types. Separate ECS instance types with commas \(,\).

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
      annotations:
        k8s.aliyun.com/eci-use-specs : "ecs.c5.large"  #Replace the value with the ECS instance types as needed.
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```

