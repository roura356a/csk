---
keyword: [edge node, managed edge Kubernetes cluster]
---

# Add an edge node

This topic describes how to add an edge node as a worker node to an existing managed edge Kubernetes cluster in the Container Service for Kubernetes \(ACK\) console. Make sure that the node to be added can communicate with the Kubernetes API server of the cluster. You can add Elastic Compute Service \(ECS\) instances, Edge Node Service \(ENS\) instances, and on-premises servers to a managed edge Kubernetes cluster.

-   A managed edge Kubernetes cluster is created. For more information, see [Create a managed edge Kubernetes cluster](/intl.en-US/User Guide for Edge Container Service/Edge cluster management/Create a managed edge Kubernetes cluster.md).
-   ENS is activated if you want to automatically add ENS instances.

## Limits

-   The hosting service of edge Kubernetes clusters is in public preview. Each cluster can contain up to 40 nodes. To increase the node quota, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).
-   Only ENS nodes with at least 2 CPU cores and 4 GB of memory can be automatically added to an edge Kubernetes cluster. In addition, the ENS nodes must be in the Running state and run CentOS 7.4 or 7.6.
-   If you want to manually add nodes, the nodes must run operating systems that are listed in the following table.

    |System architecture|Operating system version|Kernel version|Kubernetes version|
    |-------------------|------------------------|--------------|------------------|
    |AMD64|CentOS 7.4|3.10.X|≥ 1.12.6-aliyunedge.1|
    |AMD64|CentOS 7.6|3.10.X|≥ 1.12.6-aliyunedge.1|
    |AMD64|CentOS 8.0|4.18.X|≥ 1.18.8-aliyunedge.1|
    |AMD64|Ubuntu 18.04|4.15.X|≥ 1.12.6-aliyunedge.1|
    |AMD64|Ubuntu 18.04|5.4.X|≥ 1.16.9-aliyunedge.1|
    |AMD64|Ubuntu 20.04|5.4.X|≥ 1.18.8-aliyunedge.1|
    |ARM64|CentOS 8.0|4.19.X|≥ 1.14.8-aliyunedge.1|
    |ARM64|Ubuntu 18.04|4.9.X|≥ 1.14.8-aliyunedge.1|
    |ARM64|Ubuntu 18.04|4.19.X|≥ 1.14.8-aliyunedge.1|
    |ARM|CentOS 7.7|4.19.X|≥ 1.14.8-aliyunedge.1|


## Add a node

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  Add an existing node. You can use the following methods:

    -   Method 1:
        1.  In the left-side navigation pane of the ACK console, click **Clusters**.
        2.  On the Clusters page, find the cluster to which you want to add a node and choose **More** \> **Add Existing Node** in the **Actions** column.
    -   Method 2:
        1.  In the left-side navigation pane of the ACK console, click **Clusters**.
        2.  On the Clusters page, find the cluster to which you want to add a node. Then, click the name of the cluster or click **Details** in the **Actions** column.
        3.  In the left-side navigation pane of the cluster details page, choose **Nodes** \> **Nodes**.
        4.  In the left-side navigation pane, choose **Clusters** \> **Nodes** to go to the Nodes page.
        5.  In the upper-right corner of the page, click **Add Existing Node**.
3.  On the page that appears, you can select **Manual** to manually add existing instances.

    Select the manual mode.

    **Note:** In manual mode, you can add ECS instances, ENS instances, and on-premises servers.

    1.  Click **Next Step**.
    2.  On the Specify Instance Information wizard page, configure the node based on your requirements. For more information, see [Parameters](#section_640_7ra_xed).

        **Note:** The default **validity period of the script** is 1 hour. If you want to use the script for more than 1 hour, you can set the validity period to an appropriate value. If you set the **validity period of the script** to 0 hour, it indicates that the script is permanently valid.

    3.  After the configuration is completed, click **Next Step**.
    4.  On the Complete wizard page, click **Copy** to copy the script to the edge node that you want to add. Then, execute the script on the node.
    If the following result is returned, it indicates that the edge node is added to the cluster.

    ![Node added](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9883068951/p52432.png)


## Parameters

|Parameter|Description|Default|
|---------|-----------|-------|
|flannelIface|The name of the network interface controller \(NIC\) that is used by the Flannel plug-in|The name of the NIC that is specified in the default route entry of the node|
|enableIptables|Specifies whether to enable `iptables`|`false`|
|quiet|Specifies whether to answer all questions with `yes` when you add nodes|`false`|
|manageRuntime|Specifies whether to use edgeadm to install and manage the runtime|`false`|
|nodeNameOverride|The name of the node|-   "". This is the default value. This value specifies that the hostname is used as the node name.
-   "\*". This value specifies that a random string that contains six characters is used as the node name.
-   "\*.XXX". This value specifies that a random string followed by a suffix is used as the node name. The random string contains six characters. |
|allowedClusterAddons|The list of components to be installed. By default, this parameter is empty. This indicates that no component is installed. For a regular node, set this parameter to \["kube-proxy","flannel","coredns"\].|\[\]|
|gpuVersion|Specifies whether the node to be added is a GPU-accelerated node. By default, this parameter is empty. Supported GPU models are Nvidia\_Tesla\_T4, Nvidia\_Tesla\_P4, and Nvidia\_Tesla\_P100.|"". This is the default value. This value specifies that the node to be added is not a GPU-accelerated node.|
|inDedicatedNetwork|Specifies whether an Express Connect circuit is used to connect to the managed edge Kubernetes cluster|`false`|
|labels|Specifies the labels to be added to the node|null|
|annotations|Specifies the annotations to be added to the node configurations|null|
|nodeIface|This parameter specifies the following information:-   This parameter specifies that kubelet retrieves the node IP address from the specified network interface. If you do not set this parameter, kubelet attempts to retrieve the node IP address in the following order:
    -   Searches /etc/hosts for the node whose name is the same as the specified hostname.
    -   Retrieves the IP address of the network interface that is specified in the default route entry of the node.
-   This parameter specifies the name of the NIC that is used by Flannel. In this case, this parameter is equivalent to the flannelIface parameter. This parameter will replace the flannelIface parameter in the future.

|""|

