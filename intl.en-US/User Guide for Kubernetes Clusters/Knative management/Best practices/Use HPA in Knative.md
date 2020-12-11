# Use HPA in Knative

You can use Horizontal Pod Autoscaler \(HPA\) in Knative to automatically scale the number of resources. You can set the threshold of the CPU metric for a Knative service. This ensures that resources are automatically scaled to handle heavy workloads. This topic describes how to use HPA in Knative.

[Deploy Knative](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Deploy Knative.md)

## Procedure

1.  Create the ksvc-hpa.yaml file.

    Configure an HPA scaling policy for a Knative service. The following code block is an example.

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

    -   `autoscaling.knative.dev/class: "hpa.autoscaling.knative.dev"` specifies the HPA plug-in.
    -   `autoscaling.knative.dev/metric` specifies the CPU metric.
    -   `autoscaling.knative.dev/target` specifies the threshold of the CPU metric.
    -   `autoscaling.knative.dev/minScale: "1"` specifies the minimum number of instances in the scaling policy.
    -   `autoscaling.knative.dev/maxScale: "10"` specifies the maximum number of instances in the scaling policy.
2.  Apply the scaling policy.

    ```
    kubectl apply -f ksvc-hpa.yaml
    ```


## Result

After HPA is enabled for the Knative service, the number of instances is automatically scaled, as shown in the following figure.

![Instance scaling](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0476867061/p178540.png)

