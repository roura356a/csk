---
keyword: [upgrade a component, install a component, uninstall a component, K8s]
---

# Manage system components

This topic describes how to upgrade, install, and uninstall system components of a Container Service for Kubernetes \(ACK\) cluster.

[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md) or [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md)

In most cases, the Kubernetes version of your cluster is up-to-date. However, you may still need to separately upgrade specific system components. This topic describes how to upgrade system components.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and choose **More** \> **Manage System Components** in the **Actions** column.

4.  On the **Add-ons** page, you can perform the following operations:

    -   Find the component that you want to install and click **Install**.
    -   Find the component that you want to uninstall and click **Uninstall**.
    -   Find the component that you want to upgrade and click **Upgrade**.
    -   Find the control plane component that you want to modify and click **Configuration**.

        **Note:** You can customize the parameter settings of managed components, such as Kube API Server and Kube Controller Manager \(KCM\), only in professional Kubernetes clusters. For more information, see [Customize the settings of control plane components in professional managed Kubernetes clusters](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Customize the settings of control plane components in professional managed Kubernetes clusters.md).


