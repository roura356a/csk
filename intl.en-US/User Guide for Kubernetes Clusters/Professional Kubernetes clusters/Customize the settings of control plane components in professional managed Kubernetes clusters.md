---
keyword: [component parameters, professional managed Kubernetes clusters, control plane, managed components]
---

# Customize the settings of control plane components in professional managed Kubernetes clusters

You can customize the settings of control plane components in a professional managed Kubernetes cluster to meet production needs. You can customize the settings of managed components such as Kube API Server and Kube Controller Manager \(KCM\). This topic describes how to customize the settings of control plane components in professional managed Kubernetes clusters.

## Considerations

Before you customize the settings of a control plane component, take note of the following items:

-   After you customize the settings of a component, the component is automatically restarted. We recommend that you customize the settings during off-peak hours.
-   After you customize the settings, the changes overwrite the default settings of the professional managed Kubernetes cluster.
-   To ensure the stability of the control plane component, you are allowed to customize only some of the settings.
-   Make sure that the values of the customized parameters are valid and complete. Otherwise, the component may fail to be restarted. For more information about the parameters, see [kube-apiserver](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-apiserver/) and [kube-controller-manager](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-controller-manager/).

## Customize the settings of a control plane component in a professional managed Kubernetes cluster

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Add-ons**.

5.  The following example shows how to customize the settings of Kube API Server:
6.  In the **Core Components** section, find the component and click **configuration**.

7.  In the **kube-apiserver Parameters** dialog box, set the parameters and click **OK**.

    **Note:** Make sure that the specified values are valid and complete. You can customize only the settings of Kube API Server and KCM in professional managed Kubernetes clusters. For more information about the valid format and values of component parameters, see [kube-apiserver](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-apiserver/) and [kube-controller-manager](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-controller-manager/). Select the Kubernetes version based on the practical situation.


## Default settings

The default settings are overwritten after you customize values for component parameters. You can reset the parameters to the default settings in the following table as needed.

|Kubernetes version|Component|Parameter|Default value|
|------------------|---------|---------|-------------|
|1.16|kube-apiserver|ServiceNodePortRange|30000-32767|
|EnableAdmissionPlugins|-   If PodSecurityPolicy is enabled, the default value is `NodeRestriction,PodSecurityPolicy`.
-   If PodSecurityPolicy is disabled, the default value is `NodeRestriction`. |
|kube-controller-manager|HorizontalPodAutoscalerSyncPeriod|15s|
|1.18|kube-apiserver|ServiceNodePortRange|30000-32767|
|EnableAdmissionPlugins|-   If PodSecurityPolicy is enabled, the default value is `NodeRestriction,PodSecurityPolicy`.
-   If PodSecurityPolicy is disabled, the default value is `NodeRestriction`. |
|kube-controller-manager|HorizontalPodAutoscalerSyncPeriod|15s|

