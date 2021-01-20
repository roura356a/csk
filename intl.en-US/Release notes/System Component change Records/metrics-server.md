---
keyword: [metrics-server, release notes]
---

# metrics-server

metrics-server is a component that is provided in Container Service for Kubernetes \(ACK\). The component is designed based on the open source monitoring component Metrics Server. metrics-server provides the Metrics API operations. You can make API requests to collect resource usage metrics from nodes or pods and enable the support Horizontal Pod Autoscaler \(HPA\) feature.

For more information about the open source monitoring component Metrics Server, see [Kubernetes Metrics Server](https://github.com/kubernetes-sigs/metrics-server).

## Release notes

v0.2.2-b4bf266-aliyun

-   Metric collection is supported in an ACK cluster where both nodes that run Windows and nodes that run Linux are deployed.
-   The following scaling periods are supported: 15 seconds, 20 seconds, 30 seconds, and 60 seconds.
-   The following issue is fixed: HPA is activated during rolling updates of applications.

## Troubleshooting

**No data is returned after I run the kubectl top pod or kubectl top node command**

Perform the following checks:

Run the `kubectl get apiservice` command to check whether the API service of metrics-server is working as normal. If the API service is abnormal, check whether the pod where metrics-server is deployed can be accessed through port 443 and port 8082 inside the cluster. If you failed to access the pod, restart the pod and try again.

If the issue persists, use the following ticket template to [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

Ticket template

1.  Does the API service of metrics-server work as normal?

    Yes, the API service works as normal.

2.  Can the pod where metrics-server is deployed be accessed through port 443 and port 8082 inside the cluster?

    Yes, the pod can be accessed.

3.  Provide the cluster ID in the ticket.

**No data is returned after I run the kubectl top pod or kubectl top node command**

Perform the following checks:

-   Check whether all pods on a specified node failed to return data or only some of the pods failed to return data. If only some of the pods on the node failed to return data, check whether a timezone shift exists on the node. You can use Network Time Protocol \(NTP\) to synchronize the system time.
-   Check whether the pod where metrics-server is deployed can connect to port 10255 of the node.

If the issue persists, use the following ticket template to [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

Ticket template

1.  Did all pods on a specified node fail to return data?

    Yes, all pods fail to return data.

2.  Does a timezone shift exist on the node?

    No, a timezone shift does not exist.

3.  Can the pod where metrics-server is deployed access the specified node?

    Yes, the pod can access the node.


**HPA failed to collect metrics**

Perform the following checks:

Run the `kubectl top pod pod-id` command for the pod where metrics-server is deployed. To check the pod and node, perform the steps that are provided in the preceding section. This applies if the returned data is abnormal.

If the issue persists, use the following ticket template to [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

Ticket template

1.  Do anomalies exist in the monitoring data?

    No, no anomalies exist in the monitoring data.

2.  Run the `kubectl describe hpa hpa-name` command and provide the metadata in the ticket.

**Excessive pods are added by HPA during a rolling update**

Perform the following checks:

Check whether the metrics-server component is upgraded to the latest version. If the metrics-server component is upgraded to the latest version, configure the following startup settings in the kube-system namespace for the pod where metrics-server is deployed.

```
--metric_resolution=15s
--enable-hpa-rolling-update-skipped=true
```

If the issue persists, use the following ticket template to [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

Ticket template

1.  Is the metrics-server component upgraded to the latest version?

    Yes, the component is upgraded to the latest version.

2.  Are startup settings configured for the pod where metrics-server is deployed?

    Yes, startup settings are configured.

3.  Run the `kubectl describe hpa hpa-name` command and provide the HPA description in the ticket.

