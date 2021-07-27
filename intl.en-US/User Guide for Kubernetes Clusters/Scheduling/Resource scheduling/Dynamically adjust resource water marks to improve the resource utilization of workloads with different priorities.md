---
keyword: [dynamically adjust water mark, improve resource usage, control QoS]
---

# Dynamically adjust resource water marks to improve the resource utilization of workloads with different priorities

Workloads with different priorities are usually deployed on the same node to make full use of the computing resources of the node. The resource usage of workloads with different priorities varies by time. You can dynamically adjust the resource usage and resource water marks of workloads based on priorities to improve the efficiency of your workloads. This topic describes how to dynamically adjust water marks, use CronJobs, and enforce isolation policies to improve the resource utilization of workloads with different priorities.

The resource-controller component is installed. For more information, see [resource-controller](/intl.en-US/Release notes/System Component change Records/Other SQL statements/resource-controller.md).

Workloads with different priorities are usually deployed on the same node to make full use of the computing resources of the node. For example, a latency-sensitive \(LS\) workload \(with a high priority\) and a best-effort \(BE\) workload \(with a low priority\) can be deployed on the same node. Different workloads usually have different peak hours. A workload requires different amounts of resources in different time periods. For example, LS workloads require a higher amount of resources in the daytime than in the nighttime.

In the daytime, you can dynamically adjust resource water marks to reduce the resource usage of BE workloads. This allows you to improve the resource usage of LS workloads and ensures the quality of service \(QoS\) of these workloads. In the nighttime, you can increase the resource water marks of BE workloads to improve their resource usage. This allows you to make full use of idle resources and improves overall resource utilization.

![Dynamically adjust water marks](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3574386261/p290429.png)

## Dynamically adjust resource water marks to control the resource usage of low-priority workloads

1.  Create a ConfigMap.

    1.  Create a resource-controller-config.yaml file based on the following code:

        ```
        apiVersion: v1
        kind: ConfigMap
        metadata:
          name: resource-controller-config
          namespace: kube-system
        data:
          default.qosClass: LS #LS (Latency Sensitive with High priority scheduling), BE (Best Effort with Low priority scheduling).
          default.cpushare.qos.low.cpu-watermark: "50" # 25, 50, and 100. Set maximum water mark of workload of low qos.
          default.cpushare.qos.low.namespaces: "besteffort-ns1, besteffort-ns2" # By default NULL, suppress existing workload within specified namespaces immediately.
          default.cpushare.qos.high.force-reserved: "false" # By default false, true: turn on this switch to reserve dedicated CPUs for high priority tasks (LS).
        ```

        |Parameter|Description|
        |---------|-----------|
        |default.cpushare.qos.low.cpu-watermark|Specifies the maximum percentage of resources that can be occupied by low-priority tasks.|
        |default.cpushare.qos.low.namespaces|Specifies the namespace of the low-priority tasks that you want to control.|
        |default.cpushare.qos.high.force-reserved|Specifies whether to isolate high-priority tasks and low-priority tasks by scheduling the tasks to different CPUs. This parameter takes effect after the ConfigMap is created. For existing tasks, the parameter applies only to low-priority tasks. High-priority tasks are not affected. |

    2.  Run the following command to create the ConfigMap:

        ```
        kubectl create -f resource-controller-config.yaml
        ```

2.  Create an application.

    The following example shows how to create a low-priority task that calculates the value of Pi \(π\). The `annotations` parameter is set.

    1.  Create a cal-pi.yaml file based on the following code:

        ```
        apiVersion: v1
        kind: Pod
        metadata:
          name: cal-pi
          annotations: 
            alibabacloud.com/qosClass: "BE" 
        spec:
          restartPolicy: Never
          containers:
          - image: registry.cn-zhangjiakou.aliyuncs.com/xianlu/java-pi
            name: cal-pi
            resources:
              requests:
                cpu: 4
              limits:
                cpu: 4  # Specify the value of resources.limit.cpu. 
            env:
            - name: limit
              value: "20000"
            - name: threadNum
              value: "3000"
        ```

        `alibabacloud.com/qosClass` specifies the workload type:

        -   The default value is specified in the `default.qosClass` field of the resource-controller-configmap.yaml file. A value of `LS` indicates the LS type. A value of `BE` indicates the BE type.
        -   You can also set this field in the following parameters:
            -   The `metadata` parameter of pod configurations.
            -   The `spec.metada` parameter of Deployment configurations.
            -   The `template.metadata` parameter of DaemonSet, StatefulSet, and Job configurations.
    2.  Run the following command to create the application:

        ```
        kubectl create -f cal-pi.yaml
        ```


## Use CronJobs to control the resource usage of low-priority tasks by time

You can create a CronJob to configure the water marks of low-priority tasks. You can modify the `schedule` and `json` parameters to adjust the water marks of low-priority tasks by time. The schedule parameter specifies the task execution schedule. The json parameter specifies the resource configurations.

In this example, all low-priority BE tasks are allowed a maximum CPU usage of 25% starting from 07:00. This ensures that high-priority tasks have sufficient CPU resources.

1.  Create a mix-strategy-1.yaml file based on the following code:

    ```
    apiVersion: batch/v1beta1
    kind: CronJob
    metadata:
      name: mix-strategy-1
      namespace: kube-system
    spec:
      schedule: "0 7 * * *"
      jobTemplate:
        spec:
          completions: 1
          parallelism: 1
          template:
            spec:
              containers:
                - args:
                  - $(JSON_INPUT)
                  env:
                  - name: JSON_INPUT
                    value:
                       '{
                        "apiVersion":"v1",
                        "kind":"ConfigMap",
                        "metadata":{
                          "name":"resource-controller-config",
                          "namespace":"kube-system"
                        },
                        "data":{
                          "default.cpushare.qos.high.force-reserved": "false",
                          "default.cpushare.qos.low.cpu-watermark":"25",
                          "default.qosClass":"LS",
                          "default.cpushare.qos.low.namespaces": "low-qos-workload-namespace"
                        }
                        }
                      '
                  name: strategy
                  command: ["change-config"]
                  image: registry.cn-hangzhou.aliyuncs.com/shuangkun/change-config:v1
                  imagePullPolicy: Always
              serviceAccount: admin
              restartPolicy: OnFailure
    ```

2.  Run the following command to create a CronJob:

    ```
    kubectl create -f mix-strategy-1.yaml
    ```


Similarly, you can use CronJobs to control the water marks of other low-priority tasks in other time periods. This helps improve overall resource utilization.

## Enforce isolation policies to control the resource usage of tasks

We recommend that you enforce isolation policies when the CPU usage of low-priority and high-priority tasks is both lower than 50%. You can set `default.cpushare.qos.high.force-reserved` to `true` and set `default.cpushare.qos.low.cpu-watermark` to 50. This isolates high-priority tasks and low-priority tasks on different non-uniform memory access \(NUMA\) nodes and core complexes \(CCXes\), and provides a higher-performance environment for high-priority tasks

1.  Modify the resource-controller-config.yaml file as shown in the following code:

    ```
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: resource-controller-config
      namespace: kube-system
    data:
      default.qosClass: LS #LS (Latency Sensitive with High priority scheduling), BE (Best Effort with Low priority scheduling)
      default.cpushare.qos.low.cpu-watermark: "50" # 25, 50, and 100. Set maximum water mark of workload of low qos
      default.cpushare.qos.low.namespaces: "besteffort-ns1, besteffort-ns2" # By default NULL, suppress existing workload within specified namespaces immediately.
      default.cpushare.qos.high.force-reserved: "true" # By default false, true: turn on this switch to reserve dedicated CPUs for high priority tasks (LS)
    ```

2.  Run the following command to modify the ConfigMap:

    ```
    kubectl apply -f resource-controller-config.yaml
    ```


