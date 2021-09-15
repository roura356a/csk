---
keyword: [alicloud-monitor-controller, release notes, component introduction, usage notes]
---

# alicloud-monitor-controller

The alicloud-monitor-controller component is a system component provided by Container Service for Kubernetes \(ACK\) for integration with CloudMonitor. This topic introduces alicloud-monitor-controller and provides usage notes and release notes of alicloud-monitor-controller.

## Introduction

The alicloud-monitor-controller component is a system component provided by ACK for integration with CloudMonitor. When an application is created, modified, or deleted, alicloud-monitor-controller automatically updates the application metadata to CloudMonitor. This allows you to monitor the containers that are provisioned for the application. In addition, alicloud-monitor-controller provides alert templates. You can enable this feature in the console.

## Usage notes

For more information about how to use alicloud-monitor-controller, see [Alert management](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Alert management.md).

## Release notes

**August 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.5.12-f7aedb4a-aliyun|registry.\{REGION\}.aliyuncs.com/acs/alicloud-monitor-controller:v1.5.12-f7aedb4a-aliyun|2021-08-23|The alert configurations are optimized.|No impact on workloads|
|v1.5.10-a75e4ee2-aliyun|registry.\{REGION\}.aliyuncs.com/acs/alicloud-monitor-controller:v1.5.10-a75e4ee2-aliyun|2021-08-19|The alert configurations are optimized.|No impact on workloads|

**April 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.5.7-e1d5de8a-aliyun|registry.\{REGION\}.aliyuncs.com/acs/alicloud-monitor-controller:v1.5.7-e1d5de8a-aliyun|2021-04-23|Event alerting is supported. Alerts can be configured based on events by using CustomResourceDefinitions \(CRDs\).|No impact on workloads|

**July 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.4.0-49ff2362-aliyun|registry.\{REGION\}.aliyuncs.com/acs/alicloud-monitor-controller:v1.4.0-49ff2362-aliyun|2020-07-24|-   Metric collection is supported in a cluster where both Windows node pools and Linux node pools are deployed.
-   The following scaling periods are supported: 15 seconds, 20 seconds, 30 seconds, and 60 seconds.
-   The issue that Horizontal Pod Autoscaler \(HPA\) is triggered during rolling updates of applications is fixed.

|No impact on workloads|

## Troubleshooting

**No application group is found in the CloudMonitor console**

Perform the following checks:

-   Check whether the pod where alicloud-monitor-controller is deployed runs as normal in the kube-system namespace.
-   Check whether alicloud-monitor-controller is upgraded to the latest version. We recommend that you upgrade the component to the latest version.
-   Check the log file of alicloud-monitor-controller for connection timeout errors and SDK errors.

If no error is found after you perform the preceding checks, use the following ticket template to [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

Ticket template

1.  Is alicloud-monitor-controller upgraded to the latest version?

    Yes, alicloud-monitor-controller is upgraded to the latest version.

2.  Are SDK errors or connection timeout errors found in the log file of alicloud-monitor-controller? If these errors are found, delete the pod.

    No such errors are found. The issue persists after the pod is recreated.

3.  Provide the complete log of alicloud-monitor-controller in the ticket.

    Compress the log file and then upload it.


**No data is found in the application group in the CloudMonitor console**

Perform the following checks:

Check whether the name of the instance in the application group is the same as that of the pod where alicloud-monitor-controller is deployed.

-   If the instance name is different from the pod name, perform the steps provided in the preceding section.
-   If the instance name is the same as the pod name, check whether the pod where the metrics-server component is deployed runs as normal. Check whether log data is collected as normal. If the `Successful write 164190 bytes metrics to monitor server` message is found in the log, this indicates that log data is collected as normal.

If no error is found after you perform the preceding checks, use the following ticket template to [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

Ticket template

1.  Is the name of the instance in the application group the same as that of the pod where alicloud-monitor-controller is deployed?

    Yes, the names are the same.

2.  Can the log of metrics-server in the kube-system namespace be printed as normal?

    Yes, the log of metrics-server in the kube-system namespace can be printed as normal.

3.  Provide the cluster ID, application name, and pod name in the ticket.

