# RAM authentication

Before you call an Alibaba Cloud API by using a Resource Access Management \(RAM\) user, you must use the Alibaba Cloud account to create a RAM policy with the required permissions and attach it to the RAM user.

## Authorize a RAM user to use cloud resources

By default, a RAM user is not authorized to create or modify cloud resources by calling Alibaba Cloud APIs. Before you use a RAM user to call an API, you must grant the RAM user the permissions to call the API. This requires you to create and attach a RAM policy to the RAM user.

When you create the policy, you can specify a cloud resource by its Alibaba Resource Name \(ARN\). An ARN is a globally unique name that is used to identify a cloud resource in Alibaba Cloud.

An ARN uses the following format:

```
acs:service-name:region:account-id:resource-relative-id
```

An ARN contains the following parameters:

-   acs: the abbreviation of Alibaba Cloud Service.
-   service-name: the name of an Alibaba Cloud service. Examples: ecs, oss, and slb.
-   region: the region where the cloud resource is deployed. If this parameter is not supported by the cloud resource, replace this parameter with an asterisk \(\*\).

-   account-id: the ID of the account that owns the cloud resource. Example: 1234567890123456.

-   resource-relative-id: the description of the cloud resource. The description varies based on the Alibaba Cloud service. For more information, see the documentation of Alibaba Cloud services.

    For example, `acs:oss::1234567890123456:sample_bucket/file1.txt` indicates a resource named sample\_bucket/file1.txt in Object Storage Service \(OSS\). `1234567890123456` indicates the ID of the account that owns the cloud resource.


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
|AttachInstances|AttachInstances|Adds existing Elastic Compute Service \(ECS\) instances to a specified ACK cluster.|
|CancelClusterUpgrade|CancelK8sCluster|Cancels the upgrade of a specified ACK cluster.|
|CancelComponentUpgrade|CancelComponentUpgrade|Cancels the upgrade of a specified component.|
|CreateCluster|CreateCluster|Creates an ACK cluster.|
|CreateKubernetesTrigger|GenerateTriggerHook|Creates an application trigger.|
|CreateTemplate|CreateTemplate|Creates an application deployment template.|
|DeleteCluster|DeleteCluster|Deletes an ACK cluster.|
|DeleteClusterNodes|DeleteClusterNodes|Removes a node and releases the ECS instance.|
|DeleteKubernetesTrigger|RevokeTriggers|Deletes an application trigger.|
|DeleteTemplate|V2DeleteTemplateInfo|Deletes an application deployment template.|
|DescribeAddons|Queryk8sComponentsVersion|Queries information about the components in a specified ACK cluster.|
|DescribeClusterAddonsVersion|Queryk8sComponentsUpdateVersion|Queries the versions of the components in a specified ACK cluster.|
|DescribeClusterAddonUpgradeStatus|QueryK8sComponentUpgradeStatus|Queries the upgrade status of a specified component in an ACK cluster.|
|DescribeClusterAttachScripts|GetClusterJoinScript|Queries the script that is used to manually add nodes to a specified ACK cluster.|
|DescribeClusterDetail|GetClusterById|Queries the details of a specified ACK cluster.|
|DescribeClusterLogs|GetClusterLogs|Queries the logs of a specified ACK cluster.|
|DescribeClusterNodes|DescribeClusterNodes|Queries the nodes in a specified ACK cluster.|
|DescribeClusterResources|DescribeClusterResources|Queries all resources in a specified ACK cluster.|
|DescribeClusters|GetClustersByUid and GetClusters|Queries all ACK clusters that belong to your account.|
|DescribeClustersV1|ListClusters|Queries all ACK clusters that belong to your account.|
|DescribeClusterUserKubeconfig|GetUserConfig|Queries the kubeconfig file of a specified ACK cluster.|
|DescribeClusterV2UserKubeconfig|GetUserConfig|Queries the kubeconfig file of a specified ACK cluster.|
|DescribeExternalAgent|DescribeExternalClusterAgentDeployment|Queries the configurations of an agent that is used to register an external Kubernetes cluster.|
|DescribeTemplates|V2ListTemplates|Queries application deployment templates.|
|DescribeUserQuota|GetUserQuota|Queries resource quotas of your account.|
|GetKubernetesTrigger|GetK8sTrigger|Queries the details about application triggers.|
|GetUpgradeStatus|GetK8sClusterState|Queries the upgrade status of a specified ACK cluster.|
|InstallClusterAddons|InstallK8sComponents|Installs a specified component in an ACK cluster.|
|ModifyCluster|ModifyCluster|Modifies information about a specified ACK cluster.|
|ModifyClusterTags|UpdateClusterTags|Modifies the labels of a specified ACK cluster.|
|PauseClusterUpgrade|UpgradeCluster|Suspends the upgrade of a specified ACK cluster.|
|PauseComponentUpgrade|PauseComponentUpgrade|Suspends the upgrade of a specified component.|
|ReBindSecurityGroup|ReBindSecurityGroup|Rebinds an ACK cluster to another security group.|
|RemoveClusterNodes|DeleteClusterNode|Removes nodes from a specified ACK cluster.|
|ResumeComponentUpgrade|ResumeComponentUpgrade|Resumes the upgrade of a specified component.|
|ResumeUpgradeCluster|UpgradeCluster|Resumes the upgrade of a specified ACK cluster.|
|ScaleCluster|ScaleCluster|Scales out an ACK cluster.|
|ScaleOutCluster|ScaleCluster|Adds existing nodes to a specified ACK cluster.|
|UnInstallClusterAddons|UnInstallK8sComponents|Uninstalls components from a specified ACK cluster.|
|UpdateK8sClusterUserConfigExpire|UpdateK8sClusterUserConfigExpire|Updates the expiration time of custom configurations.|
|UpgradeCluster|UpgradeCluster|Upgrades an ACK cluster.|
|UpgradeClusterAddons|UpgradeK8sComponents|Upgrades the components of a specified ACK cluster.|
|DescribeUserPermission|GetUserPermissions|Queries the permissions that are granted to a RAM user to manage ACK clusters.|
|GrantPermissions|GrantPermission|Updates the permissions of a specified RAM user to manage ACK clusters.|

