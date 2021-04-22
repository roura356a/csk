# API overview

The following tables list API operations available for use in Container Service for Kubernetes \(ACK\).

**Note:** Before you call the API operations to manage ACK clusters, make sure that you have read and agreed to [Disclaimer](/intl.en-US/Agreements/Disclaimer.md).

## Clusters

|API|Description|
|---|-----------|
|[CreateCluster](/intl.en-US/API Reference/Clusters/Create a cluster/CreateCluster.md)|Creates a dedicated Kubernetes cluster.|
|[CreateCluster](/intl.en-US/API Reference/Clusters/Create a cluster/CreateCluster.md)

|Creates a managed Kubernetes cluster and adds a specified number of nodes to the cluster.|
|[Create a serverless cluster](/intl.en-US/API Reference/Clusters/Create a cluster/Create a serverless cluster.md)

|Creates a serverless Kubernetes \(ASK\) cluster.|
|[Create a managed edge cluster](/intl.en-US/API Reference/Clusters/Create a cluster/Create a managed edge cluster.md)

|Creates a managed edge Kubernetes cluster.|
|[Create an ACK cluster that supports sandboxed containers](/intl.en-US/API Reference/Clusters/Create a cluster/Create an ACK cluster that supports sandboxed containers.md)

|Creates a dedicated Kubernetes cluster that runs sandboxed containers.|
|[Create a managed ACK cluster that supports sandboxed containers]()|Creates a managed Kubernetes cluster that runs sandboxed containers.|
|[Add worker nodes to a cluster](/intl.en-US/API Reference/Clusters/Expand a cluster/Add worker nodes to a cluster.md)

|Adds worker nodes to a specified ACK cluster. The worker nodes can be deployed across zones.|
|[Add worker nodes to a cluster that supports sandboxed containers](/intl.en-US/API Reference/Clusters/Expand a cluster/Add worker nodes to a cluster that supports sandboxed containers.md)

|Adds worker nodes to a specified ACK cluster that runs sandboxed containers. The worker nodes can be deployed across zones.|
|[Add worker nodes to a managed edge cluster](/intl.en-US/API Reference/Clusters/Expand a cluster/Add worker nodes to a managed edge cluster.md)

|Adds worker nodes to a specified ACK cluster. You can add only Edge Node Service \(ENS\) instances to a managed edge Kubernetes cluster.|
|[DescribeClusterDetail](/intl.en-US/API Reference/Clusters/DescribeClusterDetail.md)

|Queries the details of a specified ACK cluster by cluster ID.|
|[DescribeClustersV1](/intl.en-US/API Reference/Clusters/DescribeClustersV1.md)

|Queries all Swarm clusters and ACK clusters that are created by the current account.|
|[DescribeClusterResources](/intl.en-US/API Reference/Clusters/DescribeClusterResources.md)

|Queries all resources in a specified ACK cluster.|
|[DescribeExternalAgent](/intl.en-US/API Reference/Clusters/DescribeExternalAgent.md)

|Queries the agent configurations of a specified ACK cluster.|
|[DescribeUserQuota](/intl.en-US/API Reference/Clusters/DescribeUserQuota.md)

|Queries the resource quotas of the current account.|
|[DescribeClusterLogs](/intl.en-US/API Reference/Clusters/DescribeClusterLogs.md)

|Queries the logs of a specified ACK cluster.|
|[DescribeClusterUserKubeconfig](/intl.en-US/API Reference/Clusters/DescribeClusterUserKubeconfig.md)

|Obtains the kubeconfig file of a specified ACK cluster. The kubeconfig file is used to configure access to an ACK cluster.|
|[ModifyCluster](/intl.en-US/API Reference/Clusters/ModifyCluster.md)

|Modifies the configurations of a specified ACK cluster.|
|[DeleteCluster](/intl.en-US/API Reference/Clusters/DeleteCluster.md)

|Deletes an ACK cluster by cluster ID and releases all nodes in the cluster.|
|[ModifyClusterTags](/intl.en-US/API Reference/Tag/ModifyClusterTags.md)

|Modifies the labels of a specified ACK cluster.|
|[ListTagResources](/intl.en-US/API Reference/Tag/ListTagResources.md)

|Queries the labels that are added to the specified resources.|
|[DescribeKubernetesVersionMetadata](/intl.en-US/API Reference/Clusters/DescribeKubernetesVersionMetadata.md)|Queries the details of a specified Kubernetes version that is supported by ACK.|
|[DescribeTaskInfo](/intl.en-US/API Reference/Clusters/DescribeTaskInfo.md)|Queries the details of a task by task ID.|

## Permissions

|API|Description|
|---|-----------|
|[DescribeUserPermission](/intl.en-US/API Reference/Permissions/DescribeUserPermission.md)

|Queries the permissions that are granted to a specified Resource Access Management \(RAM\) user to manage ACK clusters.|
|[GrantPermissions](/intl.en-US/API Reference/Permissions/GrantPermissions.md)

|Grants a specified RAM user the permissions to manage ACK clusters.|

## Nodes

|API|Description|
|---|-----------|
|[DescribeClusterNodes](/intl.en-US/API Reference/Nodes/DescribeClusterNodes.md)

|Queries the nodes in a specified ACK cluster.|
|[DeleteClusterNodes](/intl.en-US/API Reference/Nodes/DeleteClusterNodes.md)

|Removes the nodes from a specified ACK cluster.|
|[AttachInstances](/intl.en-US/API Reference/Nodes/AttachInstances.md)

|Adds existing Elastic Compute Service \(ECS\) instances to a specified ACK cluster.|
|[DescribeClusterAttachScripts](/intl.en-US/API Reference/Nodes/DescribeClusterAttachScripts.md)

|Adds an existing node to a specified managed edge Kubernetes cluster. This operation returns a unique and executable script. You can run the script on an existing node to add the node to the managed edge Kubernetes cluster.|
|[AttachInstances]()

|Adds existing ENS instances to a specified managed edge Kubernetes cluster.|

## Node pools

|API|Description|
|---|-----------|
|[t1956932.md\#](/intl.en-US/API Reference/Node pools/Create a node pool.md)

|Creates a node pool in a specified ACK cluster.|
|[ScaleClusterNodePool](/intl.en-US/API Reference/Node pools/ScaleClusterNodePool.md)

|Adds nodes to a specified node pool.|
|[t1956078.md\#](/intl.en-US/API Reference/Node pools/Update node pool information.md)

|Updates the configurations of a specified node pool.|
|[t1957053.md\#](/intl.en-US/API Reference/Node pools/Query node pools.md)

|Queries the details of all node pools in a specified ACK cluster.|
|[t1956076.md\#](/intl.en-US/API Reference/Node pools/Query the details of a node pool.md)

|Queries the details of a specified node pool in an ACK cluster.|
|[DeleteClusterNodepool](/intl.en-US/API Reference/Node pools/DeleteClusterNodepool.md)|Deletes a node pool.|

## Upgrades

|API|Description|
|---|-----------|
|[GetUpgradeStatus](/intl.en-US/API Reference/Upgrades/Query the upgrade status of a cluster.md)

|Queries the upgrade status of a specified ACK cluster.|
|[UpgradeCluster](/intl.en-US/API Reference/Upgrades/Upgrade a cluster.md)

|Upgrades an ACK cluster.|
|[PauseClusterUpgrade](/intl.en-US/API Reference/Upgrades/Suspend the upgrade of a cluster.md)

|Pauses the upgrade of a specified ACK cluster.|
|[CancelClusterUpgrade](/intl.en-US/API Reference/Upgrades/Cancel the upgrade of a cluster.md)

|Cancels the upgrade of a specified ACK cluster.|
|[ResumeUpgradeCluster](/intl.en-US/API Reference/Upgrades/Resume the upgrade of a cluster.md)

|Resumes the upgrade of a specified ACK cluster.|

## Applications

|API|Description|
|---|-----------|
|[CreateTemplate](/intl.en-US/API Reference/Applications/Create an application deployment template.md)

|Creates an application deployment template.|
|[DescribeTemplates](/intl.en-US/API Reference/Applications/Query application deployment templates.md)

|Queries application deployment templates.|
|[UpdateTemplate](/intl.en-US/API Reference/Applications/Update an application deployment template.md)

|Updates an application deployment template.|
|[DeleteTemplate](/intl.en-US/API Reference/Applications/Delete an application deployment template.md)

|Deletes an application deployment template.|
|[CreateKubernetesTrigger](/intl.en-US/API Reference/Applications/Create a trigger.md)|Creates an application trigger.|
|[GetKubernetesTrigger](/intl.en-US/API Reference/Applications/Query triggers.md)|Queries specific triggers that are created for a specified application.|
|[DeleteKubernetesTrigger](/intl.en-US/API Reference/Applications/Delete a trigger.md)|Deletes an application trigger.|
|[DescribeTemplateAttribute](/intl.en-US/API Reference/Applications/Query the details of an orchestration template.md)|Queries the details of a specified application deployment template.|

## Components

|API|Description|
|---|-----------|
|[InstallClusterAddons](/intl.en-US/API Reference/Add-ons/InstallClusterAddons.md)

|Install components in a specified ACK cluster.|
|[DescribeClusterAddonsVersion](/intl.en-US/API Reference/Add-ons/DescribeClusterAddonsVersion.md)

|Queries the versions of components in a specified ACK cluster.|
|[DescribeAddons](/intl.en-US/API Reference/Add-ons/DescribeAddons.md)

|Query the details of components that are installed in ACK clusters.|
|[UpgradeClusterAddons](/intl.en-US/API Reference/Add-ons/UpgradeClusterAddons.md)|Upgrades a component to the specified version in a specified ACK cluster.|
|[DescribeClusterAddonsUpgradeStatus](/intl.en-US/API Reference/Add-ons/DescribeClusterAddonsUpgradeStatus.md)|Queries the upgrade status of multiple components at a time.|
|[ModifyClusterConfiguration](/intl.en-US/API Reference/Add-ons/ModifyClusterConfiguration.md)|Modifies ConfigMaps for kube-apiserver and Kube Controller Manager \(KCM\). This allows you to modify the configurations of these components.|
|[UnInstallClusterAddons](/intl.en-US/API Reference/Add-ons/UnInstallClusterAddons.md)

|Uninstalls components from a specified ACK cluster.|

## Labels

|API|Description|
|---|-----------|
|[TagResources](/intl.en-US/API Reference/Tag/TagResources.md)

|Adds labels to a specified ACK cluster.|
|[ModifyClusterTags](/intl.en-US/API Reference/Tag/ModifyClusterTags.md)

|Modifies the labels of a specified ACK cluster.|
|[ListTagResources](/intl.en-US/API Reference/Tag/ListTagResources.md)

|Queries the labels of the specified resources.|
|[UntagResources](/intl.en-US/API Reference/Tag/UntagResources.md)

|Removes labels from a specified ACK cluster.|

