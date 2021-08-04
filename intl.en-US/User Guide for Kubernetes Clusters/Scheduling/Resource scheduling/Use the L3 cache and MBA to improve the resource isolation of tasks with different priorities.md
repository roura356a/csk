---
keyword: [control the L3 cache and MBA, improve the resource isolation of tasks with different priorities, Resource Director Technology \(RDT\)]
---

# Use the L3 cache and MBA to improve the resource isolation of tasks with different priorities

Tasks of different priorities are usually deployed on the same machine. This may cause tasks to compete for computing resources. As a result, the quality of service \(QoS\) of your service cannot be ensured. The Resource Director Technology \(RDT\) controls the Last Level Cache \(L3 cache\) that can be used by workloads of different priorities. RDT also uses the Memory Bandwidth Allocation \(MBA\) feature to control the memory bandwidth that can be used by workloads. This isolates the L3 cache and memory bandwidth used by workloads, ensures the QoS of high-priority workloads, and improves overall resource utilization. This topic describes how to improve the resource isolation of tasks with different priorities by controlling the L3 cache and using the MBA feature.

The resource-controller component is installed. For more information, see [resource-controller](/intl.en-US/Release notes/System Component change Records/Other SQL statements/resource-controller.md).

RDT provides a framework that consists of several component features for the monitoring and allocation capabilities of the L3 cache and memory. These components include Cache Monitoring Technology \(CMT\), Cache Allocation Technology \(CAT\), Code and Data Prioritization \(CDP\), Memory Bandwidth Monitoring \(MBM\), and MBA. This framework enables the tracking and control of shared resources, such as the L3 cache and dynamic random access memory \(DRAM\) bandwidth, that are used by applications, containers, or VMs running on the platform. RDT helps you detect the noisy neighbor effect and reduce performance interference. This ensures the performance of key workloads in complex environments. For more information, see [resource-director-technology](https://www.intel.com/content/www/us/en/architecture-and-technology/resource-director-technology.html).

To make full use of the computing resources of a node, workloads of different priorities are usually deployed on the node. For example, a latency-sensitive \(LS\) workload \(with a high priority\) and a best-effort \(BE\) workload \(with a low priority\) can be deployed on the same node. This may cause these workloads to compete for computing resources. If resource isolation is not enabled for these workloads, they preempt the L3 cache and memory bandwidth on an equal footing. As a result, the LS workload may not be allocated with sufficient resources, which downgrades its performance.

To solve this issue, resource-controller enables resource isolation capabilities for workloads of different priorities based on RDT. resource-controller allows you to set upper limits for the L3 cache and memory bandwidth that can be used by BE workloads. This way, you can provide sufficient L3 cache and memory bandwidth for LS workloads to ensure the stability and efficiency of your service. In addition, resource-controller can dynamically adjust the limits on the resource usage of BE workloads, which allows you to further improve the overall resource utilization in specific scenarios.

![L3/MBA Control](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9640367261/p292233.png)

## Procedure

1.  Create a resource-controller-configmap.yaml file based on the following YAML template:

    ```
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: resource-controller-config
      namespace: kube-system
    data:
      default.qosClass: LS # LS (Latency Sensitive with High priority scheduling), BE (Best Effort with Low priority scheduling)
        default.cpushare.qos.low.namespaces: "besteffort-ns1, besteffort-ns2" # By default NULL, suppress existing BE task within specified namespaces immediately.
      default.cpushare.qos.low.l3-percent: "10" #optional
      default.cpushare.qos.low.mb-percent: "20" #optional
    ```

    Where:

    -   `default.cpushare.qos.low.l3-percent` specifies the maximum percentage of the L3 cache that can be used by BE workloads.
    -   `default.cpushare.qos.low.mb-percent` specifies the maximum percentage of the memory bandwidth that can be used by BE workloads.
    -   `default.cpushare.qos.low.mb-percent` and `default.cpushare.qos.low.l3-percent` take effect for all of BE workloads. The valid values for these fields are based on the machines where your workloads are deployed.
    **Note:** Modifications to the values of `default.cpushare.qos.low.l3-percent` and `default.cpushare.qos.low.mb-percent` in the `resource-controller-config` ConfigMap take effect for both existing and newly created BE workloads.

2.  Deploy an application and add annotations.

    Use the following YAML template to create a video transcoding application that has a low priority. You must first upload a video file to the /root/mp4 directory of the node on which the application is to be deployed. You must also specify the video file name in the YAML template.

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      generateName: ffmpeg-convert-mp4-job-nomi-real-
    spec:
      backoffLimit: 1
      parallelism: 1
      template:
        metadata:
          annotations:
            alibabacloud.com/qosClass: "BE"    # Set the value to BE to mark this application as a BE workload. 
            alibabacloud.com/cache-qos: "low"  # This field is optional. Valid values: low, middle, and high. 
        spec:
          containers:
          - image: jrottenberg/ffmpeg
            name: ffmpeg
            imagePullPolicy: IfNotPresent
            volumeMounts:
            - mountPath: /root/mp4 
              name: mp4-volume
            resources:
              requests:
                cpu: "4"
                memory: 6Gi
              limits:
                cpu: "4"
                memory: 6Gi
            command: ["/bin/sh"]
            args: ["-c","while true;do ffmpeg -i /root/mp4/*.mp4 -c:v libx264 -crf 2 -threads 2 destination.flv -y;sleep 1;done"] # Replace *.mp4 with the name of the video file to be transcoded.
          volumes:
          - name: mp4-volume
            hostPath:
              path: /root/mp4
              type: Directory
          dnsPolicy: ClusterFirst
          restartPolicy: OnFailure
    ```

    Where:

    -   `alibabacloud.com/qosClass` specifies the workload type. The default value is specified in the `default.qosClass` field of the resource-controller-configmap.yaml file. A value of LS indicates the LS type. A value of BE indicates the BE type.
    -   `alibabacloud.com/cache-qos` specifies the default resource limit of BE workloads. Valid values:
        -   `low`: `l3-percent=10%,mb-percent=20%`.
        -   `middle`: `l3-percent=30%,mb-percent=50%`.
        -   `high`: `l3-percent=50%,mb-percent=100%`.
    -   The following limits apply to the default settings of `l3-percent` and `mb-percent`:
        -   The default settings take effect only when the `default.cpushare.qos.low.l3-percent` field in the resource-controller-configmap.yaml file is empty .
        -   If the limits on the L3 cache and memory bandwidth are not specified in the resource-controller-configmap.yaml file, the resource usage of BE workloads is subject to the default settings specified by `alibabacloud.com/cache-qos`.
    **Note:** The settings of `l3-percent` and `mb-percent` take effect only for BE workloads. The sum of the values of `l3-percent` and `mb-percent` for LS workloads is always 100%.


