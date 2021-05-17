---
keyword: [use HPA, Knative]
---

# Use HPA in Knative

You can use Horizontal Pod Autoscaler \(HPA\) in Knative to automatically scale pods. You can set the threshold of the CPU metric for a Knative Service. This ensures that pods are automatically scaled to match the fluctuations of user traffic. This topic describes how to use HPA in Knative.

[Deploy Knative in an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Knative/Component management/Deploy Knative in an ASK cluster.md)

## Procedure

1.  Create the ksvc-hpa.yaml file.

    Configure an HPA scaling policy for a Knative Service. The following code block is an example:

    ```
    apiVersion: serving.knative.dev/v1
    kind: Service
    metadata:
      name: helloworld-go-hpa
    spec:
      template:
        metadata:
          labels:
            app: helloworld-go-hpa
          annotations:
            autoscaling.knative.dev/class: "hpa.autoscaling.knative.dev"
            autoscaling.knative.dev/metric: "cpu"
            autoscaling.knative.dev/target: "75"
            autoscaling.knative.dev/minScale: "1"
            autoscaling.knative.dev/maxScale: "10"
        spec:
          containers:
            - image: registry.cn-hangzhou.aliyuncs.com/knative-samples/helloworld-go:160e4dc8
              resources:
                requests:
                  cpu: '200m'              
    ```

    -   The `autoscaling.knative.dev/class: "hpa.autoscaling.knative.dev"` annotation specifies the HPA component for the Knative Service.
    -   The `autoscaling.knative.dev/metric` annotation specifies the CPU metric.
    -   The `autoscaling.knative.dev/target` annotation specifies the threshold of the CPU metric.
    -   The `autoscaling.knative.dev/minScale: "1"` annotation specifies the minimum number of pods that must be guaranteed.
    -   The `autoscaling.knative.dev/maxScale: "10"` annotation specifies the maximum number of pods that are allowed.
2.  Apply the HPA scaling policy.

    ```
    kubectl apply -f ksvc-hpa.yaml
    ```


## Result

Check the changes in the number of pods after HPA is enabled for the Knative Service. The following trend chart shows an example.

![Instance scaling](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0476867061/p178540.png)

