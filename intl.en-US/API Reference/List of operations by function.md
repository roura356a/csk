# List of operations by function

The following tables list the API operations available for use in Container Service for Kubernetes \(ACK\).

**Note:** Before you call the API operations to manage ACK clusters, make sure that you have read and agreed to [Disclaimer](/intl.en-US/Agreements/Disclaimer.md).

## Clusters

|API|Description|
|---|-----------|
|[Create a managed cluster](/intl.en-US/API Reference/Clusters/Create a cluster/Create a managed cluster.md)

|You can call CreateCluster to create a managed ACK cluster and add a specified number of nodes to the cluster.|
|[Create a serverless cluster](/intl.en-US/API Reference/Clusters/Create a cluster/Create a serverless cluster.md)

|You can call CreateCluster to create a serverless cluster.|
|[Create a managed edge cluster](/intl.en-US/API Reference/Clusters/Create a cluster/Create a managed edge cluster.md)

|You can call CreateCluster to create a managed edge cluster.|
|[Create an ACK cluster that supports sandboxed containers](/intl.en-US/API Reference/Clusters/Create a cluster/Create an ACK cluster that supports sandboxed containers.md)

|You can call CreateCluster to create an ACK cluster that supports sandboxed containers.|
|[Add worker nodes to a cluster](/intl.en-US/API Reference/Clusters/Expand a cluster/Add worker nodes to a cluster.md)

|You can call ScaleOutCluster to add worker nodes to an ACK cluster. Worker nodes can be deployed across zones.|
|[Add worker nodes to a cluster that supports sandboxed containers](/intl.en-US/API Reference/Clusters/Expand a cluster/Add worker nodes to a cluster that supports sandboxed containers.md)

|You can call ScaleOutCluster to add worker nodes to an ACK cluster that supports sandboxed containers. Worker nodes can be deployed across zones.|
|[Add worker nodes to a managed edge cluster](/intl.en-US/API Reference/Clusters/Expand a cluster/Add worker nodes to a managed edge cluster.md)

|You can call ScaleOutCluster to add worker nodes to a managed edge cluster. You can add only Edge Node Service \(ENS\) instances to a managed edge cluster.|
|[Query a cluster](/intl.en-US/API Reference/Clusters/Query a cluster.md)

|You can call DescribeClusterDetail to query the details of an ACK cluster by cluster ID.|
|[Query the information about all clusters](/intl.en-US/API Reference/Clusters/Query the information about all clusters.md)

|You can call DescribeClustersV1 to query all ACK clusters.|
|[Query resources in a cluster](/intl.en-US/API Reference/Clusters/Query resources in a cluster.md)

|You can call DescribeClusterResources to query all resources in an ACK cluster.|
|[Obtain an agent to access the API server of a cluster](/intl.en-US/API Reference/Clusters/Obtain an agent to access the API server of a cluster.md)

|You can call DescribeExternalAgent to obtain an agent to access the API server of a cluster.|
|[Query resource quotas](/intl.en-US/API Reference/Clusters/Query resource quotas.md)

|You can call DescribeUserQuota to query resource quotas of your account.|
|[Query the logs of a cluster](/intl.en-US/API Reference/Clusters/Query the logs of a cluster.md)

|You can call DescribeClusterLogs to query logs of an ACK cluster.|
|[Obtain a kubeconfig file of a cluster](/intl.en-US/API Reference/Clusters/Obtain a kubeconfig file of a cluster.md)

|You can call DescribeClusterUserKubeconfig to obtain the kubeconfig file of an ACK cluster. The kubeconfig file is used to configure access to an ACK cluster.|
|[Modify a cluster](/intl.en-US/API Reference/Clusters/Modify a cluster.md)

|You can call ModifyCluster to modify the configurations of an ACK cluster.|
|[Delete a cluster](/intl.en-US/API Reference/Clusters/Delete a cluster.md)

|You can call DeleteCluster to delete an ACK cluster by cluster ID and release all nodes in the cluster.|
|[Modify the tags of a cluster](/intl.en-US/API Reference/Tag/Modify the tags of a cluster.md)

|You can call ModifyClusterTags to modify tags of an ACK cluster.|
|[ListTagResources](/intl.en-US/API Reference/Tag/ListTagResources.md)

|You can call ListTagResources to query tags that are attached to one or more resources.|
|[Query the details of a cluster version](/intl.en-US/API Reference/Clusters/Query the details of a cluster version.md)|You can call DescribeKubernetesVersionMetadata to query the details of a cluster version.|

## Nodes

|API|Description|
|---|-----------|
|[Query nodes in a cluster](/intl.en-US/API Reference/Nodes/Query nodes in a cluster.md)

|You can call DescribeClusterNodes to query nodes in an ACK cluster.|
|[Add existing ECS instances to a cluster](/intl.en-US/API Reference/Nodes/Add existing ECS instances to a cluster.md)

|You can call AttachInstances to add an existing Elastic Compute Service \(ECS\) instance to an ACK cluster.|
|[Generate a script to add a node to a managed edge cluster](/intl.en-US/API Reference/Nodes/Generate a script to add a node to a managed edge cluster.md)

|You can call DescribeClusterAttachScripts to add an existing node to a managed edge cluster. This operation returns a unique and executable script. You can run the script on an existing node to add the node to a managed edge cluster.|
|[Add existing ENS instances to a managed edge cluster]()

|You can call AttachInstances to add an existing ENS instance to a managed edge cluster.|

## Node pools

|API|Description|
|---|-----------|
|[Create a node pool](/intl.en-US/API Reference/Node pools/Create a node pool.md)

|You can call CreateClusterNodePool to create a node pool for a cluster.|
|[ScaleClusterNodePool](/intl.en-US/API Reference/Node pools/ScaleClusterNodePool.md)

|You can call ScaleClusterNodePool to add nodes to a node pool.|
|[Update node pool information](/intl.en-US/API Reference/Node pools/Update node pool information.md)

|You can call ModifyClusterNodePool to update the configuration of a node pool.|
|[Query node pools](/intl.en-US/API Reference/Node pools/Query node pools.md)

|You can call DescribeClusterNodePools to query all node pools in an ACK cluster.|
|[Query the details of a node pool](/intl.en-US/API Reference/Node pools/Query the details of a node pool.md)

|You can call DescribeClusterNodePoolDetail to query the details of a node pool in an ACK cluster.|
|[Delete a node pool](/intl.en-US/API Reference/Node pools/Delete a node pool.md)|You can call DeleteClusterNodepool to delete a node pool.|

## Upgrades

|API|Description|
|---|-----------|
|[Query the upgrade status of a cluster](/intl.en-US/API Reference/Upgrade/Query the upgrade status of a cluster.md)

|You can call GetUpgradeStatus to query the upgrade status of an ACK cluster.|
|[Upgrade a cluster](/intl.en-US/API Reference/Upgrade/Upgrade a cluster.md)

|You can call UpgradeCluster to upgrade an ACK cluster.|
|[Suspend the upgrade of a cluster](/intl.en-US/API Reference/Upgrade/Suspend the upgrade of a cluster.md)

|You can call PauseClusterUpgrade to pause the upgrade of an ACK cluster.|
|[Cancel the upgrade of a cluster](/intl.en-US/API Reference/Upgrade/Cancel the upgrade of a cluster.md)

|You can call CancelClusterUpgrade to cancel the upgrade of an ACK cluster.|
|[Resume the upgrade of a cluster](/intl.en-US/API Reference/Upgrade/Resume the upgrade of a cluster.md)

|You can call ResumeUpgradeCluster to resume the upgrade of an ACK cluster.|

## Applications

|API|Description|
|---|-----------|
|[Create an application deployment template](/intl.en-US/API Reference/Applications/Create an application deployment template.md)

|You can call CreateTemplate to create an application deployment template.|
|[Query application deployment templates](/intl.en-US/API Reference/Applications/Query application deployment templates.md)

|You can call DescribeTemplates to query application deployment templates.|
|[Update an application deployment template](/intl.en-US/API Reference/Applications/Update an application deployment template.md)

|You can call UpdateTemplate to update an application deployment template.|
|[Delete an application deployment template](/intl.en-US/API Reference/Applications/Delete an application deployment template.md)

|You can call DeleteTemplate to delete an application deployment template.|
|[Create a trigger](/intl.en-US/API Reference/Applications/Create a trigger.md)|You can call CreateKubernetesTrigger to create an application operation trigger.|
|[Query triggers](/intl.en-US/API Reference/Applications/Query triggers.md)|You can call GetKubernetesTrigger to query triggers for a specific application.|
|[Delete a trigger](/intl.en-US/API Reference/Applications/Delete a trigger.md)|You can call DeleteKubernetesTrigger to delete an application operation trigger.|
|[Query the details of an orchestration template](/intl.en-US/API Reference/Applications/Query the details of an orchestration template.md)|You can call DescribeTemplateAttribute to query the details of a template.|

## Add-ons

|API|Description|
|---|-----------|
|[Install add-ons for a cluster](/intl.en-US/API Reference/Add-ons/Install add-ons for a cluster.md)

|You can call InstallClusterAddons to install add-ons for an ACK cluster.|
|[Query details of cluster add-ons]()

|You can call DescribeClusterAddonsVersion to query add-on versions in an ACK cluster.|
|[Query supported add-ons](/intl.en-US/API Reference/Add-ons/Query supported add-ons.md)

|You can call DescribeAddons to query information about add-ons in an ACK cluster.|
|[Query the upgrade statuses of cluster add-ons](/intl.en-US/API Reference/Add-ons/Query the upgrade statuses of cluster add-ons.md)|You can call DescribeClusterAddonsUpgradeStatus to query the upgrade statuses of multiple add-ons.|
|[Modify an add-on in a managed cluster](/intl.en-US/API Reference/Add-ons/Modify an add-on in a managed cluster.md)|You can call ModifyClusterConfiguration to modify ConfigMap data for a managed cluster. This allows you to modify the configuration of a cluster add-on.|
|[UnInstallClusterAddons](/intl.en-US/API Reference/Add-ons/UnInstallClusterAddons.md)

|You can call UnInstallClusterAddons to uninstall an add-on from an ACK cluster.|

