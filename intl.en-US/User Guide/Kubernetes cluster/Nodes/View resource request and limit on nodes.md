# View resource request and limit on nodes {#task_v45_blv_hfb .task}

The Container Service Console allows you to view resource usage of each node in a Kubernetes cluster.

You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.  Under Kubernetes, click **Clusters** \> **Nodes**. 

    You can view the resource usage for the CPU and memory of each node, namely, the request and limit, which are calculated as follows:

    -   CPU request = sum \(CPU request value from all pods on the current node\) /total CPU of the current node.
    -   CPU limit= sum \(actual CPU usage of all pods on the current node\)/total CPU of the current node.
    -   Memory request = sum \(memory request value from all pods on the current node\) /total memory of the current node.
    -   Memory limit= sum \(actual memory usage of all pods on the current node\)/total memory of the current node.
    **Note:** 

    -   You can allocate loads to a node based on the resource usage on the node. For more information, see [Set node scheduling](reseller.en-US/User Guide/Kubernetes cluster/Nodes/Set node scheduling.md#).
    -   When both the request and limit on a node is 100%, no new pod is scheduled to the node.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/22230/153958341813297_en-US.png)


