# 在Knative上设置定时弹性

ASK支持定时弹性功能，可以通过定时弹性提前规划资源数量，满足周期性的设置弹性资源的诉求。除此之外，Knative定时弹性融合了HPA的弹性能力，对于突发高负载的场景下，依然能够保证弹性扩容足够的资源提供服务。本文介绍如何在Knative上设置定时弹性。

[开启Knative](/intl.zh-CN/Serverless Kubernetes集群用户指南/Knative管理/Knative组件管理/开启Knative.md)

## 操作步骤

1.  设置定时策略。

    通过ConfigMap配置定时弹性策略，每个Knative Service对应一个定时弹性策略，以下为配置示例。

    ```
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: cron-autoscaler
      namespace: default
    data:
      jobs:
       - name: "workday"
         schedule: "* * 1-5"
         timeseries:
         - timeSlice: 01:30:30
           replicas: 1
         - timeSlice: 05:30:30
           replicas: 2
         - timeSlice: 07:30:30
           replicas: 5
         - timeSlice: 10:24:30
           replicas: 8
         - timeSlice: 11:47:30
           replicas: 10
         - timeSlice: 13:17:30
           replicas: 6
         - timeSlice: 16:50:30
           replicas: 9
         - timeSlice: 20:17:30
           replicas: 5
         - timeSlice: 23:30:30
           replicas: 1
       - name: "holiday"
         schedule: "* * 0,6"
         timeseries:
         - timeSlice: 08:24:30
           replicas: 4
         - timeSlice: 11:47:30
           replicas: 3
         - timeSlice: 13:17:30
           replicas: 2
    ```

    -   schedule字段`* * 1-5`：第一个参数表示一个月份中的第几日，第二个参数表示月份，第三个表示一个星期中的第几天。1-5表示周一到周五，0,6表示周六、周日。
    -   timeseries字段：表示一天之内不同时间段的实例数。timeseries字段格式（24时制）：`小时:分钟:秒`。
    **说明：**

    -   如果更新该策略，引用该策略的所有Knative Service都会更新。
    -   如果删除该策略，现有引用该策略的Knative Service保持当前的状态，不会缩容到0。
2.  使用定时弹性策略。

    配置完弹性策略之后，您可以通过Knative Service指定使用该定时弹性策略。

    ```
    apiVersion: serving.knative.dev/v1
    kind: Service
    metadata:
      name: helloworld-go-ppa
      annotations:
        alicloud.autoscaling.service.knative.dev/timeseries: "cron-autoscaler"
    spec:
      template:
        metadata:
          labels:
            app: helloworld-go-ppa
          annotations:
            autoscaling.knative.dev/class: "ppa.autoscaling.knative.dev"
            alicloud.autoscaling.knative.dev/metric-target: "cpu:50"
        spec:
          containers:
            - image: registry.cn-hangzhou.aliyuncs.com/knative-samples/helloworld-go:160e4dc8
              resources:
                requests:
                  cpu: '200m'              
    ```

    -   通过`alicloud.autoscaling.service.knative.dev/timeseries`指定弹性策略名称。
    -   通过`autoscaling.knative.dev/class: "ppa.autoscaling.knative.dev"`指定定时弹性插件。
    -   多指标支持。社区Knative仅支持同时设置一个指标，通过`alicloud.autoscaling.knative.dev/metric-target`可以设置多个HPA弹性指标。例如同时支持CPU和内存指标，则设置为`cpu:50,memory:50`。
    -   通过`autoscaling.knative.dev/minScale: "1"`设置弹性策略最小值。
    -   通过`autoscaling.knative.dev/maxScale: "10"`设置弹性策略最大值。
    **说明：** 以下为弹性策略最小值（minScale）、最大值（maxScale）和定时弹性值之间的关系：

    -   HPA最小值=Min（minScale, 定时弹性值）
    -   HPA最大值=Max（maxScale, 定时弹性值）

## 结果验证

开启定时弹性后，实例变化趋势如下图所示。

![12](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1176240061/p167946.png)

