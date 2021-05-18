---
keyword: [kubectl, connect to a Kubernetes cluster]
---

# Use kubectl to connect to an ASK cluster

To connect to a serverless Kubernetes \(ASK\) cluster from your on-premises machine, we recommend that you use the kubectl command-line tool. This topic describes how to use kubectl to connect to an ASK cluster.

1.  Download the latest kubectl client from the [Kubernetes version description](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG.md?spm=a2c4g.11186623.2.4.9ZrN6B&file=CHANGELOG.md) page.

2.  Install and set up the kubectl client.

    For more information, see [Install and set up kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

3.  Configure the cluster credentials.

    You can view cluster credentials on the details page of a cluster.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  Click the Basic Information tab. In the **Cluster Information** section, you can view the public and internal IP addresses that are used to connect to the cluster.

        ![Connect to an ASK cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0297297951/p10242.png)

    5.  Click the **Connection Information** tab. You can copy the code on the Public Access tab or the Internal Access tab to the $HOME/.kube/config file of your on-premises machine. By default, kubectl retrieves cluster credentials from this file. You can also create another file, for example, /tmp/kubeconfig, and copy the cluster credentials into the file. Then, run the `export KUBECONFIG=/tmp/kubeconfig` command.

    6.  After the preceding operation is complete, run the following command to check whether the kubectl client is connected to the cluster:

        ```
        kubectl get pod
        ```

        ```
        No resources found.
        ```


After the preceding operations are complete, you can use kubectl to connect to the ASK cluster from your on-premises machine.

