# API overview

The following tables list API operations available for use in Container Service for Kubernetes \(ACK\).

**Note:** Before you call the API operations to manage ACK clusters, make sure that you have read and agreed to [Disclaimer]().

## Clusters

|API|Description|
|---|-----------|
|[Create a dedicated Kubernetes cluster](/intl.en-US/API Reference/Clusters/Create a cluster/Create a dedicated Kubernetes cluster.md)|Call the CreateCluster operation to create a dedicated Kubernetes cluster.|
|[CreateCluster](/intl.en-US/API Reference/Clusters/Create a cluster/CreateCluster.md)

|Call the CreateCluster operation to create a managed Kubernetes cluster and add a specified number of nodes to the cluster.|
|[Create an ASK cluster](/intl.en-US/API Reference/Clusters/Create a cluster/Create an ASK cluster.md)

|Call the CreateCluster operation to create a serverless Kubernetes \(ASK\) cluster.|
|[Create a managed edge Kubernetes cluster](/intl.en-US/API Reference/Clusters/Create a cluster/Create a managed edge Kubernetes cluster.md)

|Call the CreateCluster operation to create a managed edge Kubernetes cluster.|
|[Create a dedicated Kubernetes cluster that supports sandboxed containers](/intl.en-US/API Reference/Clusters/Create a cluster/Create a dedicated Kubernetes cluster that supports sandboxed containers.md)

|Call the CreateCluster operation to create a dedicated Kubernetes cluster that runs sandboxed containers.|
|[Create a standard managed Kubernetes cluster that supports sandboxed](/intl.en-US/API Reference/Clusters/Create a cluster/Create a standard managed Kubernetes cluster that supports sandboxed.md)|Call the CreateCluster operation to create a managed Kubernetes cluster that runs sandboxed containers.|
|[Scale out an ACK cluster \(will be deleted\)](/intl.en-US/API Reference/Clusters/Scale out an ACK cluster (will be deleted).md)

|Call the ScaleOutCluster operation to add worker nodes to a specified ACK cluster. The worker nodes can be deployed across zones.|
|[Scale out a managed edge Kubernetes cluster](/intl.en-US/API Reference/Clusters/Scale out a managed edge Kubernetes cluster.md)

|Call the ScaleOutCluster operation to add worker nodes to a specified ACK cluster. You can add only Edge Node Service \(ENS\) instances to a managed edge Kubernetes cluster.|
|[DescribeClusterDetail](/intl.en-US/API Reference/Clusters/DescribeClusterDetail.md)

|Call the DescribeClusterDetail operation to query the details of a specified ACK cluster by cluster ID.|
|[DescribeClusterNodes](/intl.en-US/API Reference/Clusters/DescribeClusterNodes.md)

|Call the DescribeClusterNodes operation to query the nodes in a specified ACK cluster.|
|[DescribeClustersV1](/intl.en-US/API Reference/Clusters/DescribeClustersV1.md)

|Call the DescribeClustersV1 operation to query all Swarm clusters and ACK clusters that are created by the current account.|
|[DescribeClusterResources](/intl.en-US/API Reference/Clusters/DescribeClusterResources.md)

|Call the DescribeClusterResources operation to query all resources in a specified ACK cluster.|
|[DescribeExternalAgent](/intl.en-US/API Reference/Clusters/DescribeExternalAgent.md)

|Call the DescribeExternalAgent operation to query the agent configurations of a specified ACK cluster.|
|[DescribeUserQuota](/intl.en-US/API Reference/Clusters/DescribeUserQuota.md)

|Call the DescribeUserQuota operation to query the resource quotas of the current account.|
|[DescribeClusterLogs](/intl.en-US/API Reference/Clusters/DescribeClusterLogs.md)

|Call the DescribeClusterLogs operation to query the logs of a specified ACK cluster.|
|[DescribeClusterUserKubeconfig](/intl.en-US/API Reference/Clusters/DescribeClusterUserKubeconfig.md)

|Call the DescribeClusterUserKubeconfig operation to obtain the kubeconfig file of a specified ACK cluster. The kubeconfig file stores details of the account that is used to log on to the ACK cluster.|
|[ModifyCluster](/intl.en-US/API Reference/Clusters/ModifyCluster.md)

|Call the ModifyCluster operation to modify the configurations of a specified ACK cluster.|
|[DeleteCluster](/intl.en-US/API Reference/Clusters/DeleteCluster.md)

|Call the DeleteCluster operation to delete an ACK cluster by cluster ID and release all nodes in the cluster.|
|[ModifyClusterTags](/intl.en-US/API Reference/Tag/ModifyClusterTags.md)

|Call the ModifyClusterTags operation to modify the labels of a specified ACK cluster.|
|[ListTagResources](/intl.en-US/API Reference/Tag/ListTagResources.md)

|Call the ListTagResources operation to query the labels that are added to the specified resources.|
|[DescribeKubernetesVersionMetadata](/intl.en-US/API Reference/Clusters/DescribeKubernetesVersionMetadata.md)|Call the DescribeKubernetesVersionMetadata operation to query the details of a specified Kubernetes version that is supported by ACK.|
|[DescribeTaskInfo](/intl.en-US/API Reference/Clusters/DescribeTaskInfo.md)|Call the DescribeTaskInfo operation to query the details of a task by task ID.|
|[DescribeEvents](/intl.en-US/API Reference/Clusters/DescribeEvents.md)|Call the DescribeEvents operation to query the operations performed by users.|
|[AttachInstances]()

|Call the AttachInstances operation to add existing ENS instances to a specified managed edge Kubernetes cluster.|

## Permissions

|API|Description|
|---|-----------|
|[Query the permissions of a RAM user](/intl.en-US/API Reference/Permissions/Query the permissions of a RAM user.md)

|Call the DescribeUserPermission operation to query the permissions that are granted to a specified Resource Access Management \(RAM\) user to manage ACK clusters.|
|[GrantPermissions](/intl.en-US/API Reference/Permissions/GrantPermissions.md)

|Call the GrantPermissions operation to grant a specified RAM user the permissions to manage ACK clusters.|

## Node pools

|API|Description|
|---|-----------|
|[t1956932.md\#](/intl.en-US/API Reference/Node pools/Create a node pool.md)

|Call the CreateClusterNodePool operation to create a node pool in a specified ACK cluster.|
|[ScaleClusterNodePool](/intl.en-US/API Reference/Node pools/ScaleClusterNodePool.md)

|Call the ScaleClusterNodePool operation to add nodes to a specified node pool.|
|[t1956078.md\#](/intl.en-US/API Reference/Node pools/Update node pool information.md)

|Call the ModifyClusterNodePool operation to update the configurations of a specified node pool.|
|[DescribeClusterNodePools](/intl.en-US/API Reference/Node pools/DescribeClusterNodePools.md)

|Call the DescribeClusterNodePools operation to query the details of all node pools in a specified ACK cluster.|
|[DescribeClusterNodePoolDetail](/intl.en-US/API Reference/Node pools/DescribeClusterNodePoolDetail.md)

|Call the DescribeClusterNodePoolDetail operation to query the details of a specified node pool in an ACK cluster.|
|[AttachInstances](/intl.en-US/API Reference/Node pools/AttachInstances.md)

|Call the AttachInstances operation to add existing Elastic Compute Service \(ECS\) instances to a specified ACK cluster.|
|[DescribeClusterAttachScripts](/intl.en-US/API Reference/Node pools/DescribeClusterAttachScripts.md)

|Call the DescribeClusterAttachScripts operation to add an existing node to a specified managed edge Kubernetes cluster. This operation returns a unique and executable script. You can run the script on an existing node to add the node to the managed edge Kubernetes cluster.|
|[Remove nodes from node pools](/intl.en-US/API Reference/Node pools/DeleteClusterNodes.md)

|Call the RemoveClusterNodes operation to remove the nodes from a specified ACK cluster.|
|[DeleteClusterNodepool](/intl.en-US/API Reference/Node pools/DeleteClusterNodepool.md)|Call the DeleteClusterNodepool operation to delete a node pool.|

## Upgrades

|API|Description|
|---|-----------|
|[GetUpgradeStatus](/intl.en-US/API Reference/Upgrades/Query the upgrade status of a cluster.md)

|Call the GetUpgradeStatus operation to query the upgrade status of a specified ACK cluster.|
|[UpgradeCluster](/intl.en-US/API Reference/Upgrades/UpgradeCluster.md)

|Call the UpgradeCluster operation to upgrade an ACK cluster.|
|[PauseClusterUpgrade](/intl.en-US/API Reference/Upgrades/Suspend the upgrade of a cluster.md)

|Call the PauseClusterUpgrade operation to pause the upgrade of a specified ACK cluster.|
|[CancelClusterUpgrade](/intl.en-US/API Reference/Upgrades/Cancel the upgrade of a cluster.md)

|Call the CancelClusterUpgrade operation to cancel the upgrade of a specified ACK cluster.|
|[ResumeUpgradeCluster](/intl.en-US/API Reference/Upgrades/Resume the upgrade of a cluster.md)

|Call the ResumeUpgradeCluster operation to resume the upgrade of a specified ACK cluster.|

## Applications

|API|Description|
|---|-----------|
|[CreateTemplate](/intl.en-US/API Reference/Applications/Create an application deployment template.md)|Call the CreateTemplate operation to create an application deployment template.|
|[DescribeTemplates](/intl.en-US/API Reference/Applications/Query application deployment templates.md)|Call the DescribeTemplates operation to query application deployment templates.|
|[DescribeTemplateAttribute](/intl.en-US/API Reference/Applications/DescribeTemplateAttribute.md)|Call the DescribeTemplateAttribute operation to query the details of a specified application deployment template.|
|[UpdateTemplate](/intl.en-US/API Reference/Applications/Update an application deployment template.md)

|Call the UpdateTemplate operation to update an application deployment template.|
|[DeleteTemplate](/intl.en-US/API Reference/Applications/Delete an application deployment template.md)

|Call the DeleteTemplate operation to delete an application deployment template.|
|[Create a trigger](/intl.en-US/API Reference/Applications/Create a trigger.md)|Call the CreateKubernetesTrigger operation to create an application trigger.|
|[GetKubernetesTrigger](/intl.en-US/API Reference/Applications/GetKubernetesTrigger.md)|Call the GetKubernetesTrigger operation to query the triggers that are created for a specified application.|
|[DeleteKubernetesTrigger](/intl.en-US/API Reference/Applications/DeleteKubernetesTrigger.md)|Call the DeleteKubernetesTrigger operation to delete an application trigger.|

## Components

|API|Description|
|---|-----------|
|[InstallClusterAddons](/intl.en-US/API Reference/Add-ons/InstallClusterAddons.md)

|Call the InstallClusterAddons operation to install components in a specified ACK cluster.|
|[DescribeClusterAddonsVersion](/intl.en-US/API Reference/Add-ons/DescribeClusterAddonsVersion.md)

|Call the DescribeClusterAddonsVersion operation to query the versions of components in a specified ACK cluster.|
|[DescribeAddons](/intl.en-US/API Reference/Add-ons/DescribeAddons.md)

|Call the DescribeAddons operation to query the details of components that are installed in ACK clusters.|
|[UpgradeClusterAddons](/intl.en-US/API Reference/Add-ons/UpgradeClusterAddons.md)|Call the UpgradeClusterAddons operation to upgrade a component to the specified version in a specified ACK cluster.|
|[DescribeClusterAddonsUpgradeStatus](/intl.en-US/API Reference/Add-ons/DescribeClusterAddonsUpgradeStatus.md)|Call the DescribeClusterAddonsUpgradeStatus operation to query the upgrade status of multiple components at a time.|
|[ModifyClusterConfiguration](/intl.en-US/API Reference/Add-ons/ModifyClusterConfiguration.md)|Call the ModifyClusterConfiguration operation to modify ConfigMaps for kube-apiserver and Kube Controller Manager \(KCM\). This allows you to modify the configurations of these components.|
|[UnInstallClusterAddons](/intl.en-US/API Reference/Add-ons/UnInstallClusterAddons.md)

|Call the UnInstallClusterAddons operation to uninstall components from a specified ACK cluster.|

## Labels

|API|Description|
|---|-----------|
|[TagResources](/intl.en-US/API Reference/Tag/TagResources.md)

|Call the TagResources operation to add labels to a specified ACK cluster.|
|[ModifyClusterTags](/intl.en-US/API Reference/Tag/ModifyClusterTags.md)

|Call the ModifyClusterTags operation to modify the labels of a specified ACK cluster.|
|[ListTagResources](/intl.en-US/API Reference/Tag/ListTagResources.md)

|Call the ListTagResources operation to query the labels of the specified resources.|
|[UntagResources](/intl.en-US/API Reference/Tag/UntagResources.md)

|Call the UntagResources operation to remove labels from a specified ACK cluster.|

