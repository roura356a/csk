# Uninstall a Knative component {#concept_525886 .concept}

This topic describes how to uninstall a Knative component. In this topic, an example component **Eventing** is uninstalled.

## Prerequisites {#section_klu_a1g_mjo .section}

-   A Kubernetes cluster is created with ACK. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   Knative is deployed on the Kubernetes cluster. For more information, see [Deploy Knative on a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Knative management/Deploy Knative on a Kubernetes cluster.md#).

## Procedure {#section_zag_dev_oq9 .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
2.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
3.  In the left-side navigation pane under Container Service-Kubernetes, choose **Knative** \> **Components**.
4.  Find the target component. Then, in the **Actions** column, click **Uninstall**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/474493/156143089248923_en-US.png)

5.  In the displayed dialog box, click **Confirm**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/474493/156143089348924_en-US.png)


## Verify the result {#section_tz4_npu_1cp .section}

On the Knative Components page, verify that the status of the target component is Not deployed.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/474493/156143089348925_en-US.png)

