# Reserved instances

The reserved instances feature is an exclusive feature of serverless Kubernetes \(ASK\) Knative. If you want to reduce costs by using Knative and avoid cold starts at the same time, we recommend that you use reserved instances.

## Benefits of reserved instances

By default, the Knative community version scales the number of instances to zero during off-peak hours. This reduces the costs of running resident instances. The costs are reduced by scaling the number of instances to zero. However, a cold start with a latency occurs the next time you start an application. To create a new instance, the system must first allocate infrastructure resources by using the Kubernetes scheduler. The system must also pull the application image and start the application. The image size of an application and the time required to start an application vary based on developers and services. Common platforms have no control over the image size and startup time.

If you want to reduce costs by using Knative and avoid cold starts at the same time, we recommend that you use reserved instances. Unlike the Knative community version, ASK Knative does not scale the number of instances to zero during off-peak hours. Instead, ASK Knative reserves one instance. The type of the reserved instance can be different from the default instance type. For example, you can select an instance type that costs less than the default instance type. This helps you balance the costs and efficiency.

![Background information](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2348654061/p169856.png)

## How reserved instances work

ASK Knative uses reserved instances to replace compute optimized instances during off-peak hours. When ASK Knative receives a request, it uses the reserved instances to process the request and starts to create compute optimized instances. After the compute optimized instances are created, all new requests are forwarded to these instances. Reserved instances are released after they process all requests that are sent to them. This seamless switchover mechanism reduces the costs of resident instances and the latency caused by cold starts.

## Reserved instance types

Alibaba Cloud Elastic Container Instance \(ECI\) provides burstable instances. Burstable instances use CPU credits to ensure computing performance. The instances apply to scenarios where the CPU usage is low but bursts on occasion. A burstable instance can accumulate CPU credits. If a burstable instance fails to handle a heavy workload, it consumes CPU credits to improve computing performance. This mechanism does not affect the environment or applications running on the instance. Compared with other types of instances, a burstable instance provides a more flexible and cost-effective way to use CPU resources. The CPU credit mechanism allows you to minimize the consumption of resources during off-peak hours and scale out resources during peak hours. This reduces the cost of resources.

The cost-effectiveness and CPU credit features make burstable instances appropriate reserved instances. The default types of reserved instances in ASK Knative include ecs.t6-c1m1.large, ecs.t5-lc1m2.small, ecs.s6-c1m2.small, ecs.t6-c1m2.large, and ecs.n1.small. They are the lowest-priced instance types among all instance types that have at least one CPU core and 2 GB of memory. For more information about the ECI instance types and prices, see [ECI pricing](https://www.aliyun.com/price/product#/ecs/detail).

**Note:** You can configure up to five instance types for an ECI.

You can manually configure the types of reserved instances by using the `knative.aliyun.com/reserve-instance-eci-use-specs` annotation. You can configure instance types in the following two ways:

-   Specify the types of ECIs. In the following example, ecs.t6-c1m1.large and ecs.t5-lc1m2.small are specified.

    ```
    apiVersion: serving.knative.dev/v1
    kind: Service
    metadata:
      name: hello-spec-1
    spec:
      template:
        metadata:
          annotations:
            knative.aliyun.com/reserve-instance-eci-use-specs: "ecs.t6-c1m1.large,ecs.t5-lc1m2.small"
        spec:
          containers:
            - image: registry.cn-hangzhou.aliyuncs.com/knative-sample/helloworld-go:160e4dc8
    ```

-   Specify the minimum CPU cores and memory. ASK Knative automatically searches for instance types that meet the requirements and uses the five lowest-priced instance types to create instances. In the following example, `1-2Gi` indicates 1 CPU core and 2 GB of memory.

    ```
    apiVersion: serving.knative.dev/v1
    kind: Service
    metadata:
      name: hello-spec-2
    spec:
      template:
        metadata:
          annotations:
            knative.aliyun.com/reserve-instance-eci-use-specs: "1-2Gi"
        spec:
          containers:
            - image: registry.cn-hangzhou.aliyuncs.com/knative-sample/helloworld-go:160e4dc8
    ```


Knative creates ECI instances based on a list of ECI instance types. The instance types are listed in sequential order. If an instance type is unavailable, the next instance type in the list is used.

