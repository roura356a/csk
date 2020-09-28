# 在Knative上实现只扩容不缩容

在应用无法平滑下线或者应用缩容造成服务抖动等场景下，需要使用只扩容不缩容的功能。本文介绍如何在在Knative上实现只扩容不缩容。

[部署Knative](/cn.zh-CN/Serverless Kubernetes集群用户指南/Knative管理/部署Knative.md)

在某些场景下，例如应用无法平滑下线或者应用缩容造成服务抖动，需要使用弹性功能。ASK提供只扩容不缩容的功能满足需求。同时在业务低谷的时候需要进行缩容时，您也只需调整HPA的最小值参数。

## 操作步骤

通过Knative Service开启只扩容不缩容策略。

```
apiVersion: serving.knative.dev/v1
kind: Service
metadata:
  name: helloworld-go-spa
  annotations:
    alicloud.autoscaling.service.knative.dev/canscaledown: "false"
    networking.knative.dev/ingress.class: "hyper.ingress.networking.knative.dev"
spec:
  template:
    metadata:
      labels:
        app: helloworld-go-spa
      annotations:
        autoscaling.knative.dev/class: "spa.autoscaling.knative.dev"
        alicloud.service.knative.dev/no-queueproxy: "true"
        alicloud.autoscaling.knative.dev/metric-target: "cpu:70"
        autoscaling.knative.dev/maxScale: "10"
        autoscaling.knative.dev/minScale: "1"
    spec:
      containers:
        - image: registry.cn-hangzhou.aliyuncs.com/knative-samples/helloworld-go:160e4dc8
          resources:
            requests:
              cpu: '200m'              
```

-   通过`alicloud.autoscaling.service.knative.dev/canscaledown:false`设置只扩容不缩容。
-   通过`autoscaling.knative.dev/class: "spa.autoscaling.knative.dev"`指定只扩容不缩容弹性插件。
-   多指标支持。社区Knative仅支持同时设置一个指标，通过`alicloud.autoscaling.knative.dev/metric-target`可以设置多个HPA弹性指标。例如同时支持CPU和内存指标，则设置为`cpu:50,memory:50`。
-   通过`autoscaling.knative.dev/minScale: "1"`设置弹性策略最小值。
-   通过`autoscaling.knative.dev/maxScale: "10"`设置弹性策略最大值。

