---
keyword: [RAM authorization, RBAC authorization, ACK cluster authorization]
---

# Authorization overview

The authorization mechanism of Container Service for Kubernetes \(ACK\) consists of Resource Access Management \(RAM\) authorization and role-based access control \(RBAC\) authorization. This topic describes RAM authorization and RBAC authorization and how to use these authorizations.

## RAM authorization

In scenarios where RAM is integrated with enterprise account systems, operations and maintenance \(O&M\) engineers frequently manage cloud resources as RAM users. By default, a RAM user is not authorized to call the APIs of cloud resources. To allow a RAM user to call the API, you must grant the required permissions to the RAM user.

If you want to scale a cluster, add nodes to a cluster, or access the cluster as a RAM user, you must [grant the required permissions to the RAM user](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create a custom RAM policy.md). For more information, see [Create a custom RAM policy](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create a custom RAM policy.md).

You can perform RAM authorization by using the following methods:

-   Attach system policies: You can use this method to grant a RAM user read and write permissions on all clusters that belong to the current Alibaba Cloud account. To grant a RAM user the permissions to manage all clusters that belong to the current Alibaba Cloud account, we recommend that you attach system policies to the RAM user. For more information about how to attach system policies to a RAM user, see [Grant permissions to a RAM user](/intl.en-US/RAM User Management/Authorization management/Grant permissions to a RAM user.md).

    The following table describes the commonly used system policies for ACK. You can select the system policies based on your requirements.

    |System policy|Description|
    |-------------|-----------|
    |AliyunCSFullAccess|Allows a RAM user to fully control all ACK clusters that belong to the current Alibaba Cloud account.|
    |AliyunVPCReadOnlyAccess|Allows a RAM user to specify a virtual private cloud \(VPC\) when the RAM user creates a cluster.|
    |AliyunECSReadOnlyAccess|Allows a RAM user to add existing nodes to a specified cluster and view the details of nodes.|
    |AliyunContainerRegistryFullAccess|Allows a RAM user to fully control the images of all workloads that belong to the current Alibaba Cloud account.|
    |AliyunLogReadOnlyAccess|Allows a RAM user to specify an existing Log Service project to store auditing logs when the RAM user creates a cluster and view the inspection details of a specified cluster.|
    |AliyunAHASReadOnlyAccess|Allows a RAM user to enable the cluster topology feature.|
    |AliyunRAMFullAccess|Allows a RAM user to manage the authorization of all RAM users of the current Alibaba Cloud account.|
    |AliyunYundunSASReadOnlyAccess|Allows a RAM user to view the runtime monitoring data of a specified cluster.|
    |AliyunARMSReadOnlyAccess|Allows a RAM user to view the Prometheus monitoring state of a specified cluster.|
    |AliyunKMSReadOnlyAccess|Allows a RAM user to enable Secret encryption when the RAM user creates a professional managed Kubernetes cluster.|

-   Attach custom policies: You can use this method to implement fine-grained access control on cloud resources for a RAM user. If a RAM user requires permissions for custom development by using SDKs, you can use this method to grant the RAM user the permissions to call specified API operations.

    For more information about how to attach custom policies to a RAM user for cluster-level access control, see [Create a custom RAM policy](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create a custom RAM policy.md).

    For example, if a RAM user requires read permissions on a specified Object Storage Service \(OSS\) bucket, you can create a custom policy based on the following content and attach it to the RAM user.

    ```
    {
        "Version": "1",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                          "oss:ListBuckets",
                          "oss:GetBucketStat",
                          "oss:GetBucketInfo",
                          "oss:GetBucketTagging",
                          "oss:GetBucketAcl" 
                          ],    
                "Resource": "acs:oss:*:*:*"
            },
            {
                "Effect": "Allow",
                "Action": [
                    "oss:ListObjects",
                    "oss:GetBucketAcl"
                ],
                "Resource": "acs:oss:*:*:myphotos"
            },
            {
                "Effect": "Allow",
                "Action": [
                    "oss:GetObject",
                    "oss:GetObjectAcl"
                ],
                "Resource": "acs:oss:*:*:myphotos/*"
            }
        ]
    }
    ```


## RBAC authorization

If a RAM user requires permissions to manage Kubernetes resources in a specified cluster, you must go to the Authorizations page of the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com) console and grant the RAM user resource-level permissions, such as the permissions to view information about pods and nodes.

You can assign the following predefined roles to a RAM user. For more information, see [Assign RBAC roles to RAM users](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Assign RBAC roles to RAM users.md).

|Role|RBAC permissions on cluster resources|
|----|-------------------------------------|
|Administrator|Read and write permissions on resources in all namespaces.|
|O&M Engineer|Read and write permissions on visible resources in the console in all namespaces and read-only permissions on nodes, persistent volumes \(PVs\), namespaces, and quotas.|
|Developer|Read and write permissions on visible resources in the console in a specified namespace or all namespaces.|
|Restricted User|Read-only permission on visible resources in the console in a specified namespace or all namespaces.|
|Custom role|The permissions of a custom role are determined by the ClusterRole that you select. Before you select a ClusterRole, check the permissions of the ClusterRole and make sure that you grant only the required permissions to the RAM user.|

**Related topics**  


[FAQ about authorization management](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/FAQ about authorization management.md)

[ACK default roles](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/ACK default roles.md)

