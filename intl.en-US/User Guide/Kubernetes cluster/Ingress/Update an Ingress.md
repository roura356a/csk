# Update an Ingress {#task_ky2_s14_vdb .task}

-   You have successfully created a Kubernetes cluster and Ingress controller is running normally in the cluster. For how to create a Kubernetes cluster, see [Create a Kubernetes cluster](intl.en-US/User Guide/Kubernetes cluster/Clusters/Create a cluster.md#).
-   You have successfully created an Ingress. For more information, see [Create an Ingress in Container Service console](intl.en-US/User Guide/Kubernetes cluster/Ingress/Create an Ingress in Container Service console.md#).

1.   Log on to the [Container Service console](https://cs.console.aliyun.com). 
2.   Click Kubernetes \>**Application \>** \> **Ingress**in the left-side navigation pane. 
3.   Select the cluster and namespace from the Clusters and Namespace drop-down lists. Click **Update** at the right of the Ingress. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6916/15330262244562_en-US.png)

4.   Update the Ingress parameters in the displayed dialog box and then click **OK**. In this example, change `test.[cluster-id].[region-id].alicontainer.com` to `testv2.[cluster-id].[region-id].alicontainer.com`. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6916/15330262244564_en-US.png)


On the Ingress page, you can see a rule of this Ingress is changed.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6916/15330262244565_en-US.png)

