# Delete Istio {#task_qmp_ltq_gfb .task}

This topic describes how to use the Container Service console to delete the deployed Istio.

-   A Kubernetes cluster is created with ACK. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   Istio is deployed on the Kubernetes cluster. For more information, see [Deploy Istio on a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Istio management/Deploy Istio on a Kubernetes cluster.md#).
-   istio-init on the Helm tab page of the target cluster is in the running status.

    **Note:** You must first delete the deployed Istio before you can delete istio-init.

-   The Master node of the Kubernetes cluster is accessible. For more information, see [Connect to a Kubernetes cluster by using kubectl](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#).

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Application** \> **Release**.
3.  Click the **Helm** tab and select the target cluster to find the deployed Istio. Then, in the **Action** column, click **Delete**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21820/156393058612751_en-US.png)

4.  In the displayed dialog box, click **OK**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21820/156393058612752_en-US.png)

    The displayed dialog box contains a check box named **Purge**. By default, it is selected. We recommend that you retain the default setting.

    -   If you do not select this check box, the following settings apply:
        -   The release record of `istio` is retained.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21820/156393058612783_en-US.png)

        -   Istio cannot be deployed again on the target cluster.

            Specifically, if you redeploy Istio on the target cluster, the system prompts an error message and also shows that Istio is deployed.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21820/156393058612784_en-US.png)

    -   If you retain the check box selected, all release records are deleted. This means that Istio can be deployed again on the target cluster.
5.  In the left-side navigation pane under Container Service-Kubernetes, choose **Applications** \> **Pods**.
6.  Select the target cluster and the `istio-system` namespace to verify that pods related to Istio are deleted. 

    **Note:** If the `istio-config` resource in the `istio-system` namespace cannot be deleted, see [What do I do if a custom resource is retained after I deleted Istio](../../../../reseller.en-US/FAQ/Istio FAQ.md#).

7.  In the left-side navigation pane under Container Service-Kubernetes, choose **Applications** \> **Releases**.
8.  Click the **Helm** tab and select the target cluster to find `istio-init`. Then, in the **Action** column, click **Delete**. This action deletes the operator pods that used to deploy Istio.

1.  In the left-side navigation pane under Container Service-Kubernetes, choose **Applications** \> **Pods**.
2.  Select the target cluster and the `istio-system` namespace to verify that pods related to Istio are deleted.

