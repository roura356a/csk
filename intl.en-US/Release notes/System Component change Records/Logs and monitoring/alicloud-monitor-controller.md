---
keyword: [alicloud-monitor-controller, release notes]
---

# alicloud-monitor-controller

The alicloud-monitor-controller component is a system component that is provided by Container Service for Kubernetes \(ACK\). This system component is integrated with CloudMonitor. This topic lists the latest changes to the alicloud-monitor-controller component.

## Introduction

The alicloud-monitor-controller component is a system component that is provided by ACK. This system component is integrated with CloudMonitor. When an application is created, modified, or deleted, alicloud-monitor-controller automatically updates the metadata to CloudMonitor. This allows you to monitor containers that are provisioned for an application. In addition, alicloud-monitor-controller provides predefined alert settings. Developers can use this feature in the CloudMonitor console.

## Instruction

For more information about how to use alicloud-monitor-controller, see [Alert management](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Alert management.md).

## Release notes

**April 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.5.7-e1d5de8a-aliyun|registry.\{REGION\}.aliyuncs.com/acs/alicloud-monitor-controller:v1.5.7-e1d5de8a-aliyun|2021-04-23|Alerting can be configured for container events by using CustomResourceDefinitions \(CRDs\).|No impact on workloads.|

**July 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.4.0-49ff2362-aliyun|registry.\{REGION\}.aliyuncs.com/acs/alicloud-monitor-controller:v1.4.0-49ff2362-aliyun|2020-07-24|-   Metric collection is supported in an ACK cluster where both nodes that run Windows and nodes that run Linux are deployed.
-   The following scaling periods are supported: 15 seconds, 20 seconds, 30 seconds, and 60 seconds.
-   The issue that Horizontal Pod Autoscaler \(HPA\) is activated during rolling updates of applications is fixed.

|No impact on workloads.|

## Troubleshooting

**No application group is found in the CloudMonitor console**

Perform the following checks:

-   Check whether the pod where alicloud-monitor-controller is deployed runs as normal in the kube-system namespace.
-   Check whether alicloud-monitor-controller is upgraded to the latest version. We recommend that you upgrade the component to the latest version.
-   Check the log files of alicloud-monitor-controller to fix issues such as connection timeouts and SDK errors.

If no error is found after you have performed the preceding checks, use the following ticket template to [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

Ticket template

1.  Is alicloud-monitor-controller upgraded to the latest version?

    Yes.

2.  Are SDK errors or connection timeouts found in the log files of alicloud-monitor-controller? If one of the preceding errors is found, delete the pod.

    No application group is found in the CloudMonitor console after the pod is restarted.

3.  Provide a complete log file of alicloud-monitor-controller in the ticket.

    Compress the log file and then upload it.


**No data is found in the application group in the CloudMonitor console**

Perform the following checks:

Check whether the name of the instance in the application group is the same as that of the pod where alicloud-monitor-controller is deployed.

-   If the instance name is different from the pod name, perform the steps that are provided in the preceding section.
-   If the instance name is the same as the pod name, check whether the pod where the metrics-server component is deployed runs as normal. Check whether the log data is collected as normal. If the `Successful write 164190 bytes metrics to monitor server` message is found in the log data, this indicates that the log data is collected as normal.

If no error is found after you have performed the preceding checks, use the following ticket template to [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

Ticket template

1.  Is the name of the instance in the application group the same as that of the pod where alicloud-monitor-controller is deployed?

    Yes.

2.  Can the log files of metrics-server in the kube-system namespace be printed as normal?

    Yes.

3.  Provide the cluster ID, the name of the application, and the name of the pod in the ticket.

