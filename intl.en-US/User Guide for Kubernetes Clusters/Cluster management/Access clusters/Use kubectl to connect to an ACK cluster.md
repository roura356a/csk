# Use kubectl to connect to an ACK cluster

This topic describes how to use kubectl to connect to a cluster of Container Service for Kubernetes \(ACK\).

You can use the Kubernetes command-line tool [kubectl](https://kubernetes.io/docs/user-guide/kubectl/) to connect to an ACK cluster from your on-premises machine.

1.  Install and set up the kubectl client.

    For more information, see [Install and set up kubectl](https://kubernetes.io/docs/tasks/kubectl/install/).

2.  Configure the cluster credentials.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane, click **Clusters**.

    3.  On the Clusters page, click the name of the cluster to which you want connect or click **Applications** in the **Actions** column.

    4.  In the left-side navigation pane, click **Cluster Information**.

    5.  On the **Cluster Information** page, click the **Connection Information** tab.

        ![kubeconfig](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8895858951/p131580.png)

        -   To connect to the cluster over the Internet, click the **Public Access** tab and **copy** the content in the code block to the file in the`$HOME/.kube/config` directory of your on-premises machine. By default, the `kubectl` retrieves cluster credentials from this directory.
        -   To connect to the cluster over an internal network, click the **Internal Access** tab and **copy** the content in the code block to the file in the `$HOME/.kube/config` directory of your on-premises machine. By default, the `kubectl` retrieves cluster credentials from this directory.

-   If you want to connect to a cluster of ACK Managed Edition, you can run kubectl commands to connect to the cluster from your on-premises machine after you configure the kubeconfig file.

-   If you want to connect to a cluster of ACK Proprietary Edition, you can obtain the IP address for SSH connection to a master node from the **Basic Information** tab and run kubectl commands to connect to the cluster from your on-premises machine.

    ![IP address for SSH connection to a master node](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1435359951/p9791.png)

    **Note:** To use SSH to log on to a cluster of ACK Proprietary Edition, you must enable **SSH logon** when you create the cluster. For more information, see [Create a cluster of ACK Proprietary Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Proprietary Edition.md).


