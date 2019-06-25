# Grant a RAM user the permissions to access a Kubernetes cluster {#concept_268689 .concept}

This topic describes the RAM and RBAC permissions required by a RAM user to access a Kubernetes cluster, and the general steps to grant the required permissions to a RAM user.

## RAM permissions {#section_xgk_3k1_gz9 .section}

Alibaba Cloud Container Service for Kubernetes \(ACK\) provides the RAM permissions required to control the access to the management interface of a Kubernetes cluster. If you want to use a RAM user to scale in or scale out a Kubernetes cluster, or add nodes to the cluster, you must grant the corresponding RAM permissions to the user account. For more information, see [Create custom authorization policies](reseller.en-US/User Guide/Kubernetes cluster/Authorization management/Create custom authorization policies.md#).

The following table lists the RAM permissions that can be granted to a RAM user to access a Kubernetes cluster.

|Action|Permission|
|------|----------|
|CreateCluster|Create a cluster.|
|AttachInstances|Add existing ECS instances to a cluster.|
|ScaleCluster|Scale out a cluster.|
|GetClusters|View clusters.|
|GetClusterById|View cluster details.|
|ModifyClusterName|Modify a cluster name.|
|DeleteCluster|Delete a cluster.|
|UpgradeClusterAgent|Upgrade the cluster agent.|
|GetClusterLogs|View cluster logs.|
|GetClusterEndpoint|View the endpoint of a cluster.|
|GetClusterCerts|Download the cluster certificate.|
|RevokeClusterCerts|Revoke the cluster certificate.|
|BindSLB|Associate a cluster with an SLB instance.|
|UnBindSLB|Remove the SLB instance associated with a cluster.|
|ReBindSecurityGroup|Reassociate a security group with a cluster.|
|CheckSecurityGroup|Check the security group rules set for the cluster.|
|FixSecurityGroup|Fix the security group rules of the cluster.|
|ResetClusterNode|Reset a cluster node.|
|DeleteClusterNode|Delete a cluster node.|
|CreateAutoScale|Create the auto scaling rule for cluster nodes.|
|UpdateAutoScale|Update the auto scaling rule for cluster nodes.|
|DeleteAutoScale|Delete the auto scaling rule for cluster nodes.|
|GetClusterProjects|View applications that runs in the cluster.|
|CreateTriggerHook|Create a trigger for the application.|
|GetTriggerHook|View the trigger list of the application.|
|RevokeTriggerHook|Delete the application trigger.|
|CreateClusterToken|Create a token.|

## RBAC permissions {#section_j5k_rqk_6va .section}

ACK provides the RBAC permissions required by a RAM user to access the resources of a Kubernetes cluster by calling the API server of the cluster. For more information, see [Authorization overview](https://kubernetes.io/docs/reference/access-authn-authz/authorization/).

The following table lists the RBAC permissions that can be granted to a RAM user to access a Kubernetes cluster.

|Role|Permission|
|----|----------|
|Admin|The read and write permissions of resources in all namespaces, and those of nodes, volumes, namespaces, and quotas.|
|Operation|The read and write permissions of resources in all namespaces, and the ready permissions of nodes, volumes, namespaces, and quotas.|
|Developer|The read and write permissions of the resources in all namespaces or specified namespaces.|
|Restricted User|The read permissions of resources in all namespaces or specified namespaces.|
|Custom|The permissions of the RAM user depend on the cluster role you select. Confirm the permissions that your selected cluster role has on resources before authorization, to avoid inappropriate permissions granted to the RAM user.|

## Grant a RAM user the permissions to access a Kubernetes cluster {#section_bmd_wzi_cc4 .section}

1.  Grant a RAM user the RAM permissions to access a Kubernetes cluster.

    The following are the two types of required RAM permissions:

    -   Read permissions: Allow a RAM user to view basic cluster information, such as the cluster configuration and kubeconfig.
    -   Write permissions: Allow a RAM user to scale in or scale out a Kubernetes cluster, upgrade the cluster, delete a node from the cluster, add a node to the cluster, and perform other actions to manage cluster resources.
    The following shows a RAM policy file that contains a read permission:

    ``` {#codeblock_kqa_gd1_gr2}
    {
      "Statement": [
        {
          "Action": "cs:Get*",
          "Effect": "Allow",
          "Resource": [
            "acs:cs:*:*:cluster/<yourclusterID>"
          ]
        }
      ],
      "Version": "1"
    }
    ```

    For information about the specific steps in the procedure, see [Create custom authorization policies](reseller.en-US/User Guide/Kubernetes cluster/Authorization management/Create custom authorization policies.md#).

2.  Grant the RBAC permissions to a RAM user to access Kubernetes resources. For information about the specific steps, see [Grant Kubernetes permissions to a RAM user](reseller.en-US/User Guide/Kubernetes cluster/Authorization management/Grant Kubernetes permissions to a RAM user.md#).

