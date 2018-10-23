# Update Istio {#task_lhb_ktq_gfb .task}

You can modify the deployed Istio through **updates**.

-   You have created an Kubernetes cluster. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   You have created an **Istio**. For more information, see [Deploy Istio](reseller.en-US/User Guide/Kubernetes cluster/Istio/Deploy Istio.md#).

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.  Under the Kubernetes menu, click **Application** \> **Helm** in the left-side navigation pane. 
3.  Select a cluster, select the Istio to be updated, and click **Update** in the action column. 

    **Note:** 

    -   The **release name** of the Istio that is deployed through the cluster interface is istio. Configurations to be updated are the same as the options configured in deployment.
    -   The **release name** of the Istio that is deployed through the application catalog is the name specified when you create the Istio. Configurations to be updated are the same as the options configured in deployment.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21819/154025850112755_en-US.png)

4.  In the displayed dialog box, modify parameters of the Istio, and then click **Update**. 

    In this example, update the Istio that is deployed through the cluster interface:

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21819/154025850112756_en-US.png)


You can view updated content in two ways:

-   After you complete the update, the page automatically jumps to theRelease List page. On the Resource tab, you can view updated content.
-   Under the Kubernetes menu, click **Application** \> **Pods**, and select the target cluster and namespace to view updating results.

