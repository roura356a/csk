# Use kubectl commands in Cloud Shell to manage a Kubernetes cluster {#task_y24_gbm_2gb .task}

This topic describes how to use kubectl on Cloud Shell to manage a Kubernetes cluster after you log on to the console of Alibaba Cloud Container Service for Kubernetes.

You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).

If you want to use kubectl to manage a Kubernetes cluster of Container Servcie, you can download kubectl to your local host. For more information, see [Connect to a Kubernetes cluster by using kubectl](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#). Additionally, you can also start Cloud Shell on the console of Container Service for Kubernetes, and then use kubectl on Cloud Shell to manage a Kubernetes cluster.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Kubernetes, click **Clusters**.
3.  In the **Action** column of the target cluster, choose **More** \> **cos.cls.cloudshell**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/81209/155894945934720_en-US.png)

    **Note:** 

    Do the following:

    -   On the Authorization page, click **OK** to obtain a temporary access key that expires within one hour.
    -   On the Storage Space page, click **Create Now** or **Skip** as needed.
4.  On Cloud Shell, you can use kubectl to manage a Kubernetes cluster of Container Service. 

    **Note:** When you start Cloud Shell associated with the Kubernetes cluster, the system loads the kubeconfig file of the cluster onto Cloud Shell. Then you can use kubectl to manage your cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/81209/155894945934727_en-US.png)


