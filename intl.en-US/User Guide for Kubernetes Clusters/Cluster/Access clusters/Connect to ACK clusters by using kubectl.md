---
keyword: [K8s, kubectl, temporary kubeconfig, kubeconfig, cluster credentials]
---

# Connect to ACK clusters by using kubectl

In addition to the Container Service for Kubernetes \(ACK\) console, you can also use the Kubernetes command-line tool kubectl to manage clusters and applications. This topic describes how to connect to an ACK cluster from a kubectl client.

kubectl is a standard Kubernetes command-line tool. You can use kubectl to connect to and manage all of the ACK cluster types, such as dedicated Kubernetes clusters, managed Kubernetes clusters, and serverless Kubernetes \(ASK\) clusters. Managed Kubernetes clusters include standard managed Kubernetes clusters and professional managed Kubernetes clusters. For more information about kubectl, see [kubectl](https://kubernetes.io/docs/user-guide/kubectl/).

## Step 1: Install kubectl

1.  Select a client to install kubectl.

    You can connect to a cluster over the Internet or through a private connection. We recommend that you select a client based on the following information:

    -   Connect to the cluster over the Internet

        If an elastic IP address \(EIP\) is associated with the API server of the cluster, the API server is exposed to the Internet. In this case, the cluster that you want to access does not have limits on the client that you choose. For more information about how to enable public access to the API server, see [Control public access to the API server of a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Cluster access control/Access the Kubernetes API server over the Internet.md).

    -   Connect to the cluster through a private connection

        If you want to connect to the cluster through a private connection, your client must be deployed in the same virtual private cloud \(VPC\) as the cluster.

2.  Download and install kubectl on the client.

    For more information, see [Install and configure kubectl](https://kubernetes.io/docs/tasks/kubectl/install/).


## Step 2: Select a type of cluster credentials

Each ACK cluster provides two types of cluster credentials for you to connect to the cluster over the Internet or through a private connection. The cluster credentials are stored in a kubeconfig.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the **Cluster Information** page, click the **Connection Information** tab. Then, select cluster credentials for connecting to the cluster over the Internet or through a private connection.

    **Note:** You can also use a temporary kubeconfig to control the validity period of the cluster credentials. This allows you to access the cluster in a more flexible manner. For more information, see [Generate a temporary kubeconfig](#section_4ps_hwr_03y).

    ![kubeconfig](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/0973972261/p269156.png)


## Step 3: Add cluster credentials

By default, kubectl searches for a file named config in the $HOME/.kube directory from the client. The file stores the credentials for authenticating to the cluster that you want to access. kubectl uses this file to connect to the cluster.

1.  Select the **Public Access** or **Internal Access** tab and then click **Copy**.

2.  Copy the cluster credentials to the config file in the $HOME/.kube directory, save the file, and exit.

    **Note:** If the .kube folder and the config file do not exist in the $HOME/ directory, you need to manually create the folder and file.

    If a dedicated Kubernetes cluster is used and you have enabled SSH logon over the Internet for the cluster, the **SSH** tab appears on the **Connection Information** tab. You can also log on to a master node by using SSH. After you add the cluster credentials to the kubeconfig file, you can use kubectl to connect to and manage the cluster. For more information about how to connect to a dedicated Kubernetes cluster by using SSH, see [Connect to the master nodes of a dedicated Kubernetes cluster by using SSH](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/More connection methods/Use SSH to connect to an ACK cluster.md).

    ![Dedicated Kubernetes cluster](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9529449261/p301443.png)


## Step 4: Verify the connectivity

After you add the cluster credentials, run the kubectl command to verify the connectivity to the cluster. Run the following command to query the namespaces of the cluster:

```
kubectl get namespace
```

Expected output:

```
NAME              STATUS   AGE
default           Active   4h39m
kube-node-lease   Active   4h39m
kube-public       Active   4h39m
kube-system       Active   4h39m
```

## Generate a temporary kubeconfig

You can use a temporary kubeconfig to store cluster credentials and set the validity period of the temporary kubeconfig. This allows you to access the cluster in a more flexible manner.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the **Cluster Information** page, click the **Connection Information** tab, and then click **Generate Temporary kubeconfig**.

5.  In the **Temporary kubeconfig** dialog box, set the validity period of the temporary kubeconfig and select a connection mode: Public Access or Internal Access.

    ![tempo](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/0973972261/p269169.png)

    -   To connect to the cluster over the Internet, select **Public Access** and click **Generate Temporary kubeconfig**. Then, click **Copy** to copy the content in the code block to the `$HOME/.kube/config` file on your client.
    -   To connect to the cluster through a private connection, select **Internal Access** and click **Generate Temporary kubeconfig**. Then, click **Copy** to copy the content in the code block to the `$HOME/.kube/config` file on your client.
    Then, you can connect to the cluster by using the temporary kubeconfig. You cannot connect to the cluster after the temporary kubeconfig expires.


## Revoke a kubeconfig

You can revoke a kubeconfig that is no longer in use to ensure the security of the cluster. For more information, see [Revoke a KubeConfig credential](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Cluster access control/Revoke a KubeConfig credential.md).

**Related topics**  


[Query the kubeconfig file of a cluster](/intl.en-US/API Reference/Clusters/Query the kubeconfig file of a cluster.md)

