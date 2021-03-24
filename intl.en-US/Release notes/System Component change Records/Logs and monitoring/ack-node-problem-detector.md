---
keyword: [node-problem-detector, ack-node-problem-detector, monitoring of node events]
---

# ack-node-problem-detector

The ack-node-problem-detector component is used in Container Service for Kubernetes \(ACK\). This component is optimized and enhanced based on the open source Node Problem Detector \(NPD\) that is provided by the Kubernetes community. This component is used to monitor cluster nodes and integrate third-party node monitoring plug-ins. You can use ack-node-problem-detector to integrate custom monitoring plug-ins. This allows you to enhance the monitoring of nodes and detect more node anomalies.

For more information about the open source node-problem-detector, see [node-problem-detector](https://github.com/kubernetes/node-problem-detector).

For more information about the installation, scenarios, and features of ack-node-problem-detector, see [Event monitoring](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Event monitoring.md).

## Release notes

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v0.6.3-28-160499f|registry.aliyuncs.com/acs/node-problem-detector:v0.6.3-28-160499f|July 27, 2020|-   The following information can be added to out of memory \(OOM\) Killer events: the name of the relative pod, the namespace to which the pod belongs, and the user IDs \(UIDs\) of the killed processes.
-   The efficiency of the check\_fd plug-in is improved.
-   Nodes events are optimized to report that the process ID \(PID\) usage of cluster nodes exceeds the threshold.
-   Plug-ins that detect network connections are upgraded.
-   Alert plug-ins are added to send alerts when the inode usage in the system disks of cluster nodes exceeds the threshold. |

