# API overview

The following tables list API operations available for use in Container Service for Kubernetes \(ACK\).

**Note:** Before you call the API operations to manage ACK clusters, make sure that you have read and agreed to [Disclaimer](/intl.en-US/Agreements/Disclaimer.md).

## Clusters

|API|Description|
|---|-----------|
|[CreateCluster](/intl.en-US/API Reference/Clusters/Create a cluster/CreateCluster.md)|Creates a dedicated Kubernetes cluster.|
|[CreateCluster](/intl.en-US/API Reference/Clusters/Create a cluster/Create a managed cluster.md)

|Creates a managed Kubernetes cluster and adds a specified number of nodes to the cluster.|
|[Create a serverless cluster](/intl.en-US/API Reference/Clusters/Create a cluster/Create a serverless cluster.md)

|Creates a serverless Kubernetes \(ASK\) cluster.|
|[Create a managed edge cluster](/intl.en-US/API Reference/Clusters/Create a cluster/Create a managed edge cluster.md)

|Creates a managed edge Kubernetes cluster.|
|[t1830556.md\#](/intl.en-US/API Reference/Clusters/Create a cluster/Create an ACK cluster that supports sandboxed containers.md)

|Creates a dedicated Kubernetes cluster that runs sandboxed containers.|
|[t2012386.md\#]()|Creates a managed Kubernetes cluster that runs sandboxed containers.|
|[Add worker nodes to a cluster](/intl.en-US/API Reference/Clusters/Expand a cluster/Add worker nodes to a cluster.md)

|Adds worker nodes to a specified ACK cluster. The worker nodes can be deployed across zones.|
|[Add worker nodes to a cluster that supports sandboxed containers](/intl.en-US/API Reference/Clusters/Expand a cluster/Add worker nodes to a cluster that supports sandboxed containers.md)

|Adds worker nodes to a specified ACK cluster that runs sandboxed containers. The worker nodes can be deployed across zones.|
|[Add worker nodes to a managed edge cluster](/intl.en-US/API Reference/Clusters/Expand a cluster/Add worker nodes to a managed edge cluster.md)

|Adds worker nodes to a specified ACK cluster. You can add only Edge Node Service \(ENS\) instances to a managed edge Kubernetes cluster.|
|[Query a cluster](/intl.en-US/API Reference/Clusters/Query a cluster.md)

|Queries the details of a specified ACK cluster by cluster ID.|
|[Query the information about all clusters](/intl.en-US/API Reference/Clusters/Query the information about all clusters.md)

|Queries all Swarm clusters and ACK clusters that are created by the current account.|
|[Query resources in a cluster](/intl.en-US/API Reference/Clusters/Query resources in a cluster.md)

|Queries all resources in a specified ACK cluster.|
|[Obtain an agent to access the API server of a cluster](/intl.en-US/API Reference/Clusters/Obtain an agent to access the API server of a cluster.md)

|Queries the agent configurations of a specified ACK cluster.|
|[Query resource quotas](/intl.en-US/API Reference/Clusters/Query resource quotas.md)

|Queries the resource quotas of the current account.|
|[Query the logs of a cluster](/intl.en-US/API Reference/Clusters/Query the logs of a cluster.md)

|Queries the logs of a specified ACK cluster.|
|[Obtain a kubeconfig file of a cluster](/intl.en-US/API Reference/Clusters/Obtain a kubeconfig file of a cluster.md)

|Obtains the kubeconfig file of a specified ACK cluster. The kubeconfig file is used to configure access to an ACK cluster.|
|[Modify a cluster](/intl.en-US/API Reference/Clusters/Modify a cluster.md)

|Modifies the configurations of a specified ACK cluster.|
|[Delete a cluster](/intl.en-US/API Reference/Clusters/Delete a cluster.md)

|Deletes an ACK cluster by cluster ID and releases all nodes in the cluster.|
|[Modify the tags of a cluster](/intl.en-US/API Reference/Tag/Modify the tags of a cluster.md)

|Modifies the labels of a specified ACK cluster.|
|[ListTagResources](/intl.en-US/API Reference/Tag/ListTagResources.md)

|Queries the labels that are added to the specified resources.|
|[Query the details of a cluster version](/intl.en-US/API Reference/Clusters/Query the details of a cluster version.md)|Queries the details of a specified Kubernetes version that is supported by ACK.|
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
|[Query nodes in a cluster](/intl.en-US/API Reference/Nodes/Query nodes in a cluster.md)

|Queries the nodes in a specified ACK cluster.|
|[Remove worker nodes from a cluster](/intl.en-US/API Reference/Nodes/Remove worker nodes from a cluster.md)

|Removes the nodes from a specified ACK cluster.|
|[Add existing ECS instances to a cluster](/intl.en-US/API Reference/Nodes/Add existing ECS instances to a cluster.md)

|Adds existing Elastic Compute Service \(ECS\) instances to a specified ACK cluster.|
|[Generate a script to add a node to a managed edge cluster](/intl.en-US/API Reference/Nodes/Generate a script to add a node to a managed edge cluster.md)

|Adds an existing node to a specified managed edge Kubernetes cluster. This operation returns a unique and executable script. You can run the script on an existing node to add the node to the managed edge Kubernetes cluster.|
|[Add existing ENS instances to a managed edge cluster]()

|Adds existing ENS instances to a specified managed edge Kubernetes cluster.|

## Node pools

|API|Description|
|---|-----------|
|[Create a node pool](/intl.en-US/API Reference/Node pools/Create a node pool.md)

|Creates a node pool in a specified ACK cluster.|
|[ScaleClusterNodePool](/intl.en-US/API Reference/Node pools/ScaleClusterNodePool.md)

|Adds nodes to a specified node pool.|
|[Update node pool information](/intl.en-US/API Reference/Node pools/Update node pool information.md)

|Updates the configurations of a specified node pool.|
|[Query node pools](/intl.en-US/API Reference/Node pools/Query node pools.md)

|Queries the details of all node pools in a specified ACK cluster.|
|[Query the details of a node pool](/intl.en-US/API Reference/Node pools/Query the details of a node pool.md)

|Queries the details of a specified node pool in an ACK cluster.|
|[Delete a node pool](/intl.en-US/API Reference/Node pools/Delete a node pool.md)|Deletes a node pool.|

## Upgrades

|API|Description|
|---|-----------|
|[Query the upgrade status of a cluster](/intl.en-US/API Reference/Upgrade/Query the upgrade status of a cluster.md)

|Queries the upgrade status of a specified ACK cluster.|
|[Upgrade a cluster](/intl.en-US/API Reference/Upgrade/Upgrade a cluster.md)

|Upgrades an ACK cluster.|
|[Suspend the upgrade of a cluster](/intl.en-US/API Reference/Upgrade/Suspend the upgrade of a cluster.md)

|Pauses the upgrade of a specified ACK cluster.|
|[Cancel the upgrade of a cluster](/intl.en-US/API Reference/Upgrade/Cancel the upgrade of a cluster.md)

|Cancels the upgrade of a specified ACK cluster.|
|[Resume the upgrade of a cluster](/intl.en-US/API Reference/Upgrade/Resume the upgrade of a cluster.md)

|Resumes the upgrade of a specified ACK cluster.|

## Applications

|API|Description|
|---|-----------|
|[Create an application deployment template](/intl.en-US/API Reference/Applications/Create an application deployment template.md)

|Creates an application deployment template.|
|[Query application deployment templates](/intl.en-US/API Reference/Applications/Query application deployment templates.md)

|Queries application deployment templates.|
|[Update an application deployment template](/intl.en-US/API Reference/Applications/Update an application deployment template.md)

|Updates an application deployment template.|
|[Delete an application deployment template](/intl.en-US/API Reference/Applications/Delete an application deployment template.md)

|Deletes an application deployment template.|
|[Create a trigger](/intl.en-US/API Reference/Applications/Create a trigger.md)|Creates an application trigger.|
|[Query triggers](/intl.en-US/API Reference/Applications/Query triggers.md)|Queries specific triggers that are created for a specified application.|
|[Delete a trigger](/intl.en-US/API Reference/Applications/Delete a trigger.md)|Deletes an application trigger.|
|[Query the details of an orchestration template](/intl.en-US/API Reference/Applications/Query the details of an orchestration template.md)|Queries the details of a specified application deployment template.|

## Components

|API|Description|
|---|-----------|
|[Install add-ons for a cluster](/intl.en-US/API Reference/Add-ons/Install add-ons for a cluster.md)

|Install components in a specified ACK cluster.|
|[t1957411.md\#]()

|Queries the versions of components in a specified ACK cluster.|
|[Query supported add-ons](/intl.en-US/API Reference/Add-ons/Query supported add-ons.md)

|Query the details of components that are installed in ACK clusters.|
|[UpgradeClusterAddons](/intl.en-US/API Reference/Add-ons/UpgradeClusterAddons.md)|Upgrades a component to the specified version in a specified ACK cluster.|
|[Query the upgrade statuses of cluster add-ons](/intl.en-US/API Reference/Add-ons/Query the upgrade statuses of cluster add-ons.md)|Queries the upgrade status of multiple components at a time.|
|[Modify an add-on in a managed cluster](/intl.en-US/API Reference/Add-ons/Modify an add-on in a managed cluster.md)|Modifies ConfigMaps for kube-apiserver and Kube Controller Manager \(KCM\). This allows you to modify the configurations of these components.|
|[UnInstallClusterAddons](/intl.en-US/API Reference/Add-ons/UnInstallClusterAddons.md)

|Uninstalls components from a specified ACK cluster.|

## Labels

|API|Description|
|---|-----------|
|[t2036587.md\#]()

|Adds labels to a specified ACK cluster.|
|[Modify the tags of a cluster](/intl.en-US/API Reference/Tag/Modify the tags of a cluster.md)

|Modifies the labels of a specified ACK cluster.|
|[ListTagResources](/intl.en-US/API Reference/Tag/ListTagResources.md)

|Queries the labels of the specified resources.|
|[t2036666.md\#]()

|Removes labels from a specified ACK cluster.|

