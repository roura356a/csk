---
keyword: [edge node, managed edge Kubernetes cluster]
---

# Add an edge node

This topic describes how to add an edge node as a worker node to a managed edge Kubernetes cluster in the Container Service for Kubernetes \(ACK\) console. You must ensure that the added node can communicate with the Kubernetes API server of the cluster. You can add Elastic Compute Service \(ECS\) instances, Edge Node Service \(ENS\) instances, and on-premises servers to a managed edge Kubernetes cluster.

-   A managed edge Kubernetes cluster is created. For more information, see [Create a managed edge Kubernetes cluster](/intl.en-US/User Guide for Edge Container Service/Edge cluster management/Create a managed edge cluster.md).
-   To enable the cluster to automatically add ENS instances, ENS must be activated and an edge Kubernetes service must be created.

**Note:** When you use a managed edge Kubernetes cluster, note the following limits:

-   Managed edge Kubernetes clusters are in public preview. You can deploy up to 40 nodes in a managed edge Kubernetes cluster. To increase the quota, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).
-   You can add only nodes that run CentOS 7.4, CentOS 7.6, or Ubuntu 18.04.
-   Only ENS instances with at least 2 cores and 4 GB memory can be automatically added to the cluster. In addition, the ENS instances must run CentOS 7.4 or 7.6, and work in the Running state.
-   If the version of a managed edge Kubernetes cluster is 1.14.8-aliyunedge.1 or later, Advanced RISC Machine \(ARM\) node that run CentOS 7.4 or ARM64 nodes that run Ubuntu 18.04 can be added to this cluster.

## Add a node

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  Add an existing node in the following methods:

    -   Method 1:
        1.  Log on to the ACK console. In the left-side navigation pane, click **Clusters**.
        2.  On the Clusters page, find the cluster to which you want to add nodes and choose **More** \> **Add Existing Node** in the **Actions** column.
    -   Method 2:
        1.  Log on to the ACK console. In the left-side navigation pane, click **Clusters**.
        2.  On the Clusters page, find the cluster to which you want to add nodes and click **Details** in the **Actions** column.
        3.  In the left-side navigation pane, click **Nodes**.
        4.  In the upper-right corner of the page, click **Add Existing Node**.
3.  On the page that appears, select the **Manual** mode to add existing instances.

    If you select the Manual mode, you can add only one instance at a time.

    **Note:** In Manual mode, you can add ECS instances, ENS instances, and on-premises servers.

    1.  Click **Next Step**.
    2.  On the Specify Instance Information wizard page, configure the node based on your requirements. For more information, see [Parameters](#section_640_7ra_xed).

        **Note:** If the version of a managed edge Kubernetes cluster is 1.14.8-aliyunedge.1 or later, Advanced RISC Machine \(ARM\) or ARM64 nodes can be added to the cluster.

    3.  After the configuration is complete, click **Next Step**.
    4.  On the Complete wizard page, click **Copy**. Then, log on to the selected edge node and run the following command:

        ```
        wget http://aliacs-k8s-cn-beijing.oss-cn-beijing.aliyuncs.com/public/pkg/run/attach/1.14.8-aliyunedge.1/amd64/edgeadm -O edgeadm; chmod u+x edgeadm; ./edgeadm join --openapi-token=XXXXX --node-spec="{\"enableIptables\":true,\"quiet\":true,\"manageRuntime\":true,\"nodeNameOverride\":\"*.ack.edge\",\"allowedClusterAddons\":[\"kube-proxy\",\"flannel\",\"coredns\"]}" --region=cn-beijing
        ```

    The ENS instance is added to the cluster, as shown in the following figure.

    ![Added successfully](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9883068951/p52432.png)


## Parameters

|Parameter|Description|Default|
|---------|-----------|-------|
|flannelIface|The name of the network interface controller \(NIC\) that is used by the Flannel plug-in.|The name of the NIC that is recorded in the default route entry of the node.|
|enableIptables|Specifies whether to enable iptables.|false|
|quiet|Specifies whether to answer all questions with yes when adding nodes.|false|
|manageRuntime|Specifies whether to use edgeadm to install and manage the runtime.|false|
|nodeNameOverride|The name of the node.|-   "". This is the default setting. This setting specifies that the hostname is used as the node name.
-   "\*". This setting specifies that a random string that contains six characters is used as the node name.
-   "\*.XXX". The setting specifies that a random string that is followed by a suffix is used as the node name. The random string contains six characters. |
|allowedClusterAddons|The list of addons to be installed. By default, this parameter is not specified. It indicates that no add-on is installed. For a standard edge node, set this parameter to \["kube-proxy","flannel","coredns"\].|\[\]|
|gpuVersion|Specifies whether the node to be added is a GPU-accelerated node. By default, this parameter is not specified. Supported GPU versions are Nvidia\_Tesla\_T4, Nvidia\_Tesla\_P4, and Nvidia\_Tesla\_P100.|"". This is the default setting. It indicates that the node to be added is not a GPU-accelerated node.|

