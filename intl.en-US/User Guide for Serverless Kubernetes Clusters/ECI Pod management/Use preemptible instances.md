---
keyword: [preemptible instances, ECI pod]
---

# Use preemptible instances

Preemptible instances are cost-effective. You can bid for idle resources of Alibaba Cloud, obtain the resources, and then run containers until the container resources are reclaimed due to higher bids from other customers. This reduces the costs of Elastic Container Instances \(ECIs\) in some scenarios. This topic describes how to use preemptible instances.

Preemptible instances are billed at a lower price compared with pay-as-you-go instances. The actual price of a preemptible instance changes based on the supply and demand. A preemptible instance is billed based on the actual period of usage. For more information, see [Overview](/intl.en-US/Instance/Instance purchasing options/Preemptible instances/Overview.md).

A preemptible instance has a guaranteed period of one hour after it is created. During this period, the instance is not released even if your bid is lower than the current market price. This ensures that the instance can run your services without interruptions. After the guaranteed period ends, the system checks the market price and the stock of the instance type every five minutes. If the market price is higher than your bid or if the stock of the instance type is insufficient, your preemptible instance is released.

-   After your preemptible instance keeps running for one hour, it may be reclaimed by the system due to price fluctuations or insufficient resources.
-   The system will notify you three minutes before a preemptible instance is released.
-   Released preemptible instances are reclaimed. The instance information is retained. After a preemptible instance is reclaimed, the billing stops and its state changes to Expired.

When you use a preemptible instance, we recommend that you follow these suggestions:

-   When you set your bid, consider market price fluctuations. Only if your bid is higher than bids from other customers, the request is accepted and processed, and the created instance will not be released due to price fluctuations. In addition, you must submit the price based on the expected requirements of your workloads.
-   We recommend that you store important data in a storage medium that is not affected by the release of preemptible instances. For example, you can use a cloud disk that will not be released along with the preemptible instance, or choose external storage such as Network-attached storage \(NAS\).

You can create a preemptible instance in one of the following ways:

-   Specify the type of Elastic Compute Service \(ECS\) instance.

    The preemptible instance is billed based on the market price and discount for a pay-as-you-go ECS instance of the specified instance type at the time of purchase.

-   Specify CPU and memory specifications.

    ECI automatically matches ECS instance types that meet the price requirement. The market price of the selected instance type is adopted as the base market price. Discounts are applied based on the base market price instead of the pay-as-you-go price of CPU and memory resources of the corresponding ECI. Specifying CPU and memory specifications is equivalent to specifying the ECS instance type.


## Scenarios

Preemptible instances are ideal for stateless applications, such as scalable web services, image rendering, big data analytics, and large-scale parallel computing. Preemptible instances can be applied to applications that require a high level of distribution, scalability, and fault tolerance capabilities. Preemptible instances help reduce costs and increase the throughput of these applications.

You can use preemptible instances in the following scenarios:

-   Real-time analytics
-   Big data processing
-   Scalable websites
-   Image and media encoding
-   Scientific computing
-   Geospatial surveys
-   Web crawlers
-   Tests

## Procedure

You can set annotations: k8s.aliyun.com/eci-spot-strategy and annotations: k8s.aliyun.com/eci-spot-price-limit in a pod.

-   SpotAsPriceGo: automatically submits bids based on the up-to-date market price.
-   SpotWithPriceLimit: specifies the highest bid for the preemptible instance.

Details:

-   Set `k8s.aliyun.com/eci-spot-strategy` to `"SpotAsPriceGo"`.
-   `k8s.aliyun.com/eci-spot-price-limit` takes effect only when `k8s.aliyun.com/eci-spot-strategy` is set to `"SpotWithPriceLimit"`.

    Set the highest bid within an hour for the preemptible instance. The price can be accurate to three decimal places.

    -   `k8s.aliyun.com/eci-spot-strategy: "SpotAsPriceGo"`
    -   `k8s.aliyun.com/eci-spot-price-limit: "0.250"`

Deployment example

-   SpotAsPriceGo strategy

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
            k8s.aliyun.com/eci-use-specs: "2-4Gi"  #Specify an ECS instance type or CPU and memory specifications based on requirements.
            k8s.aliyun.com/eci-spot-strategy: "SpotAsPriceGo"  #A value of SpotAsPriceGo specifies a preemptible instance that is priced at the market price at the time of purchase.  
        spec:
          containers:
          - name: nginx
            image: nginx:1.7.9
            ports:
            - containerPort: 80
    ```

-   SpotWithPriceLimit strategy

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
            k8s.aliyun.com/eci-use-specs: "ecs.c5.large"  #Specify an ECS instance type or CPU and memory specifications as needed.
            k8s.aliyun.com/eci-spot-strategy: "SpotWithPriceLimit"  #A value of SpotWithPriceLimit specifies a preemptible instance with the highest hourly price.
            k8s.aliyun.com/eci-spot-price-limit: "0.250" #The highest hourly price.
        spec:
          containers:
          - name: nginx
            image: nginx:1.7.9
            ports:
            - containerPort: 80
    ```


