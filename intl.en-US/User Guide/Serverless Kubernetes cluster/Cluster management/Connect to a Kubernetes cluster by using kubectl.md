# Connect to a Kubernetes cluster by using kubectl {#task_msl_bc1_ydb .task}

To connect to a Kubernetes cluster from a client computer, use the Kubernetes command line client kubectl.

1.  Download the latest kubectl client from the [Kubernetes version](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG.md?spm=a2c4g.11186623.2.4.9ZrN6B&file=CHANGELOG.md) page.
2.  Install and set the kubectl client. 

    For more information, see [Install and set kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

3.  Configure the cluster credentials. 

    You can view the cluster credentials on the cluster information page.

    1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
    2.  Under Kubernetes, click **Clusters** in the left-side navigation pane.
    3.  Click **Manage** at the right of the cluster.
    4.  In the **Connection Information** section, view the master node SSH IP address. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6961/15553284305649_en-US.png)

    5.  Copy the cluster credentials to a local file, and you can create and save the cluster credentials to $HOME/.kube/config \(location where kubectl credentials are to be stored\). You can also name a new file, such as /tmp/kubeconfig, and run the `export KUBECONFIG=/tmp/kubeconfig` command.
    6.  After the preceding operation is performed, you can confirm the cluster connectivity by running the following command. 

        ```
        #kubectl get pod
        No resources found.
        ```


After the configuration is complete, you can use kubectl to access the Kubernetes cluster from a local computer.

