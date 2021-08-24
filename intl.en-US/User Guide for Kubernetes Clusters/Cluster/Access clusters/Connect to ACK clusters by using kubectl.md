---
keyword: [K8s, kubectl, temporary kubeconfig, kubeconfig]
---

# Connect to ACK clusters by using kubectl

You can connect to a Container Service for Kubernetes \(ACK\) cluster from your on-premises machine by using the kubectl command-line tool. This topic describes how to connect to an ACK cluster by using kubectl.

For more information about kubectl, see [kubectl](https://kubernetes.io/docs/user-guide/kubectl/).

## Connect to an ACK cluster by using kubectl

1.  Install and set up a kubectl client.

    For more information, see [Install and set up kubectl](https://kubernetes.io/docs/tasks/kubectl/install/).

2.  Configure the credentials for accessing the cluster.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  In the left-side navigation pane of the details page, click **Cluster Information**.

    5.  On the **Cluster Information** page, click the **Connection Information** tab.

        ![kubeconfig](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/0973972261/p269156.png)

    6.  Configure the kubeconfig file that stores the credentials.

        -   To connect to the cluster over the Internet, click the **Public Access** tab and click **Copy** to copy the content in the code block to the $HOME/.kube/config file of your on-premises machine.

            **Note:** By default, kubectl searches the $HOME/.kube/config directory for the config file. kubectl communicates with a cluster based on the configurations saved in the kubeconfig file.

        -   To connect to the cluster over the internal network, click the **Internal Access** tab and click **Copy** to copy the content in the code block to the `$HOME/.kube/config` file of your on-premises machine.
    **Expected result**

    -   To connect to a managed Kubernetes cluster, you can run kubectl commands to connect to the cluster from your on-premises machine after you configure the kubeconfig file.

    -   To connect to a dedicated Kubernetes cluster, you can obtain the IP address for connecting to a master node over SSH from the **Basic Information** tab and run kubectl commands to connect to the cluster from your on-premises machine.

        ![IP address for SSH connection to a master node](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/1435359951/p9791.png)

        **Note:** To use SSH to log on to a dedicated Kubernetes cluster, you must enable **SSH logon** when you create the cluster. For more information, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).


## Generate a temporary kubeconfig file

To ensure cluster security, you can generate a temporary kubeconfig file and set a validity period for the file. You can use this kubeconfig file to enable temporary access to ACK clusters.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the **Cluster Information** page, click the **Connection Information** tab, and then click **Generate Temporary kubeconfig**.

5.  In the **Temporary kubeconfig** dialog box, set the validity period of the kubeconfig file and select the access mode \(public access or internal access\).

    ![tempo](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/0973972261/p269169.png)

    -   To connect to the cluster over the Internet, select **Public Access** and click **Generate Temporary kubeconfig**. Then, click **Copy** to copy the content in the code block to the `$HOME/.kube/config` file of your on-premises machine. `kubectl` retrieves the credentials from this file.
    -   To connect to the cluster over the internal network, select **Internal Access** and click **Generate Temporary kubeconfig**. Then, click **Copy** to copy the content in the code block to the `$HOME/.kube/config` file of your on-premises machine. `kubectl` retrieves the credentials from this file.

## Revoke a kubeconfig file

You can revoke a kubeconfig file that is no longer in use to ensure the security of the cluster. For more information, see [Revoke a KubeConfig credential](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Cluster access control/Revoke a KubeConfig credential.md).

**Related topics**  


[Query the kubeconfig file of a cluster](/intl.en-US/API Reference/Clusters/Query the kubeconfig file of a cluster.md)

