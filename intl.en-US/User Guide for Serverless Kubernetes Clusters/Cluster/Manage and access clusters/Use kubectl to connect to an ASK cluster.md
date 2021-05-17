# Use kubectl to connect to an ASK cluster

This topic describes how to use kubectl to connect to a serverless Kubernetes \(ASK\) cluster from your on-premises machine.

1.  Download the latest kubectl client from the [Kubernetes version description](https://github.com/kubernetes/kubectl/releases) page.

2.  Install and set up the kubectl client.

    For more information, see [Install and set up kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

3.  Configure the cluster credentials.

    You can view the cluster credentials on the details page of the cluster.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane, click **Serverless Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Applications** in the **Actions** column.

    4.  In the left-side navigation pane, click **Cluster Information**.

    5.  Click the Basic Information tab. In the **Cluster Information** section, you can view the public and internal IP addresses.

        ![Connection information](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0297297951/p10242.png)

    6.  Click the **Connection Information** tab. You can copy the code block on the Public Access tab or the Internal Access tab to the file in the $HOME/.kube/config directory of your on-premises machine. By default, kubectl retrieves cluster credentials from this directory. You can also create a file, for example, a /tmp/kubeconfig file, and paste the cluster credentials into the file. Then, run the `export KUBECONFIG=/tmp/kubeconfig` command.

    7.  After the preceding operation is complete, run the following command to check whether the kubectl client is connected to the cluster:

        ```
        kubectl get pod
        ```

        ```
        No resources found.
        ```


After the preceding operations are complete, you can use kubectl to connect to the ASK cluster from your on-premises machine.

