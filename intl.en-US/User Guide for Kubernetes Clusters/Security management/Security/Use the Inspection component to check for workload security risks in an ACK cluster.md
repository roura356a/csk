---
keyword: [inspection, workload configurations, security risks]
---

# Use the Inspection component to check for workload security risks in an ACK cluster

This topic describes how to use the Inspection component to check for real-time security risks of running applications in an Alibaba Cloud Container Service for Kubernetes \(ACK\) cluster and how to view inspection reports.

A managed or dedicated ACK cluster of Kubernetes 1.14.8 or later is created. For more information, see [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md).

## Inspect workloads in an ACK cluster

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, choose **Security** \> **Inspections**.

5.  If you have not installed the Inspection component, click **Install** under **Install Inspection Component**, and then click **Get Started**. If you have installed the Inspection component, skip this step.

6.  On the Inspections page, click **Inspect** in the upper-right corner.

7.  After the inspection is complete, click **Refresh** to refresh the page and view the inspection report.


## Inspection report

An inspection report provides the result of the latest inspection, including the following information:

-   Overview of the inspection result. This includes the number of check items, the quantity and ratio of each resource object, and the overall health status.
-   Statistics of each inspection category. This includes the results of image, network, resource, and security inspections, and the results of health checks.
-   Detailed inspection results of each workload configuration. The results include resource categories, resource names, namespaces, inspection types, check items, and inspection results of each workload.

The following table lists the check items.

|Check item|Content and potential security risks|Suggestion|
|----------|------------------------------------|----------|
|hostNetworkSet|Checks whether the Pod Spec contains the hostNetwork:true setting. This setting indicates that the pod uses the network namespace of the host. If the hostNetwork:true setting is used, the host network may be attacked by containers in the pod and the data transfer in the host network may be sniffed.|Delete the hostNetwork:true setting from the Pod Spec.

Examples:

![1](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4355359951/p129766.png) |
|hostIPCSet|Checks whether the Pod Spec contains the hostIPC:true setting. This setting indicates that the pod uses the IPC namespace of the host. If the hostIPC:true setting is used, the host processes may be attacked by containers in the pod and the process data may be sniffed.|Delete the hostIPC:true setting from the Pod Spec.

Examples:

![2](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4355359951/p129768.png) |
|hostPIDSet|Checks whether the Pod Spec contains the hostPID:true setting. This setting indicates that the pod uses the PID namespace of the host. If the hostPID:true setting is used, containers in the pod may attack the host processes and collect process data.|Delete the hostPID:true setting from the Pod Spec. Examples:

![3](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4355359951/p129769.png) |
|hostPortSet|Checks whether the Pod Spec contains the hostPort parameter. This parameter specifies the host port to which the listening port of the pod is mapped. If the hostPort parameter is specified, the host port may be occupied without authorization and the container port may receive unexpected requests.|Delete the hostPort parameter from the Pod Spec. Examples:

![4](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4355359951/p129770.png) |
|runAsRootAllowed|Checks whether the Pod Spec contains the runAsNonRoot:true setting. This setting indicates that a user can run containers in a pod as a root user. If the runAsNonRoot:true setting is used, malicious processes in containers may intrude into your applications, hosts, or even the cluster.|Add the runAsNonRoot:true setting to the Pod Spec. Examples:

![5](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4355359951/p129771.png) |
|runAsPrivileged|Checks whether the Pod Spec contains the privileged:true setting. This setting indicates that a user can run containers in privileged mode. If the privileged:true setting is used, malicious processes in containers may intrude into your applications, hosts, or even the cluster.|Delete the privileged:true setting from the Pod Spec. Examples:

![6](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4355359951/p129772.png) |
|privilegeEscalationAllowed|Checks whether the Pod Spec contains the allowPrivilegeEscalation:true setting. This setting indicates that the child processes of a container is granted privileges higher than those of its parent process. If the allowPrivilegeEscalation:true setting is used, malicious processes in containers may be granted escalated privileges and perform unauthorized operations.|Delete the allowPrivilegeEscalation:true setting from the Pod Spec. Examples:

![7](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4355359951/p129773.png) |
|capabilitiesAdded|Checks whether the Pod Spec contains the capabilities parameter. This parameter is used to enable Linux capabilities for processes in the containers, such as the SYS\_ADMIN, NET\_ADMIN, and ALL capabilities. If the privileged:true setting is used, malicious processes in containers may intrude into your applications, cluster components, or even the cluster.|Retain only the required Linux capabilities in the parameter. Example 1:

![8](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4355359951/p129774.png)

Example 2:

![9](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4355359951/p129775.png) |
|notReadOnlyRootFileSystem|Checks whether the Pod Spec contains the readOnlyRootFilesystem:true setting. This setting indicates that the root file system mounted to containers is read-only. If the readOnlyRootFilesystem:true setting is not used, malicious processes in the containers may modify the root file system.|Add the readOnlyRootFilesystem:true setting to the Pod Spec. If you want to modify files in a specified directory, you can pass the volumeMounts parameter to the Pod Spec. Examples:

![10](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5355359951/p129776.png)

If you want to modify files in a specified directory, you can pass the volumeMounts parameter to the Pod Spec.

Examples:

![11](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5355359951/p129777.png) |
|cpuRequestsMissing|Checks whether the Pod Spec contains the resources.requests.cpu parameter. This parameter specifies the minimum CPU resources that are required to run each container. If the resources.requests.cpu parameter is not specified, the pod may be scheduled to nodes that have insufficient CPU resources. This leads to slow processes in the containers.|Add the resources.requests.cpu parameter to the Pod Spec. Examples:

![12](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5355359951/p129778.png) |
|cpuLimitsMissing|Checks whether the Pod Spec contains the resources.limits.cpu parameter. This parameter specifies the maximum CPU resources that are used to run each container. If the resources.requests.cpu parameter is not specified, abnormal processes in the containers may exhaust CPU resources of the node or even the cluster.|Add the resources.limits.cpu parameter to the Pod Spec. Examples:

![13](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5355359951/p129779.png) |
|memoryRequestsMissing|Checks whether the Pod Spec contains the resources.requests.memory parameter. This parameter specifies the minimum memory resources that are required to run each container. If the resources.requests.memory parameter is not specified, the pod may be scheduled to a node that have insufficient memory resources. As a result, processes in the containers may be terminated due to an OOM \(out-of-memory\) error.|Add the resources.requests.memory parameter to the Pod Spec. Examples:

![14](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5355359951/p129780.png) |
|memoryLimitsMissing|Checks whether the Pod Spec contains the resources.limits.memory parameter. This parameter specifies the maximum memory resources that are used to run each container. If the resources.limits.memory parameter is not specified, abnormal processes in the containers may exhaust memory resources of the node or even the cluster.|Add the resources.limits.memory parameter to the Pod Spec. Examples:

![15](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5355359951/p129781.png) |
|readinessProbeMissing|Checks whether the Pod Spec contains the readinessProbe parameter. This parameter specifies whether readiness probes are configured for containers. Readiness probes are used to check whether applications in containers are ready to accept requests. If the readinessProbe parameter is not specified, service exceptions may occur when applications cannot process requests.|Add the readinessProbe parameter to the Pod Spec. Examples:

![16](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5355359951/p129783.png) |
|livenessProbeMissing|Checks whether the Pod Spec contains the livenessProbe parameter. This parameter specifies whether liveness probes are configured for containers. Liveness probes are used to check whether the system needs to restart the containers to resolve application anomalies. If the livenessProbe parameter is not specified, service exceptions may occur when application anomalies can be resolved only by restarting the relevant containers.|Add the livenessProbe parameter to the Pod Spec. Examples:

![17](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5355359951/p129784.png) |
|tagNotSpecified|Checks whether the image parameter in the Pod Spec specifies an image tag or whether the value of the parameter is set to latest. If no image tag is specified or the value of the parameter is set to latest, service exceptions may occur when the containers use the wrong image version.|Modify the image parameter in the Pod Spec by specifying an image tag. Do not set the value of the parameter to latest. Examples:

![18](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5355359951/p129785.png) |

-   [Configure a Security Context for a Pod or Container](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)
-   [Configure Liveness, Readiness and Startup Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)

  


