---
keyword: [alicloud-monitor-controller, release notes]
---

# alicloud-monitor-controller

alicloud-monitor-controller is a system component that is provided by Container Service for Kubernetes \(ACK\). This system component is integrated with Cloud Monitor. When an application is created, modified, or deleted, alicloud-monitor-controller automatically updates the metadata to Cloud Monitor. This allows you to monitor containers that are provisioned for an application. In addition, alicloud-monitor-controller provides alert settings. Developers can use this feature in the Cloud Monitor console.

## Release notes

v1.4.0-49ff2362-aliyun

-   Metric collection is supported in an ACK cluster where both nodes that run Windows and nodes that run Linux are deployed.
-   The following scaling periods are supported: 15 seconds, 20 seconds, 30 seconds, and 60 seconds.
-   The following issue is fixed: Horizontal Pod Autoscaler \(HPA\) is activated during rolling updates of applications.

## Troubleshooting

**No application group is found in the Cloud Monitor console**

Perform the following checks:

-   Check whether the pod where alicloud-monitor-controller is deployed runs as normal in the kube-system namespace.
-   Check whether alicloud-monitor-controller is upgraded to the latest version. We recommend that you upgrade the component to the latest version.
-   Check the logs of alicloud-monitor-controller to fix issues such as connection timeouts and SDK errors.

If the issue persists, use the following ticket template to [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

Ticket template

1.  Is alicloud-monitor-controller upgraded to the latest version?

    Yes, alicloud-monitor-controller is upgraded to the latest version.

2.  Are SDK errors or connection timeouts found in the logs of alicloud-monitor-controller? If one of the preceding errors is found, delete the pod.

    No application group is found in the Cloud Monitor console after the pod is restarted.

3.  Provide a complete log file of alicloud-monitor-controller in the ticket.

    Upload a compressed package of the log file.


**No data is found in the application group in the Cloud Monitor console**

Perform the following checks:

Check whether the name of the instance in the application group is the same as that of the pod where alicloud-monitor-controller is deployed.

-   If the instance name is not the same as the pod name, perform the steps that are provided in the preceding section.
-   If the instance name is the same as the pod name, check whether the pod where the metrics-server component is deployed runs as normal. Check whether logs can be printed as normal. If the `Successful write 164190 bytes metrics to monitor server` error message is found in the log file, this indicates that the log can be printed as normal.

If the issue persists, use the following ticket template to [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

Ticket template

1.  Is the name of the instance in the application group the same as that of the pod where alicloud-monitor-controller is deployed?

    Yes, both names are the same.

2.  Can the logs of metrics-server in the kube-system namespace be printed as normal?

    Yes, the logs of metrics-server in the kube-system namespace can be printed as normal.

3.  Provide the cluster ID, the name of the application, and the name of the pod in the ticket.

