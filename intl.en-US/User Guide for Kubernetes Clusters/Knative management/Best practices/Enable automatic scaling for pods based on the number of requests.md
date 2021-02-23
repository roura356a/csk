# Enable automatic scaling for pods based on the number of requests

Knative Pod Autoscaler \(KPA\) is an out-of-the-box feature that can scale the pods for your applications based on the number of requests. This topic describes how to implement auto scaling based on the number of requests.

Knative Serving adds the Queue-Proxy container to each pod. The container reports concurrency-related metrics of the application container to KPA. After KPA receives the metrics, it implements auto scaling by adding pods to or removing pods from the deployment based on the number of concurrent requests and related algorithms.

![Scaling](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1805715061/p172886.png)

## Algorithms

KPA implements auto scaling based on the average number of concurrent requests per pod, which is specified by the concurrency target. The default value of the concurrency target is 100. The number of pods is determined based on the following formula: Number of pods = Total number of concurrent requests on the application/Concurrency target per pod. For example, if you set the concurrency target per pod to 10 and load 50 concurrent requests, KPA creates five pods.

KPA implements auto scaling in two modes: stable and panic.

-   Stable

    In stable mode, KPA adjusts the number of pods for an application. This ensures that the average number of concurrent requests per pod matches the concurrency target. The concurrency target indicates the average number of requests per pod within a 60-second window.

-   Panic

    To enable KPA to calculate the average number of concurrent requests per pod over a 60-second window, the number of concurrent requests must remain at a specified level for 60 seconds. KPA also calculates the number of concurrent requests over a six-second time window. KPA enters the panic mode if the total number of concurrent requests on the application is twice as many as the requests that can be handled by the current pods. In panic mode, KPA scales pods in a shorter time period than in stable mode. After the application remains in panic mode for 60 seconds, KPA returns to the stable mode.

    ```
                                                           |
                                      Panic Target--->  +--| 20
                                                        |  |
                                                        | <------Panic Window
                                                        |  |
           Stable Target--->  +-------------------------|--| 10   CONCURRENCY
                              |                         |  |
                              |                      <-----------Stable Window
                              |                         |  |
    --------------------------+-------------------------+--+ 0
    120                       60                           0
                         TIME
    ```


## KPA configurations

-   When you configure KPA, you must configure the config-autoscaler ConfigMap. By default, the config-autoscaler parameters are configured. The following list describes the key parameters:

    Run the following command to query config-autoscaler:

    ```
    kubectl -n knative-serving get cm config-autoscaler
    ```

    Expected output:

    ```
    apiVersion: v1
    kind: ConfigMap
    metadata:
     name: config-autoscaler
     namespace: knative-serving
    data:
     container-concurrency-target-default: "100"
     container-concurrency-target-percentage: "0.7"
     enable-scale-to-zero: "true"
     max-scale-up-rate: "1000"
     max-scale-down-rate: "2"
     panic-window-percentage: "10"
     panic-threshold-percentage: "200"
     scale-to-zero-grace-period: "30s"
     scale-to-zero-Pod-retention-period: "0s"
     stable-window: "60s"
     target-burst-capacity: "200"
     requests-per-second-target-default: "200"
    ```

    -   Configure parameters related to the scale-to-zero feature.
        -   scale-to-zero-grace-period: The time period for which the last pod remains active after KPA decides to scale pods to zero. The minimum value is 30 seconds.

            ```
            scale-to-zero-grace-period: 30s
            ```

        -   stable-window: In stable mode, KPA decides whether to scale pods based on the average number of concurrent requests per pod over the stable window.

            ```
            stable-window: 60s
            ```

            You can also specify the stable window by using the following per-revision annotation key:

            ```
            autoscaling.knative.dev/window: 60s
            ```

        -   enable-scale-to-zero: Set the `enable-scale-to-zero` parameter to `true`.

            ```
            enable-scale-to-zero: "true"
            ```

    -   Configure the concurrency targets for KPA.
        -   target

            The target parameter is a soft limit that defines the number of concurrent requests on each pod in a specified time period. We recommend that you use this parameter to limit the concurrency. The default value of the target parameter is 100 in the ConfigMap.

            ```
            `container-concurrency-target-default: 100`
            ```

            You can modify this value by using the `autoscaling.knative.dev/target` per-revision annotation key.

            ```
            autoscaling.knative.dev/target: 50
            ```

        -   containerConcurrency

            containerConcurrency is a hard limit that defines the enforced upper bound of the number of concurrent requests allowed on each pod in a specified time period. You can configure this parameter in the template section of the revision configuration file.

            **Note:**

            To use containerConcurrency, make sure that the following conditions are met:

            -   Use containerConcurrency only if you want to limit the number of concurrent requests on the application in a specified time period.
            -   Use containerConcurrency only if the application requires enforced concurrency limits.
            ```
            containerConcurrency: 0 | 1 | 2-N
            ```

            -   1: Only one request is handled at a time by each pod created based the current revision.
            -   2-N: Two or more concurrent requests are allowed to flow to each pod at a time.
            -   0: The number of concurrent requests on each pod is not limited. The number depends on the system.
        -   container-concurrency-target-percentage

            This parameter is also called a target utilization value or a concurrency factor. It is used in the calculation of the concurrency target in scaling. For example, assume that the target or containerConcurrency parameter is set to 100 and the target utilization value is set to 70%. KPA adds pods to the application when the average number of concurrent requests per pod reaches 70.

            The following formula applies: Actual concurrency that triggers auto scaling = target or containerConcurrency × container-concurrency-target-percentage.

-   Set the scale bounds. You can configure the minScale and maxScale parameters to configure the scale bounds of pods that an application can use. You can use these two parameters to reduce cold starts and computing costs.

**Note:**

-   If you do not configure the minScale annotation, all pods are removed if no traffic arrives. If you set the `enable-scale-to-zero` parameter of config-autoscaler to `false`, KPA stops scaling in pods when only one pod is used.
-   If you do not configure the maxScale annotation, the number of pods that can be created for the application is unlimited.
    You can configure the minScale and maxScale parameters in the template section of the revision configuration file.

    ```
    spec:
      template:
        metadata:
          autoscaling.knative.dev/minScale: "2"
          autoscaling.knative.dev/maxScale: "10"
    ```


## Scenario 1: Implement auto scaling based on concurrency targets

You can use KPA to implement auto scaling based on the specified average number of concurrent requests per pod.

1.  [Create a Knative Service](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative services/Create a Knative Service.md).

2.  Create the autoscale-go.yaml file.

    Set the maximum number of concurrent requests per pod to 10.

    ```
    apiVersion: serving.knative.dev/v1alpha1
    kind: Service
    metadata:
      name: autoscale-go
      namespace: default
    spec:
      template:
        metadata:
          labels:
            app: autoscale-go
          annotations:
            autoscaling.knative.dev/target: "10"
        spec:
          containers:
            - image: registry.cn-hangzhou.aliyuncs.com/knative-sample/autoscale-go:0.1
    ```

3.  Run the following command to query the Kourier access gateway:

    ```
    kubectl -n knative-serving get svc kourier --output jsonpath="{.status.loadBalancer.ingress[*]['ip']}"
    ```

4.  Use hey to maintain 50 concurrent requests on the application for 30 seconds.

    **Note:** hey is a load generator. For more information, see [hey](https://github.com/rakyll/hey).

    ```
    hey -z 30s -c 50   -host "autoscale-go.default.example.com"   "http://121.199.194.150?sleep=100&prime=10000&bloat=5"
    ```

    Expected output:

    ![hey](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1805715061/p181965.gif)

    Five pods are added.


## Scenario 2: Implement auto scaling based on scale bounds

Scale bounds control the minimum and maximum number of pods that an application can use. You can implement auto scaling based on scale bounds.

1.  [Create a Knative Service](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative services/Create a Knative Service.md).

2.  Create the autoscale-go.yaml file.

    Set the concurrency target to 10, minScale to 1, and maxScale to 3.

    ```
    apiVersion: serving.knative.dev/v1alpha1
    kind: Service
    metadata:
      name: autoscale-go
      namespace: default
    spec:
      template:
        metadata:
          labels:
            app: autoscale-go
          annotations:
            autoscaling.knative.dev/target: "10"
            autoscaling.knative.dev/minScale: "1"
            autoscaling.knative.dev/maxScale: "3"   
        spec:
          containers:
            - image: registry.cn-hangzhou.aliyuncs.com/knative-sample/autoscale-go:0.1
    ```

3.  Use hey to maintain 50 concurrent requests on the application for 30 seconds.

    **Note:** hey is a load generator. For more information, see [hey](https://github.com/rakyll/hey).

    ```
    hey -z 30s -c 50   -host "autoscale-go.default.example.com"   "http://121.199.194.150?sleep=100&prime=10000&bloat=5"
    ```

    Expected output:

    ![maintain](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2805715061/p181966.gif)

    A maximum of three pods are added. One pod is reserved when no traffic flows to the application.


