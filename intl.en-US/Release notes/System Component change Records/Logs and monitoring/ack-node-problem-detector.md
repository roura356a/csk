---
keyword: [node-problem-detector, ack-node-problem-detector, monitoring of node events]
---

# ack-node-problem-detector

The ack-node-problem-detector component is used in Container Service for Kubernetes \(ACK\). This component is optimized and enhanced based on the open source Node Problem Detector \(NPD\) that is provided by the Kubernetes community. The ack-node-problem-detector component is used to monitor cluster nodes and integrate third-party node monitoring plug-ins. This component detects node anomalies in an ACK cluster and supports the event center feature. You can use ack-node-problem-detector to integrate custom monitoring plug-ins. This allows you to enhance the monitoring of nodes and detect more node anomalies.

## Introduction

-   The DaemonSet of ack-node-problem-detector detects node anomalies. For more information about the open source node-problem-detector, see [node-problem-detector](https://github.com/kubernetes/node-problem-detector).
-   If you specify a sink parameter when the event center feature is enabled, ack-node-problem-detector-eventer is configured for the ack-node-problem-detector component. The ack-node-problem-detector-eventer component is used to monitor events of the cluster and report the events to the event center. For more information about kube-eventer, see [kube-eventer](https://github.com/AliyunContainerService/kube-eventer).

## Instruction

For more information about the installation, scenarios, and features of ack-node-problem-detector, see [Event monitoring](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Event monitoring.md).

## Release notes

**April 2021**

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.2.5|-   ack-node-problem-detector: registry.aliyuncs.com/acs/node-problem-detector:v0.6.3-28-160499f
-   kube-eventer: registry-vpc.\_\_ACK\_REGION\_ID\_\_.aliyuncs.com/acs/kube-eventer-amd64:v1.2.4-0f5aaee-aliyun
-   kube-event-init: registry.\{ .Values.controller.regionId \}.aliyuncs.com/acs/kube-eventer-init:1.5-5e0e7c1-aliyun

|2021-04-25|-   The following issue is fixed: kube-event-init in the kube-system namespace returns the "414 Request Too Large" error when the event center feature is enabled.
-   The eventer list-watch mechanism is optimized. This prevents etcd from receiving excessive requests. For more information, see [eventer list-watch](https://github.com/AliyunContainerService/kube-eventer/pull/188).
-   The following issue is fixed: kube-eventer fails to parse the timestamps of some system events. For more information, see [fix FailedScheduling event write to sls with wrong timestamp](https://github.com/AliyunContainerService/kube-eventer/pull/168). |

**July 2020**

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v0.6.3-28-160499f|registry.aliyuncs.com/acs/node-problem-detector:v0.6.3-28-160499f|2020-07-27|-   The following information can be added to out of memory \(OOM\) Killer events: the name of the relative pod, the namespace to which the pod belongs, and the user IDs \(UIDs\) of the killed processes.
-   The efficiency of the check\_fd plug-in is improved.
-   Node events are optimized to report that the process ID \(PID\) usage of cluster nodes exceeds the threshold.
-   Plug-ins that detect network connections are upgraded.
-   Alert plug-ins are added to send alerts when the inode usage in the system disks of cluster nodes exceeds the threshold. |

