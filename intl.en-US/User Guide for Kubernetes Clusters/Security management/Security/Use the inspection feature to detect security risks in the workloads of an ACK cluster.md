---
keyword: [inspection, workload configurations, security risk]
---

# Use the inspection feature to detect security risks in the workloads of an ACK cluster

This topic describes how to use the inspection feature to check for security risks in the workloads of a Container Service for Kubernetes \(ACK\) cluster. This topic also describes how to view inspection reports. This way, you can detect potential risks in workloads at the earliest opportunity.

A managed Kubernetes cluster or a dedicated Kubernetes cluster is created, and the Kubernetes version of the cluster is 1.14.8 or later. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

## Grant RAM permissions

If you use a Resource Access Management \(RAM\) user, a message appears on the Inspections page of the ACK console, which prompts you to grant the RAM user the permissions to perform operations on the Inspections page. If the RAM use does not have the required permissions, you cannot perform operations on the Inspections page. For more information, see [Create a custom RAM policy](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create a custom RAM policy.md).

```
{
  "Statement": [
    {
      "Action": [
        "cs:DescribePolarisConfig",
        "cs:DescribePolarisJob",
        "cs:DescribePolarisCronJob",
        "cs:UpdatePolarisJob",
        "cs:UpdatePolarisCronJob"
      ],
      "Effect": "Allow",
      "Resource": [
        "acs:cs:*:*:cluster/<yourclusterID>"
      ]
    }
  ],
  "Version": "1"
}
```

If you use a RAM user and want to use the inspection feature, you must grant the RAM user read permissions on the `Log Service project` used by the `logtail-ds` component installed in your cluster. This allows the RAM user to read the log data of the `Log Service project`. Otherwise, you cannot view inspection reports. For more information, see [Use custom policies to grant permissions to a RAM user](/intl.en-US/Developer Guide/Access control RAM/Use custom policies to grant permissions to a RAM user.md).

```
{
    "Version": "1",
    "Statement": [
        {
            "Action": [
                "log:Get*",
                "log:List*"
            ],
            "Resource": "acs:log:*:*:project/<The name of the project>/*",
            "Effect": "Allow"
        }
    ]
}
```

## Grant RBAC permissions

If you use a RAM user, you must grant role-based access control \(RBAC\) permissions to the RAM user to manage the Kubernetes resources displayed on the Inspections page of the ACK console. You must grant administrator permissions to the RAM user to manage your cluster. This ensures that the RAM user has the permissions to manage the Kubernetes resources displayed on the Inspections page. For more information, see [Assign RBAC roles to RAM users](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Assign RBAC roles to RAM users.md).

## Inspect workloads in an ACK cluster

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the cluster details page, choose **Security** \> **Inspections**.

5.  If the security-inspector component is not installed, click **Install** below **Confirm**. If security-inspector is installed, skip this step.

6.  In the upper-right corner of the Inspections page, click **Inspect**.

7.  After the inspection is completed, click the Refresh button to view the inspection report.

8.  In the upper-right corner of the Inspections page, click **Configure Periodic Inspection**. In the panel that appears, you can enable or disable periodic inspections and configure inspection items.


## Inspection details

The **Inspections** page provides a table to show the inspection results of different workloads. The following features are provided to display the inspection results:

-   Displays the values of **Number of Passed Items** and **Number of Failed Items** for each inspected workload.
-   Displays the passed and failed inspection items, description of each inspection item, and suggestions for security reinforcement on the inspection details page.
-   Allows you to configure workload whitelists for inspection.
-   Allows you to select filter options from the **Namespace**, **Workload Type**, and **Passed or Failed** drop-down lists to narrow down inspection results.

## Inspection reports

An inspection report provides the results of the latest inspection, including the following information:

-   Overview of the inspection results. This includes the total number of inspection items, the number and percentage of each inspected resource object type, and the overall health status of the cluster.
-   Statistics of the following inspection categories: health checks, images, networks, resources, and security conditions.
-   Detailed inspection results of the configurations of each workload. The results include resource categories, resource names, namespaces, inspection types, inspection items, and inspection results.

The following table describes the inspection items.

|Inspection item|Inspection content and potential security risk|Suggestion|
|---------------|----------------------------------------------|----------|
|hostNetworkSet|Checks whether the pod specification of a workload contains the hostNetwork:true setting. This setting specifies that the pod uses the network namespace of the host. If the hostNetwork:true setting is specified, the host network may be attacked by containers in the pod and the data transfer in the host network may be sniffed.|Delete the hostNetwork field from the pod specification.

Example:

![1](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4355359951/p129766.png) |
|hostIPCSet|Checks whether the pod specification of a workload contains the hostIPC:true setting. This setting specifies that the pod uses the inter-process communication \(IPC\) namespace of the host. If the hostIPC:true setting is specified, containers in the pod may attack the host processes and sniff process data.|Delete the hostIPC field from the pod specification.

Example:

![2](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4355359951/p129768.png) |
|hostPIDSet|Checks whether the pod specification of a workload contains the hostPID:true setting. This setting specifies that the pod uses the process ID \(PID\) namespace of the host. If the hostPID:true setting is specified, containers in the pod may attack the host processes and collect process data.|Delete the hostPID field from the pod specification. Example:

![3](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4355359951/p129769.png) |
|hostPortSet|Checks whether the pod specification of a workload contains the hostPort field. This field specifies the host port to which the listening port of the pod is mapped. If the hostPort field is specified, the specified host port may be occupied without authorization and the container port may receive unexpected requests.|Delete the hostPort field from the pod specification. Example:

![4](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4355359951/p129770.png) |
|runAsRootAllowed|Checks whether the pod specification of a workload contains the runAsNonRoot:true setting. This setting specifies that containers in the pod are not allowed to run as the root user. If the runAsNonRoot:true setting is not specified, malicious processes in the containers may intrude into your applications, hosts, or cluster.|Add the runAsNonRoot:true setting to the pod specification. Example:

![5](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4355359951/p129771.png) |
|runAsPrivileged|Checks whether the pod specification of a workload contains the privileged:true setting. This setting specifies that containers in the pod are allowed to run in privileged mode. If the privileged:true setting is specified, malicious processes in the containers may intrude into your applications, hosts, or cluster.|Delete the privileged field from the pod specification. Example:

![6](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4355359951/p129772.png) |
|privilegeEscalationAllowed|Checks whether the pod specification of a workload contains the allowPrivilegeEscalation:false setting. This setting specifies that the child processes of a container cannot be granted higher privileges than the parent process. If the allowPrivilegeEscalation:false setting is not specified, malicious processes in the container may be granted escalated privileges and perform unauthorized operations.|Add the allowPrivilegeEscalation:false setting to the pod specification. Example:

![7](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4908915161/p129773.png) |
|capabilitiesAdded|Checks whether the pod specification of a workload contains the capabilities field. This field is used to enable Linux capabilities for processes in containers. The capabilities include SYS\_ADMIN, NET\_ADMIN, and ALL. If the capabilities field is specified, malicious processes in the containers may intrude into your applications, cluster components, or cluster.|Modify the pod specification to retain only the required Linux capabilities and remove other capabilities. If processes in the containers do not require Linux capabilities, remove all Linux capabilities. Example 1:

![8](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4355359951/p129774.png)

If processes in the containers require Linux capabilities, specify only the required Linux capabilities and remove other capabilities. Example 2:

![9](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4355359951/p129775.png) |
|notReadOnlyRootFileSystem|Checks whether the pod specification of a workload contains the readOnlyRootFilesystem:true setting. This setting specifies that the root file system mounted to containers is read-only. If the readOnlyRootFilesystem:true setting is not specified, malicious processes in the containers may modify the root file system.|Add the readOnlyRootFilesystem:true setting to the pod specification. If you want to modify files in a specific directory, set volumeMounts in the pod specification. Example:

![10](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5355359951/p129776.png)

If you want to modify files in a specific directory, set the volumeMounts field in the pod configurations.

Example:

![11](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5355359951/p129777.png) |
|cpuRequestsMissing|Checks whether the pod specification of a workload contains the resources.requests.cpu field. This field specifies the minimum CPU resources that are required to run each container. If the resources.requests.cpu field is not specified, the pod may be scheduled to a node that has insufficient CPU resources. This may lead to slow processes.|Add the resources.requests.cpu field to the pod specification. Example:

![12](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5355359951/p129778.png) |
|cpuLimitsMissing|Checks whether the pod specification of a workload contains the resources.limits.cpu field. This field specifies the maximum CPU resources that can be used to run each container. If the resources.limits.cpu field is not specified, abnormal processes in containers may consume an excessive amount of CPU resources or exhaust the CPU resources of the node or cluster.|Add the resources.limits.cpu field to the pod specification. Example:

![13](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5355359951/p129779.png) |
|memoryRequestsMissing|Checks whether the pod specification of a workload contains the resources.requests.memory field. This field specifies the minimum memory resources that are required to run each container. If the resources.requests.memory field is not specified, the pod may be scheduled to a node that has insufficient memory resources. As a result, processes in containers may be terminated when the Out of Memory \(OOM\) killer is triggered.|Add the resources.requests.memory field to the pod specification. Example:

![14](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5355359951/p129780.png) |
|memoryLimitsMissing|Checks whether the pod specification of a workload contains the resources.limits.memory field. This field specifies the maximum memory resources that can be used to by each container. If the resources.limits.memory field is not specified, abnormal processes in containers may consume an excessive amount of memory resources or exhaust the memory resources of the node or cluster.|Add the resources.limits.memory field to the pod specification. Example:

![15](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5355359951/p129781.png) |
|readinessProbeMissing|Checks whether the pod specification of a workload contains the readinessProbe field. This field specifies whether readiness probes are configured for containers. Readiness probes are used to check whether applications in the containers are ready to process requests. If the readinessProbe field is not specified, service exceptions may occur when requests are sent to applications that are not ready to process requests.|Add the readinessProbe field to the pod specification. Example:

![16](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5355359951/p129783.png) |
|livenessProbeMissing|Checks whether the pod specification of a workload contains the livenessProbe field. This field specifies whether liveness probes are configured for containers. Liveness probes are used to check whether a container restart is required to resolve application exceptions. If the livenessProbe field is not specified, the service may be interrupted when application exceptions can be resolved only by restarting containers.|Add the livenessProbe field to the pod specification. Example:

![17](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5355359951/p129784.png) |
|tagNotSpecified|Checks whether the image field in the pod specification of a workload specifies an image version or whether the value of the field is set to latest. If no image version is specified or the value of the field is set to latest, the service may be interrupted when containers use a wrong image version.|Modify the image field in the pod specification by specifying an image version. Set the field to a value other than latest. Example:

![18](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5355359951/p129785.png) |
|anonymousUserRBACBinding|Checks RBAC role bindings in the cluster and locates the configurations that allow access from anonymous users. If anonymous users are allowed to access the cluster, they may gain access to sensitive information, attack the cluster, and intrude into the cluster.|Remove the configurations that allow access from anonymous users from the RBAC role bindings. Example:

![z-1](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/7721998161/p208940.png) |

**Related topics**  


[Configure a Security Context for a Pod or Container](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/?spm=a2c4g.11186623.2.30.17fa2c50Pt0mAG)

[Configure Liveness, Readiness and Startup Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/?spm=a2c4g.11186623.2.31.17fa2c50Pt0mAG)

