# Delete Istio {#task_qmp_ltq_gfb .task}

You can delete a deployed Istio through the **deleting**operation.

-   You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   You have created an **Istio**. For more information, see [Deploy Istio](reseller.en-US/User Guide/Kubernetes cluster/Istio/Deploy Istio.md#).

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.  Under the Kubernetes menu, click **Application** \> **Helm** in the left-side navigation pane. 
3.  Select a cluster, select the Istio to be deleted, and click **Delete** in the action column. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21820/154025852612751_en-US.png)

4.  In the displayed dialog box, click **OK**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21820/154025852612752_en-US.png)

    **Note:** 

    -   Do not select the **Purge** box:
        -   Releasing records are not deleted:

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21820/154025852712783_en-US.png)

        -   The name of this Istio cannot be used again.

            When you redeploy the Istio through the cluster interface, the deployment status is deployed.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21820/154025852712784_en-US.png)

            When you redeploy the Istio through the application catalog, the system prompts you that deployment or resource with the same name already exists and please modify the Istio name.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21820/154025852712785_en-US.png)

    -   Selecting the **Purge** box deletes all releasing records and the Istio name can be reused.
    We recommend that you keep the **Purge** box selected.


Back to the Release List page, you can see that the Istio is removed.

