# RAM authorization

Before you call an Alibaba Cloud API as a Resource Access Management \(RAM\) user, you must use the Alibaba Cloud account to create a RAM policy with the required permissions and attach the policy to the RAM user.

## Authorize a RAM user to access cloud resources

By default, a RAM user is not authorized to create or modify cloud resources by calling Alibaba Cloud APIs. Before you call an API as a RAM user, you must grant the RAM user the permissions to call the API. This requires you to create and attach a RAM policy to the RAM user.

When you create the RAM policy, you can specify the resource that the RAM user wants to access by its Alibaba Resource Name \(ARN\). An ARN is a globally unique name that is used to identify a cloud resource in Alibaba Cloud.

An ARN uses the following format:

```
acs:service-name:region:account-id:resource-relative-id
```

Where:

-   acs: the acronym of Alibaba Cloud Service.
-   service-name: the name of an Alibaba Cloud service, such as Elastic Compute Service \(ECS\), Object Storage Service \(OSS\), and Server Load Balancer \(SLB\).
-   region: the region where the cloud resource is deployed. If this parameter is not supported by the cloud resource, set the parameter to an asterisk \(\*\).

-   account-id: the ID of the account that owns the cloud resource, for example, 1234567890123456.

-   resource-relative-id: the description of the cloud resource. The description varies based on the Alibaba Cloud service. For more information, see the documentation of Alibaba Cloud services.

    For example, `acs:oss::1234567890123456:sample_bucket/file1.txt` indicates a resource named sample\_bucket/file1.txt in OSS. `1234567890123456` indicates the ID of the account that owns the cloud resource.


## Authorize a RAM user to manage ACK

|Authorization type|ARN format|
|------------------|----------|
|Grant permissions on one Container Service for Kubernetes \(ACK\) cluster|```
"Resource": [
     "acs:cs:*:*:cluster/Cluster ID"
 ]
``` |
|Grant permissions on multiple ACK clusters|```
"Resource": [
     "acs:cs:*:*:cluster/Cluster ID",
     "acs:cs:*:*:cluster/Cluster ID"
 ]
``` |
|Grant permissions on all ACK clusters|```
"Resource": [
     "*"
 ]
``` |

## Mappings between API operations and RAM actions

The following table describes the mappings between the ACK API operations and the RAM actions.

|API operation|RAM Action|Description|
|-------------|----------|-----------|
|AttachInstances|AttachInstances|Adds existing ECS instances to an ACK cluster.|
|CancelClusterUpgrade|CancelK8sCluster|Cancels the upgrade of an ACK cluster.|
|CancelComponentUpgrade|CancelComponentUpgrade|Cancels the upgrade of a component.|
|CreateCluster|CreateCluster|Creates an ACK cluster.|
|CreateTriggerHook|CreateTrigger|Creates and modifies an application trigger.|
|CreateTemplate|CreateTemplate|Creates an application deployment template.|
|DeleteCluster|DeleteCluster|Deletes an ACK cluster.|
|DeleteClusterNodes|DeleteClusterNodes|Removes nodes and releases the ECS instances.|
|DeleteTrigger|RevokeTriggers|Deletes an application trigger.|
|DeleteTemplate|V2DeleteTemplateInfo|Deletes an application deployment template.|
|DescribeAddons|Queryk8sComponentsVersion|Queries information about the components in an ACK cluster.|
|DescribeClusterAddonsVersion|Queryk8sComponentsUpdateVersion|Queries the versions of the components in an ACK cluster.|
|DescribeClusterAddonUpgradeStatus|QueryK8sComponentUpgradeStatus|Queries the upgrade status of a component in an ACK cluster.|
|DescribeClusterAttachScripts|GetClusterJoinScript|Queries the script that is used to manually add a node to an ACK cluster.|
|DescribeClusterDetail|GetClusterById|Queries the details of an ACK cluster.|
|DescribeClusterLogs|GetClusterLogs|Queries the log of an ACK cluster.|
|DescribeClusterNodes|DescribeClusterNodes|Queries the nodes in an ACK cluster.|
|DescribeClusterResources|DescribeClusterResources|Queries all resources in an ACK cluster.|
|DescribeClusters|GetClustersByUid and GetClusters|Queries all ACK clusters that belong to your account.|
|DescribeClustersV1|ListClusters|Queries all ACK clusters that belong to your account.|
|DescribeClusterUserKubeconfig|GetUserConfig|Queries the kubeconfig file of an ACK cluster.|
|DescribeClusterV2UserKubeconfig|GetUserConfig|Queries the kubeconfig file of an ACK cluster.|
|DescribeExternalAgent|DescribeExternalClusterAgentDeployment|Queries the configurations of an agent that is used to register an external Kubernetes cluster.|
|DescribeTemplates|V2ListTemplates|Queries application deployment templates.|
|DescribeUserQuota|GetUserQuota|Queries resource quotas of your account.|
|GetTrigger|GetK8sTrigger|Queries the details about application triggers.|
|GetUpgradeStatus|GetK8sClusterState|Queries the upgrade status of an ACK cluster.|
|InstallClusterAddons|InstallK8sComponents|Installs a component in an ACK cluster.|
|ModifyCluster|ModifyCluster|Modifies information about an ACK cluster.|
|ModifyClusterTags|UpdateClusterTags|Modifies the labels of an ACK cluster.|
|PauseClusterUpgrade|UpgradeCluster|Suspends the upgrade of an ACK cluster.|
|PauseComponentUpgrade|PauseComponentUpgrade|Suspends the upgrade of a component.|
|ReBindSecurityGroup|ReBindSecurityGroup|Rebinds an ACK cluster to another security group.|
|RemoveClusterNodes|DeleteClusterNode|Removes nodes from an ACK cluster.|
|ResumeComponentUpgrade|ResumeComponentUpgrade|Resumes the upgrade of a component.|
|ResumeUpgradeCluster|UpgradeCluster|Resumes the upgrade of an ACK cluster.|
|ScaleOutCluster|ScaleCluster|Scales out an ACK cluster.|
|UnInstallClusterAddons|UnInstallK8sComponents|Uninstalls components from an ACK cluster.|
|UpdateK8sClusterUserConfigExpire|UpdateK8sClusterUserConfigExpire|Updates the expiration time of custom configurations.|
|UpgradeCluster|UpgradeCluster|Upgrades an ACK cluster.|
|UpgradeClusterAddons|UpgradeK8sComponents|Upgrades a component of an ACK cluster.|
|DescribeUserPermission|GetUserPermissions|Queries the permissions that are granted to a RAM user to manage ACK clusters.|
|GrantPermissions|GrantPermission|Updates the permissions that are granted to a RAM user to manage ACK clusters.|
|CreateClusterNodePool|CreateNodepool|Creates a node pool.|
|DeleteClusterNodepool|DeleteNodepool|Deletes a node pool.|
|DescribeClusterNodePoolDetail|GetNodepoolDetail|Queries detailed information about a node pool.|
|DescribeClusterNodePools|GetNodepools|Queries the node pools in an ACK cluster.|
|ModifyClusterNodePool|UpdateNodepool|Modifies a node pool.|
|ScaleClusterNodePool|ScaleNodepool|Scales out a node pool.|
|MigrateCluster|MigrateCluster|Migrates an ACK cluster.|

