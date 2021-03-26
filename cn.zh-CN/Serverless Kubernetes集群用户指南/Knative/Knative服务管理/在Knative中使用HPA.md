---
keyword: [使用HPA, Knative]
---

# 在Knative中使用HPA

Knative支持HPA的弹性能力。您可以在Knative Service中设置CPU指标阈值，满足在突发高负载的场景下，自动扩缩容资源的诉求。本文介绍如何在Knative中使用HPA。

[开启Knative](/cn.zh-CN/Serverless Kubernetes集群用户指南/Knative/Knative组件管理/开启Knative.md)

## 操作步骤

1.  创建ksvc-hpa.yaml。

    在Knative Service指定使用HPA弹性策略，以下为配置示例。

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

    -   通过`autoscaling.knative.dev/class: "hpa.autoscaling.knative.dev"`指定HPA弹性插件。
    -   通过`autoscaling.knative.dev/metric`设置HPA CPU指标。
    -   通过`autoscaling.knative.dev/target`设置HPA CPU指标的阈值。
    -   通过`autoscaling.knative.dev/minScale: "1"`设置弹性策略实例数的最小值。
    -   通过`autoscaling.knative.dev/maxScale: "10"`设置弹性策略实例数的最大值。
2.  执行HPA弹性策略。

    ```
    kubectl apply -f ksvc-hpa.yaml
    ```


## 执行结果

Knative Service开启HPA弹性后，实例变化趋势如下图所示。

![实例变化](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2016034061/p178540.png)

