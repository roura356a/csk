---
keyword: [Knative Pod Autoscaler, number of requests]
---

# Enable auto scaling to withstand traffic fluctuations

Knative Pod Autoscaler \(KPA\) is an out-of-the-box feature that can scale pods for your application to withstand traffic fluctuations. This topic describes how to enable auto scaling of pods based on the number of requests.

Knative Serving adds the Queue-Proxy container to each pod. The Queue-Proxy container sends concurrency metrics of the application containers to KPA. After KPA receives the metrics, KPA automatically adjusts the number of pods provisioned for a Deployment based on the number of concurrent requests and related algorithms.

![Scaling](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0032790261/p172886.png)

**Concurrency and QPS**

-   The number of concurrent requests refers to the number of requests received by a pod at the same time.
-   Queries per second \(QPS\) refers to the number of requests that can be handled by a pod per second. It also shows the maximum throughput of a pod.

The increase of concurrent requests does not necessarily increase the QPS. In scenarios where the access load is heavy, if the concurrency is increased, the QPS may be decreased. This is because the system is overloaded and the CPU and memory usage is high, which downgrades the performance and increases the response latency.

## Algorithms

KPA performs auto scaling based on the average number of concurrent requests per pod. This value is specified by the concurrency target. The default concurrency target is 100. The number of pods required to handle requests is determined based on the following formula: Number of pods = Total number of concurrent requests to the application/Concurrency target. For example, if you set the concurrency target to 10 and send 50 concurrent requests to an application, KPA creates five pods.

KPA provides two auto scaling modes: stable and panic.

-   Stable

    In stable mode, KPA adjusts the number of pods provisioned for a Deployment to match the specified concurrency target. The concurrency target indicates the average number of requests received by a pod within a stable window of 60 seconds.

-   Panic

    KPA calculates the average number of concurrent requests per pod within a stable window of 60 seconds. Therefore, the number of concurrent requests must remain at a specific level for 60 seconds. KPA also calculates the number of concurrent requests per pod within a panic window of 6 seconds. If the number of concurrent requests reaches twice the concurrency target, KAP switches to the panic mode. In panic mode, KPA scales pods within a shorter time window than in stable mode. After the burst of concurrent requests lasts for 60 seconds, KPA automatically switches back to the stable mode.

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

-   To configure KPA, you must configure config-autoscaler. By default, config-autoscaler is configured. The following content describes the key parameters.

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
        -   scale-to-zero-grace-period: The time period for which `inactive revison` keeps running before KPA scales the number of pods to zero. The minimum period is 30 seconds.

            ```
            scale-to-zero-grace-period: 30s
            ```

        -   stable-window: In stable mode, KPA scales pods based on the average number of concurrent requests per pod within the stable window.

            ```
            stable-window: 60s
            ```

            You can also specify the stable window by using an annotation in the Deployment revision.

            ```
            autoscaling.knative.dev/window: 60s
            ```

        -   enable-scale-to-zero: Set `enable-scale-to-zero` to `true`.

            ```
            enable-scale-to-zero: "true"
            ```

    -   Set a concurrency target for KPA.
        -   `target`

            The `target` parameter sets a soft limit on the number of concurrent requests handled by each pod within a specified time period. We recommend that you use this parameter to control the concurrency. By default, the concurrency target in the ConfigMap is set to 100.

            ```
            `container-concurrency-target-default: 100`
            ```

            You can change the value by using the `autoscaling.knative.dev/target` per-revision annotation.

            ```
            autoscaling.knative.dev/target: 50
            ```

        -   `containerConcurrency`

            The `containerConcurrency` parameter sets a hard limit on the number of concurrent requests handled by each pod within a specified time period. You can configure this parameter in the template section of the revision configuration file.

            **Note:**

            To use `containerConcurrency`, make sure that the following conditions are met:

            -   Use `containerConcurrency` only when you want to limit the number of concurrent requests handled by an application within a specified time period.
            -   Use `containerConcurrency` only when you want to forcibly control the concurrency of an application.
            ```
            containerConcurrency: 0 | 1 | 2-N
            ```

            -   1: Only one request is handled at a time by each pod created based the current revision.
            -   2-N: Two or more concurrent requests are handled by each pod at a time.
            -   0: The number of concurrent requests handled by each pod is not limited. The number depends on the system.
        -   container-concurrency-target-percentage

            This parameter is also known as a target utilization value or a concurrency factor. It is used in the calculation of the concurrency target for auto scaling. For example, the concurrency target or the `containerConcurrency` parameter is set to 100 and the target utilization value is set to 0.7. KPA adds pods to the application when the average number of concurrent requests per pod reaches 70 \(100 × 0.7\).

            The following formula applies: Concurrency value that triggers auto scaling = Concurrency target \(or the value of `containerConcurrency`\) × The value of container-concurrency-target-percentage.

-   Set the scale bounds. You can use the `minScale` and `maxScale` parameters to set the scale bounds of pods that are provisioned for an application. You can use these two parameters to reduce cold starts and computing costs.

**Note:**

-   If you do not set the `minScale` annotation, all pods are removed when no traffic arrives. If you set `enable-scale-to-zero` to `false` in the config-autoscaler ConfigMap, KPA scales the number of pods to one when no traffic arrives.
-   If you do not set the `maxScale` annotation, the number of pods that can be provisioned for the application is unlimited.
    You can configure the `minScale` and `maxScale` parameters in the template section of the revision configuration file.

    ```
    spec:
      template:
        metadata:
          autoscaling.knative.dev/minScale: "2"
          autoscaling.knative.dev/maxScale: "10"
    ```


## Scenario 1: Enable auto scaling by setting a concurrency target

This example shows how to enable KPA to perform auto scaling by setting a concurrency target.

1.  [Enable Knative](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Knative/Component management/Enable Knative.md).

2.  Create an autoscale-go.yaml file.

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

3.  Run the following command to query the Ingress gateway:

    ```
    kubectl -n knative-serving get svc
    ```

    Expected output:

    ```
    NAME              TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                      AGE
    ingress-gateway   LoadBalancer   172.19.1*.***   121.199.19*.***   80:32185/TCP,443:31137/TCP   69d
    ```

4.  Use the load testing tool hey to send 50 concurrent requests to the application within 30 seconds.

    **Note:** For more information about hey, see [hey](https://github.com/rakyll/hey).

    ```
    hey -z 30s -c 50   -host "autoscale-go.default.example.com"   "http://121.199.194.150?sleep=100&prime=10000&bloat=5"
    ```

    Expected output:

    ![hey](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7005915261/p172988.gif)

    The output indicates that five pods are added.


## Scenario 2: Enable auto scaling by setting scale bounds

Scale bounds control the minimum and maximum numbers of pods that can be provisioned for an application. This example shows how to enable auto scaling by setting scale bounds.

1.  [Enable Knative](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Knative/Component management/Enable Knative.md).

2.  Create an autoscale-go.yaml file.

    Set the concurrency target to 10, `minScale` to 1, and `maxScale` to 3.

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

3.  Use hey to send 50 concurrent requests to the application within 30 seconds.

    **Note:** For more information about hey, see [hey](https://github.com/rakyll/hey).

    ```
    hey -z 30s -c 50   -host "autoscale-go.default.example.com"   "http://121.199.194.150?sleep=100&prime=10000&bloat=5"
    ```

    Expected output:

    ![Auto scaling](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7005915261/p172995.gif)

    A maximum of three pods are added. One pod is reserved when no traffic flows to the application.


