# RAM user authorization

Before you call an Alibaba Cloud API by using a RAM user, you must use an Alibaba Cloud account to create an authorization policy to grant permissions to the RAM user.

## Authorize a RAM user to use cloud resources

By default, a RAM user is not authorized to call Alibaba Cloud APIs to create or modify cloud resources. Before you use a RAM user to call an API, you must grant the RAM user the permission to call the API by creating an authorization policy and attaching the policy to the RAM user.

When you create the authorization policy, you can specify the resource by its Alibaba Resource Name \(ARN\). An ARN is a unique name that is used to identify a resource on Alibaba Cloud.

The following string shows the format of an ARN:

```
acs:service-name:region:account-id:resource-relative-id
```

<input tabindex="-1" class="dnt" readonly="readonly" value="Do Not Translate"\>

-   acs: the abbreviation for Alibaba Cloud Service.
-   service-name: the name of an Alibaba Cloud service, such as ecs, oss, and slb.
-   region: the region where the service resides. If this option is not supported, use the asterisk \(\*\) wildcard instead.

-   account-id: the ID of the user account, such as 1234567890123456.

-   resource-relative-id: the description of a resource. The description varies based on the service. For more information, see the documentation of each service.

    For example, `acs:oss:1234567890123456:sample_bucket/file1.txt` indicates a resource named sample\_bucket/file1.txt in Alibaba Could Object Storage Service \(OSS\). `1234567890123456` indicates the ID of the user that owns the resource.


## Resource types

|Resource type|ARN format|
|-------------|----------|
|Single cluster|```
"Resource": [
     "acs:cs:*:*:cluster/The ID of the cluster."
 ]
``` |
|Multiple clusters|```
"Resource": [
     "acs:cs:*:*:cluster/The ID of the cluster.",
     "acs:cs:*:*:cluster/The ID of the cluster."
 ]
``` |
|All clusters|```
"Resource": [
     "*"
 ]
``` |

## Mappings between API operations and actions that RAM users are authorized to perform

The following table describes the mappings between the operations in the Container Service for Kubernetes \(ACK\) API and actions that RAM users are authorized to perform.

|Operation|RAM Action|Description|
|---------|----------|-----------|
|AttachInstances|AttachInstances|Adds existing ECS instances to a cluster.|
|CancelClusterUpgrade|CancelK8sCluster|Cancels the upgrade of a cluster.|
|CancelComponentUpgrade|CancelComponentUpgrade|Cancels the upgrade of an add-on.|
|CreateCluster|CreateCluster|Creates a cluster.|
|CreateKubernetesTrigger|GenerateTriggerHook|Creates an application operation trigger.|
|CreateTemplate|CreateTemplate|Creates an application deployment template.|
|DeleteCluster|DeleteCluster|Deletes a cluster.|
|DeleteClusterNodes|DeleteClusterNodes|Removes a node and releases the ECS instance.|
|DeleteKubernetesTrigger|RevokeTriggers|Deletes an application operation trigger.|
|DeleteTemplate|V2DeleteTemplateInfo|Deletes an application deployment template.|
|DescribeAddons|Queryk8sComponentsVersion|Queries the information about add-ons in an ACK cluster.|
|DescribeApiVersion|GetCurrentVersions|Queries the API version.|
|DescribeClusterAddonsVersion|Queryk8sComponentsUpdateVersion|Queries the versions of cluster add-ons.|
|DescribeClusterAddonUpgradeStatus|QueryK8sComponentUpgradeStatus|Queries the upgrade status of an add-on in an ACK cluster.|
|DescribeClusterAttachScripts|GetClusterJoinScript|Obtains the script for manually adding nodes to the ACK cluster.|
|DescribeClusterDetail|GetClusterInfo|Queries the details of a cluster.|
|DescribeClusterLogs|GetClusterLogs|Queries the logs of a cluster.|
|DescribeClusterNodes|DescribeClusterNodes|Queries nodes in a cluster.|
|DescribeClusterResources|DescribeClusterResources|Queries all resources in a cluster.|
|DescribeClusters|GetClustersByUid and GetClusters|Queries all clusters under your account.|
|DescribeClustersV1|ListClusters|Queries all clusters under your account.|
|DescribeClusterUserKubeconfig|GetUserConfig|Obtains the kubeconfig file of an ACK cluster.|
|DescribeClusterV2UserKubeconfig|GetUserConfig|Obtains the kubeconfig file of an ACK cluster.|
|DescribeExternalAgent|DescribeExternalClusterAgentDeployment|Queries external agents of the Kubernetes API server.|
|DescribeTemplates|V2ListTemplates|Queries deployment templates for an application.|
|DescribeUserQuota|GetUserQuota|Queries resource quotas of your account.|
|GetKubernetesTrigger|GetK8sTrigger|Queries the details about an application operation trigger.|
|GetUpgradeStatus|GetK8sClusterState|Queries the upgrade status of a cluster.|
|InstallClusterAddons|InstallK8sComponents|Installs an add-on for a cluster.|
|ModifyCluster|ModifyCluster|Modifies the information about a cluster.|
|ModifyClusterTags|UpdateClusterTags|Modifies the tags of a cluster.|
|PauseClusterUpgrade|UpgradeCluster|Suspends the upgrade of a cluster.|
|PauseComponentUpgrade|PauseComponentUpgrade|Suspends the upgrade of an add-on.|
|ReBindSecurityGroup|ReBindSecurityGroup|Rebinds the cluster to the security group.|
|RemoveClusterNodes|DeleteClusterNodes|Removes nodes from a cluster.|
|ResumeComponentUpgrade|ResumeComponentUpgrade|Resumes the upgrade of an add-on.|
|ResumeUpgradeCluster|UpgradeCluster|Resumes the upgrade of a cluster.|
|ScaleCluster|ScaleCluster|Scales out a cluster.|
|ScaleOutCluster|V2ScaleCluster|Adds worker nodes to a cluster.|
|UnInstallClusterAddons|UnInstallK8sComponents|Uninstalls an add-on from a cluster.|
|UpdateK8sClusterUserConfigExpire|UpdateK8sClusterUserConfigExpire|Updates the expiration time of custom configurations.|
|UpgradeCluster|UpgradeCluster|Upgrades a cluster.|
|UpgradeClusterAddons|UpgradeK8sComponents|Upgrades the add-ons of a cluster.|

