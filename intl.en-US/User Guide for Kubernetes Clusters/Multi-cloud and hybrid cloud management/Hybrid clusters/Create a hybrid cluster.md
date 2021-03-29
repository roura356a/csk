---
keyword: [hybrid cluster, registered cluster]
---

# Create a hybrid cluster

After you register a self-managed on-premises Kubernetes cluster in the Container Service for Kubernetes \(ACK\) console, you can add cloud computing nodes to the cluster. This way, you can create a hybrid cluster that manages both cloud and on-premises computing resources. This topic describes how to create a hybrid cluster.

-   The self-managed Kubernetes cluster deployed in the data center is connected to the virtual private cloud \(VPC\) where the cluster registration proxy is deployed. The computing nodes and containers are connected to each other.
-   The self-managed Kubernetes cluster is connected to the cluster registration proxy by using the agent for internal network connection.
-   The cloud computing nodes that are added through the cluster registration proxy can access the API server of the self-managed Kubernetes cluster deployed in the data center.

1.  Plan the CIDR blocks for the cluster that uses the Terway network plug-in. For more information, see [Network planning](/intl.en-US/User Guide for Kubernetes Clusters/Network/Plan CIDR blocks for an ACK cluster.md).

2.  Connect the on-premises Kubernetes cluster to a VPC. For more information, see [Physical connection solutions](/intl.en-US/Product Introduction/Physical connection solutions.md).

3.  Create a cluster registration proxy in the VPC and register the on-premises Kubernetes cluster. For more information, see [Create a cluster registration proxy and register an on-premises cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Register an external Kubernetes cluster.md).

4.  Configure container network plug-ins. For more information, see [Install and configure container network plug-ins]().

5.  Configure a custom script to add cluster nodes. For more information, see [Use a custom script to add cluster nodes]().

6.  Create a node pool and add computing nodes to the node pool. For more information, see [Create and scale out a node pool]().

    Configure auto scaling. For more information, see [Configure auto scaling]().


