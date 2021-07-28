---
keyword: [控制L3 cache和MBA, 提高不同优先级任务的隔离能力, RDT（Resource Director Technology）]
---

# 通过控制L3 cache和MBA提高不同优先级任务的隔离能力

不同优先级的任务通常会被部署在同一台机器上，这将导致不同优先级任务之间的资源竞争问题，且无法保证高优先级任务的服务质量QoS（Quality of Service）。RDT（Resource Director Technology）通过控制L3 cache（Last Level Cache，即三级缓存LLC）和MBA（Memory Bandwidth Allocation）内存带宽，对不同优先级的任务进行隔离，从而保证高优先级任务的QoS，同时提高整体的资源利用率。本文主要介绍如何通过控制L3 cache和MBA提高不同优先级任务的隔离能力。

已安装resource-controller组件。更多信息，请参见[resource-controller](/intl.zh-CN/产品发布记录/组件介绍与变更记录/其他/resource-controller.md)。

RDT提供了一个由多个组件功能（包括CMT、CAT、CDP、MBM和MBA）组成的框架，用于L3 cache和内存的监控及分配功能。该框架可以跟踪和控制平台上同时运行的多个应用程序、容器或VM使用的共享资源，例如：L3 cache和主内存（DRAM） 带宽。RDT可以帮助检测吵闹的邻居并减少性能干扰，从而确保复杂环境中关键工作负载的性能。更多信息，请参见[resource-director-technology](https://www.intel.com/content/www/us/en/architecture-and-technology/resource-director-technology.html)。

为了充分利用机器中的资源，通常会将高优先延迟敏感性LS（Latency-Sensitive）和低优先尽力而为BE（Best-Effort）的任务部署在同一台机器上，但这将导致两种不同优先级任务之间的资源竞争问题。若不加隔离控制，这两种不同优先级任务将平等地抢占L3 cache和MBA内存带宽等资源，从而无法保证给LS业务分配足够的资源，使其性能受到影响。

因此，基于RDT resource-controller提供了对不同优先级任务的隔离能力。通过resource-controller提供的L3 cache和MBA控制功能可以限制BE任务对L3 cache和MBA内存带宽的最大使用量，维持高优先延迟敏感性LS类型负载的资源占用率，保证业务运行的高效稳定。同时，resource-controller允许动态地修改对低优先尽力而为BE类型负载的资源限制程度，以适应不同的部署场景，进一步提高整体的资源使用率。

![L3/MBA Control](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2803375261/p292233.png)

## 操作步骤

1.  使用以下YAML样例，创建名称为resource-controller-configmap.yaml的文件。

    ```
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: resource-controller-config
      namespace: kube-system
    data:
      default.qosClass: LS #LS（Latency Sensitive with High priority scheduling)、BE(Best Effort with Low priority scheduling)
        default.cpushare.qos.low.namespaces: "besteffort-ns1, besteffort-ns2" # By default NULL, suppress existing BE task within specified namespaces immediately.
      default.cpushare.qos.low.l3-percent: "10" #optional
      default.cpushare.qos.low.mb-percent: "20" #optional
    ```

    其中：

    -   `default.cpushare.qos.low.l3-percent`：表示BE应用的最大可用L3 cache块数占总L3 cache块数的百分比。
    -   `default.cpushare.qos.low.mb-percent`：表示BE应用的最大可用内存带宽占全部内存带宽的百分比。
    -   `default.cpushare.qos.low.mb-percent`和`default.cpushare.qos.low.l3-percent`：均为可选设置，对所有的BE任务生效，实际设置的L3 cache与MBA内存带宽百分比值精度由应用部署所在的机器决定。
    **说明：** 增加或修改`resource-controller-config`中的`default.cpushare.qos.low.l3-percent`或`default.cpushare.qos.low.mb-percent`会对目前已有的以及此后创建的所有BE应用生效。

2.  部署应用，且在Annotation处打标。

    使用以下YAML样例，部署一个低优先级的视频转码应用，部署任务前把视频上传至所在节点的/root/mp4文件夹内，并修改YAML文件中对应的视频名称。

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
            alibabacloud.com/qosClass: "BE"    #低优先级别应用应设置为BE。
            alibabacloud.com/cache-qos: "low"  #optional, 可设置为low、middle、high。
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
            args: ["-c","while true;do ffmpeg -i /root/mp4/*.mp4 -c:v libx264 -crf 2 -threads 2 destination.flv -y;sleep 1;done"] #将*.mp4修改为待转码视频名称
          volumes:
          - name: mp4-volume
            hostPath:
              path: /root/mp4
              type: Directory
          dnsPolicy: ClusterFirst
          restartPolicy: OnFailure
    ```

    其中：

    -   `alibabacloud.com/qosClass`：表示该任务的性质，默认值在resource-controller-configmap.yaml的`default.qosClass`中设置，高优先延迟敏感性负载应设置为LS，低优先尽力而为类型负载应设置为BE。
    -   `alibabacloud.com/cache-qos`：表示BE任务的三个默认配置：
        -   设置为`low`时：`l3-percent=10%,mb-percent=20%`。
        -   设置为`middle`时：`l3-percent=30%,mb-percent=50%`。
        -   设置为`high`时：`l3-percent=50%,mb-percent=100%`。
    -   默认配置中的`l3-percent`和`mb-percent`：
        -   只会在resource-controller-configmap.yaml中`default.cpushare.qos.low.l3-percent`为空时生效。
        -   resource-controller-configmap.yaml对应值更新为空时，BE任务的资源将自动按照`alibabacloud.com/cache-qos`值恢复默认配置。
    **说明：** 以任何方式设置的`l3-percent`或`mb-percent`均只对BE任务生效，LS任务的`l3-percent`和`mb-percent`的值恒等于100%。


