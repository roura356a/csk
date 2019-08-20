# Update Istio {#task_lhb_ktq_gfb .task}

This topic describes how to use the Container Service console to update Istio deployed on a Kubernetes cluster.

-   A Kubernetes cluster is created with ACK. For more information, see [Create a Kubernetes cluster](reseller.en-US//Create a Kubernetes cluster.md#).
-   Istio is deployed on the Kubernetes cluster. For more information, see [Deploy Istio on a Kubernetes cluster](reseller.en-US/User Guide for Kubernetes Clusters/Istio management/Deploy Istio on a Kubernetes cluster.md#).

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Application** \> **Release**.
3.  Click the **Helm** tab and select the target cluster to find the deployed Istio. Then, in the **Action** column, click **Update**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21819/156629546712755_en-US.png)

4.  In the displayed dialog box, modify the Istio parameters, and then click **Update**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21819/156629546712756_en-US.png)


You can view the updated Istio by using one of the following two methods:

-   On the Release List-Istio page that is automatically displayed, click the Resource tab.
-   In the left-side navigation pane under Container Service-Kubernetes, choose **Application** \> **Pods**. Then, select the target cluster and namespace.

