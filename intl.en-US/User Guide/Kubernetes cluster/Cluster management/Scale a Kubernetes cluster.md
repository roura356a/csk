# Scale a Kubernetes cluster {#concept_avm_wrh_1hb .concept}

This topic describes how to scale a Kubernetes cluster \(by increasing or decreasing the number of Worker nodes in the cluster\) in the Alibaba Cloud Container Service for Kubernetes console.

## Limits {#section_vgd_yrh_1hb .section}

-   The number of Master nodes in any Kubernetes cluster cannot be changed.
-   You can only decrease the number of Worker nodes that are added when you create or scale out the cluster. The number of Worker nodes that are added through the [Add an existing ECS instance](reseller.en-US/User Guide/Kubernetes cluster/Node management/Add an existing ECS instance.md#) feature cannot be decreased.
-   Worker nodes cannot be removed either through the kubectl delete command or through node removal operations in the Container Service console.
-   When you scale in a cluster, Worker nodes are removed from the cluster in the sequence that they were added when you scaled out the cluster.
-   You can scale in a cluster only if the cluster has more than one node that was created automatically.

## Procedure {#section_wk3_1sh_1hb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Clusters** \> **Clusters**.
3.  On the right of the target cluster, click **Scale Cluster**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16647/155228642710904_en-US.png)

4.  Click **Scale out** or **Scale in** and then set the number of Worker nodes.

    In this example, the number of Worker nodes of the cluster is increased from 1 to 4.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16647/155228642710905_en-US.png)

5.  Enter the node logon password.
6.  Click **Submit**.

In the left-side navigation pane, choose **Clusters** \> **Node** to verify that the current number of Worker nodes is changed to 4.

