---
keyword: [configure schedule-based autoscaling, Knative]
---

# Configure schedule-based autoscaling in Knative

Serverless Kubernetes \(ASK\) clusters support schedule-based autoscaling. This feature allows you to create schedule-based autoscaling policies for the system to allocate resources. This feature is integrated with the resource scaling capabilities of Horizontal Pod Autoscaler \(HPA\). This ensures that the system can automatically allocate sufficient resources to cope with spikes in workloads. This topic describes how to configure and apply a schedule-based autoscaling policy in Knative.

[Enable Knative](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Knative/Component management/Enable Knative.md)

## Procedure

1.  Configure a schedule-based autoscaling policy.

    You can use a ConfigMap to define a schedule-based autoscaling policy. Each Knative Service is associated with one policy. The following code block shows a configuration example:

    ```
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: cron-autoscaler
      namespace: default
    data:
      jobs: |
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

    -   In the `schedule` field, the first number indicates the day of a month. The second number indicates the month of a year. The remaining indicates the days of a week. The format of the value is `* * 1-5`. In the preceding example, 1-5 indicates Monday to Friday, and 0,6 indicates Saturday and Sunday.
    -   The format of the value in the `timeseries``timeseries` field is `hour: minute: second`.
    **Note:**

    -   Policy updates apply to all Knative Services that are associated with the policy.
    -   If you delete the policy, the change does not apply to the Knative Services that are associated with the policy. The Knative Services do not scale the number of resources to zero.
2.  Apply the schedule-based autoscaling policy.

    After you configure a schedule-based autoscaling policy, you can apply the policy to Knative Services.

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

    -   Set `alicloud.autoscaling.service.knative.dev/timeserie` to specify the policy name.
    -   Set `autoscaling.knative.dev/class: "ppa.autoscaling.knative.dev"` to specify the schedule-based autoscaling component.
    -   Specify multiple HPA metrics. Open source Knative allows you to specify only one HPA metric. In ASK Knative, you can specify multiple HPA metrics by setting `alicloud.autoscaling.knative.dev/metric-target`. For example, you can specify the number of CPU cores and the amount of memory resources by setting the parameter to `cpu:50,memory:50`.
    -   Set `autoscaling.knative.dev/minScale: "1"` to specify the minimum number of pods that must be guaranteed.
    -   Set `autoscaling.knative.dev/maxScale: "10"` to specify the maximum number of pods that are allowed.
    **Note:** The following formulas are used to determine the maximum \(`maxScale`\) and minimum \(`minScale`\) numbers of pods for schedule-based autoscaling:

    -   Minimum number of pods = min\(autoscaling.knative.dev/minScale,Number of pods specified in the schedule-based autoscaling policy\)
    -   Maximum number of pods = max\(autoscaling.knative.dev/maxScale,Number of a resource specified in the schedule-based autoscaling policy\)

## Verify the result

Check the changes in the number of pods after you apply the schedule-based autoscaling policy. The following trend chart shows an example.

![12](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7701221261/p167946.png)

