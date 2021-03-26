---
keyword: [edge node, managed edge Kubernetes cluster]
---

# Add an edge node

This topic describes how to add an edge node as a worker node to a managed edge Kubernetes cluster in the Container Service for Kubernetes \(ACK\) console. Make sure that the added node can communicate with the Kubernetes API server of the cluster. You can add Elastic Compute Service \(ECS\) instances, Edge Node Service \(ENS\) instances, and on-premises servers to a managed edge Kubernetes cluster.

-   A managed edge Kubernetes cluster is created. For more information, see [Create a managed edge Kubernetes cluster](/intl.en-US/User Guide for Edge Container Service/Edge cluster management/Create a managed edge Kubernetes cluster.md).
-   To enable the cluster to automatically add ENS instances, ENS must be activated.

**Note:** When you use a managed edge Kubernetes cluster, take note of the following limits:

-   You can add only nodes that run CentOS 7.4, CentOS 7.6, or Ubuntu 18.04.
-   Only ENS instances with at least 2 cores and 4 GB of memory can be automatically added to the cluster. In addition, the ENS instances must be in the Running state and run CentOS 7.4 or 7.6.
-   If the version of the managed edge Kubernetes cluster is 1.14.8-aliyunedge.1 or later, Advanced RISC Machine \(ARM\) nodes that run CentOS 7.4 or ARM64 nodes that run Ubuntu 18.04 can be added to the cluster.

## Add a node

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  Add an existing node by using the following methods:

    -   Method 1:
        1.  In the left-side navigation pane of the ACK console, click **Clusters**.
        2.  On the Clusters page, find the cluster to which you want to add nodes and choose **More** \> **Add Existing Node** in the **Actions** column.
    -   Method 2:
        1.  In the left-side navigation pane of the ACK console, click **Clusters**.
        2.  On the Clusters page, find the cluster to which you want to add nodes. Then, click the name of the cluster or click **Details** in the **Actions** column.
        3.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node**.
        4.  In the left-side navigation pane, choose **Clusters** \> **Nodes** to go to the Nodes page.
        5.  In the upper-right corner of the page, click **Add Existing Node**.
3.  On the page that appears, you can select **Manual** to manually add existing instances.

    If you select the manual mode, you can add only one instance at a time.

    **Note:** In manual mode, you can add ECS instances, ENS instances, and on-premises servers.

    1.  Click **Next Step**.
    2.  On the Specify Instance Information wizard page, configure the node based on your requirements. For more information, see [Parameters](#section_640_7ra_xed).

        **Note:** If the version of the managed edge Kubernetes cluster is 1.14.8-aliyunedge.1 or later, ARM or ARM64 nodes can be added to the cluster.

    3.  After the configuration is completed, click **Next Step**.
    4.  On the Complete wizard page, click **Copy**. Then, log on to the selected edge node and run the following command:

        ```
        wget http://aliacs-k8s-cn-beijing.oss-cn-beijing.aliyuncs.com/public/pkg/run/attach/1.14.8-aliyunedge.1/amd64/edgeadm -O edgeadm; chmod u+x edgeadm; ./edgeadm join --openapi-token=XXXXX --node-spec="{\"enableIptables\":true,\"quiet\":true,\"manageRuntime\":true,\"nodeNameOverride\":\"*.ack.edge\",\"allowedClusterAddons\":[\"kube-proxy\",\"flannel\",\"coredns\"]}" --region=cn-beijing
        ```

    If the edge node is added to the cluster, the following result is returned.

    ![Node added](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9883068951/p52432.png)


## Parameters

|Parameter|Description|Default value|
|---------|-----------|-------------|
|flannelIface|The name of the network interface controller \(NIC\) that is used by the Flannel plug-in.|The name of the NIC that is specified in the default route entry of the node.|
|enableIptables|Specifies whether to enable iptables.|false|
|quiet|Specifies whether to answer all questions with yes when you add nodes.|false|
|manageRuntime|Specifies whether to use edgeadm to install and manage the runtime.|false|
|nodeNameOverride|The name of the node.|-   "". This is the default value. This value specifies that the hostname is used as the node name.
-   "\*". This value specifies that a random string that contains six characters is used as the node name.
-   "\*.XXX". This value specifies that a random string that is followed by a suffix is used as the node name. The random string contains six characters. |
|allowedClusterAddons|The list of add-ons to be installed. By default, this parameter is empty. This indicates that no add-on is installed. For a standard edge node, set this parameter to \["kube-proxy","flannel","coredns"\].|\[\]|
|gpuVersion|Specifies whether the node to be added is a GPU-accelerated node. By default, this parameter is empty. Supported GPU models are Nvidia\_Tesla\_T4, Nvidia\_Tesla\_P4, and Nvidia\_Tesla\_P100.|"". This is the default value. This value specifies that the node to be added is not a GPU-accelerated node.|
|inDedicatedNetwork|Specifies whether an Express Connect circuit is used to connect to the managed edge Kubernetes cluster.|false|
|labels|Specifies the labels to be added to the node.|\{\}|
|annotations|Specifies the annotations to be added to the node.|\{\}|
|nodeIface|This parameter specifies the following information:-   Specifies the node IP address that kubelet retrieves from the specified network interface. If you do not specify this parameter, kubelet attempts to retrieve the node IP address in the following order:
    -   Searches /etc/hosts for the node whose name is the same as the specified hostname.
    -   Finds the IP address of the network interface that is specified in the default route entry of the node.
-   Specifies the name of the NIC that is used by the Flannel plug-in. In this case, this parameter is equivalent to the flannellface parameter. This parameter will soon replace the flannellface parameter.

|""|

