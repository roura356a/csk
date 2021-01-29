# RAM authentication

Before you call an Alibaba Cloud API by using a Resource Access Management \(RAM\) user, you must use an Alibaba Cloud account to create a RAM policy with required permissions and attach it to the RAM user.

## Authorize a RAM user to use cloud resources

By default, a RAM user is not authorized to create or modify cloud resources by calling Alibaba Cloud APIs. Before you use a RAM user to call an API, you must grant the RAM user the permissions to call the API. This requires you to create and attach a RAM policy to the RAM user.

When you create the policy, you can specify a cloud resource by its Alibaba Resource Name \(ARN\). An ARN is a globally unique name that is used to identify a cloud resource on Alibaba Cloud.

An ARN uses the following format:

```
acs:service-name:region:account-id:resource-relative-id
```

An ARN contains the following parameters:

-   acs: the abbreviation of Alibaba Cloud Service.
-   service-name: the name of an Alibaba Cloud service. Examples: ecs, oss, and slb.
-   region: the region where the cloud resource is deployed. If this parameter is not supported by the cloud resource, you can replace this parameter with an asterisk \(\*\).

-   account-id: the ID of the account that owns the cloud resource. Example: 1234567890123456.

-   resource-relative-id: the description of the cloud resource. The description varies based on the Alibaba Cloud service. For more information, see the documentation of Alibaba Cloud services.

    For example, `acs:oss::1234567890123456:sample_bucket/file1.txt` indicates a cloud resource named sample\_bucket/file1.txt in Object Storage Service \(OSS\). `1234567890123456` indicates the ID of the account that owns the cloud resource.


## Authorization types on ACK

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
|AttachInstances|AttachInstances|Adds existing Elastic Compute Service \(ECS\) instances to an ACK cluster.|
|CancelClusterUpgrade|CancelK8sCluster|Cancels the upgrade of an ACK cluster.|
|CancelComponentUpgrade|CancelComponentUpgrade|Cancels the upgrade of a component.|
|CreateCluster|CreateCluster|Creates an ACK cluster.|
|CreateKubernetesTrigger|GenerateTriggerHook|Creates an application trigger.|
|CreateTemplate|CreateTemplate|Creates an application deployment template.|
|DeleteCluster|DeleteCluster|Deletes an ACK cluster.|
|DeleteClusterNodes|DeleteClusterNodes|Removes a cluster node and releases the ECS instance.|
|DeleteKubernetesTrigger|RevokeTriggers|Deletes an application trigger.|
|DeleteTemplate|V2DeleteTemplateInfo|Deletes an application deployment template.|
|DescribeAddons|Queryk8sComponentsVersion|Queries information about the components in an ACK cluster.|
|DescribeClusterAddonsVersion|Queryk8sComponentsUpdateVersion|Queries the versions of the components in an ACK cluster.|
|DescribeClusterAddonUpgradeStatus|QueryK8sComponentUpgradeStatus|Queries the upgrade status of a component in an ACK cluster.|
|DescribeClusterAttachScripts|GetClusterJoinScript|Queries the script that is used to manually add nodes to an ACK cluster.|
|DescribeClusterDetail|GetClusterById|Queries the details of an ACK cluster.|
|DescribeClusterLogs|GetClusterLogs|Queries the logs of an ACK cluster.|
|DescribeClusterNodes|DescribeClusterNodes|Queries the nodes in an ACK cluster.|
|DescribeClusterResources|DescribeClusterResources|Queries all resources in an ACK cluster.|
|DescribeClusters|GetClustersByUid and GetClusters|Queries all ACK clusters under your account.|
|DescribeClustersV1|ListClusters|Queries all ACK clusters under your account.|
|DescribeClusterUserKubeconfig|GetUserConfig|Queries the kubeconfig file of an ACK cluster.|
|DescribeClusterV2UserKubeconfig|GetUserConfig|Queries the kubeconfig file of an ACK cluster.|
|DescribeExternalAgent|DescribeExternalClusterAgentDeployment|Queries the configuration of an agent that is used to register an external Kubernetes cluster.|
|DescribeTemplates|V2ListTemplates|Queries application deployment templates.|
|DescribeUserQuota|GetUserQuota|Queries resource quotas of your account.|
|GetKubernetesTrigger|GetK8sTrigger|Queries the details about an application trigger.|
|GetUpgradeStatus|GetK8sClusterState|Queries the upgrade status of an ACK cluster.|
|InstallClusterAddons|InstallK8sComponents|Installs a component in an ACK cluster.|
|ModifyCluster|ModifyCluster|Modifies information about an ACK cluster.|
|ModifyClusterTags|UpdateClusterTags|Modifies the labels of an ACK cluster.|
|PauseClusterUpgrade|UpgradeCluster|Suspends the upgrade of an ACK cluster.|
|PauseComponentUpgrade|PauseComponentUpgrade|Suspends the upgrade of a component.|
|ReBindSecurityGroup|ReBindSecurityGroup|Rebinds an ACK cluster to a security group.|
|RemoveClusterNodes|DeleteClusterNode|Removes nodes from an ACK cluster.|
|ResumeComponentUpgrade|ResumeComponentUpgrade|Resumes the upgrade of a component.|
|ResumeUpgradeCluster|UpgradeCluster|Resumes the upgrade of an ACK cluster.|
|ScaleCluster|ScaleCluster|Scales out an ACK cluster.|
|ScaleOutCluster|ScaleCluster|Adds an existing node to an ACK cluster.|
|UnInstallClusterAddons|UnInstallK8sComponents|Uninstalls a component from an ACK cluster.|
|UpdateK8sClusterUserConfigExpire|UpdateK8sClusterUserConfigExpire|Updates the expiration time of custom configurations.|
|UpgradeCluster|UpgradeCluster|Upgrades an ACK cluster.|
|UpgradeClusterAddons|UpgradeK8sComponents|Upgrades the components of an ACK cluster.|

