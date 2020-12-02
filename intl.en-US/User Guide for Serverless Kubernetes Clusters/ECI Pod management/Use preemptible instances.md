---
keyword: [Preemptible instance, ECI Pod]
---

# Use preemptible instances

Preemptible instances are cost-effective. You can bid for the idle resources of Alibaba Cloud, obtain the resources, and then run containers until the container resources are reclaimed due to higher bids from other customers. This reduces the costs of Elastic Container Instances \(ECIs\) in some scenarios. This topic describes how to use a preemptible instance.

Preemptible instances are billed at a lower price compared with pay-as-you-go instances. The actual price of a preemptible instance changes based on the supply and demand condition. A preemptible instance is billed based on the actual period of usage. For more information, see [Overview](/intl.en-US/Instance/Instance purchasing options/Preemptible instances/Overview.md).

A preemptible instance has a guaranteed period of one hour after it is created. During this period, the instance is not released even if your bid is lower than the current market price. This ensures that the instance can run your services without interruptions. After the guaranteed period ends, the system checks the market price and the stock of the instance type every five minutes. If the market price is higher than your bid, or if the stock of the instance type is insufficient, your preemptible instance is released.

-   After your preemptible instance keeps running for one hour, it will be reclaimed by the system due to price fluctuations or insufficient resources.
-   The system will notify you three minutes before a preemptible instance is released.
-   Released preemptible instances are reclaimed. The instance information is retained. After an preemptible instance is reclaimed, the billing stops and its status changes to Expired.

When you use a preemptible instance, we recommend that you follow these suggestions:

-   When you set your bid, consider market price fluctuations. Only if your bid is higher than bids from other customers, the request is accepted and processed, and the created instance will not be released due to price fluctuations. In addition, you must submit the price based on the expected requirements of your workloads.
-   We recommend that you store your data in a storage medium that is not affected by the release of preemptible instances. For example, you can use a cloud disk that will not be released along with the instance, or choose an external storage such as a Network Attached Storage \(NAS\).

You can create a preemptible instance by using one of the following ways:

-   Create a preemptible instance with specified Elastic Compute Service \(ECS\) instance types.

    The instance is billed based on the pay-as-you-go market price of the corresponding instance type and the up-to-date discounts.

-   Create a preemptible instance with specified CPU and memory resources

    ECI automatically matches ECS instance types that meet the price requirement. The market price of the selected instance type is adopted as the original market price. Discounts are applied based on the original market price. Discounts are not applied based on the pay-as-you-go price of CPU/memory resources of the corresponding ECI. This is the same as when you create a preemptible instance with specified ECS instance types.


## Scenarios

Preemptible instances are ideal for stateless applications, such as scalable web services, image rendering, big data analytics, and large-scale parallel computing. Preemptible instances can be applied to applications that require a high level of distribution, scalability, and fault tolerance capabilities to save the costs and increase the throughput of these applications.

You can use preemptible instances in the following scenarios:

-   Real-time analytics
-   Big data computing
-   Scalable websites
-   Image and media coding
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
-   Only when `k8s.aliyun.com/eci-spot-strategy` is set to `"SpotWithPriceLimit"`, `k8s.aliyun.com/eci-spot-price-limit` takes effect.

    Set the highest bid within an hour for the instance. The price can be accurate to three decimal places.

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
            k8s.aliyun.com/eci-use-specs: "2-4Gi"# Replace the ECS instance type with the acceptable one.
            k8s.aliyun.com/eci-spot-strategy: "SpotAsPriceGo"# Bid based on the market price.
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
            k8s.aliyun.com/eci-use-specs: "ecs.c5.large"# Replace the ECS instance type with the acceptable one.
            k8s.aliyun.com/eci-spot-strategy: "SpotWithPriceLimit"# Set the highest bid.
            k8s.aliyun.com/eci-spot-price-limit: "0.250"# Set the highest bid within an hour.
        spec:
          containers:
          - name: nginx
            image: nginx:1.7.9
            ports:
            - containerPort: 80
    ```


