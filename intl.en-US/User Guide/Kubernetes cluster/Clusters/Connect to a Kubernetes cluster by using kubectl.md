# Connect to a Kubernetes cluster by using kubectl {#task_ubf_lhg_vdb .task}

To connect to a Kubernetes cluster from a client computer, use the Kubernetes command line client [kubectl](https://kubernetes.io/docs/user-guide/kubectl/).

1.   Download the latest kubectl client from the [Kubernetes release page](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG.md). 
2.   Install and set the kubectl client. 

    For more information, see [Install and set kubectl](https://kubernetes.io/docs/tasks/kubectl/install/).

3.   Configure the cluster credentials. 

    You can use the `scp` command to safely copy the master node configurations from the `/etc/kubernetes/kube.conf` file on the master virtual machine of the Kubernetes cluster to the `$HOME/.kube/config` file \(where the`kubectl` expected credentials reside\) of the local computer.

    -   If you select Password in the Login field when creating the cluster, copy the kubectl configuration file in the following method:

        ```
        mkdir $HOME/.kube
        scp root@<master-public-ip>:/etc/kubernetes/kube.conf $HOME/.kube/config
        ```

    -   If you select Key Pair in the Login field when creating the cluster, copy the kubectl configuration file in the following method:

        ```
        mkdir $HOME/.kube
        scp -i [the storage path of the .pem private key file on the local machine] root@:/etc/kubernetes/kube.conf $HOME/.kube/config
        ```

    You can check the cluster `master-public-ip` on the cluster information page.

    1.   Log on to the [Container Service console](https://cs.console.aliyun.com). 
    2.   Under Kubernetes, click **Clusters** in the left-side navigation pane. 
    3.   Click **Manage** at the right of the cluster. 

        In the **Connection Information** section, view the Master node SSH IP address.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6883/15329315432009_en-US.png)


