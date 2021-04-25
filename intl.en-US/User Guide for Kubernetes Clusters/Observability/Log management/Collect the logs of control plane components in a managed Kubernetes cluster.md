# Collect the logs of control plane components in a managed Kubernetes cluster

Container Service for Kubernetes \(ACK\) allows you to collect the logs of control plane components in a managed Kubernetes cluster. The logs are collected to a Log Service project that belongs to your account. This topic describes how to enable the log collection feature to collect the logs of control plane components in a managed Kubernetes cluster. It also describes how to view the collected logs.

Your Alibaba Cloud account has a sufficient quota of Logstores in Log Service.

**Note:** By default, an Alibaba Cloud account can create up to 50 Logstores.

You can manage an ACK cluster in a more secure and effective way by analyzing the logs of the [control plane components](https://kubernetes.io/docs/concepts/overview/components/#control-plane-components) in the cluster. To enable the log collection feature for control plane components in a standard or professional managed Kubernetes cluster, select **Collect Logs of Control Plane Components** when you create the cluster. The logs are shipped to the specified Log Service project that belongs to your account as log streams. You are charged based on the billing rules of Log Service on a pay-as-you-go basis. For more information, see [Pay-as-you-go](/intl.en-US/Pricing/Pay-as-you-go.md).

## Enable log collection for control plane components

When you create a cluster, select **Enable** for **Log Collection for Control Plane Components** on the **Component Configurations** wizard page. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md) or [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md).

**Note:**

-   By default, **Enable** is selected when you create a professional managed Kubernetes cluster. By default, this check box is unselected when you create a standard managed Kubernetes cluster.
-   You can select an existing Log Service project in the **Log Collection for Control Plane Components** section.

![a-1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4528234161/p209385.png)

## View the logs of control plane components

After you create the managed Kubernetes cluster, you can view the logs of the control plane components by using one of the following methods:

Method 1: View the logs of control plane components in the Log Service console.

1.  Log on to the [Log Service console](https://sls.console.aliyun.com).

2.  In the Projects section, click the name of the Log Service project that is used for the cluster.

3.  On the **Log Storage** page, click the **Logstores** tab on the left side of the page and click the Logstore where the logs of control plane components are stored. You can query the logs of the following components: **kube-apiserver**, **kube-scheduler**, and **kube-controller-manager**. For more information, see [Overview of Log Service](/intl.en-US/Index and query/Log search.md).


Method 2: View the logs of control plane components in the ACK console.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  View the three control plane components.

    -   You can view the three control plane components through the **Cluster Information** menu.
        1.  On the cluster details page, click the **Cluster Resources** tab and click the URL of the Log Service project.
        2.  On the **Log Storage** page, click the **Logstores** tab on the left side of the page and click the Logstore where the logs of control plane components are stored. You can query the logs of the following components: **kube-apiserver**, **kube-scheduler**, and **kube-controller-manager**. For more information, see [Overview of Log Service](/intl.en-US/Index and query/Log search.md).
    -   You can also view the three control plane components through the **Operations** menu.
        1.  In the left-side navigation pane of the cluster details page, choose **Operations** \> **Log Center**.
        2.  Click the **Logs of Control Plane Components** tab. You can select a component and view its logs.

## Logstores for control plane components

ACK allows you to collect the logs of the following control plane components. The logs of each component are stored in a separate Logstore. For more information about the components, see [Kubernetes components](https://kubernetes.io/docs/concepts/overview/components/).

|Component|Logstore|Description|
|---------|--------|-----------|
|kube-apiserver|apiserver|kube-apiserver is a component of the Kubernetes control plane that exposes the Kubernetes API. For more information, see [kube-apiserver](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-apiserver/).|
|kube-controller-manager|kcm|kube-controller-manager is the control center of a Kubernetes cluster and runs controller processes. For more information, see [kube-controller-manager](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-controller-manager/).|
|kube-scheduler|scheduler|kube-scheduler is the default scheduler of a Kubernetes cluster. For more information, see [kube-scheduler](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-scheduler/).|

**Related topics**  


[Pay-as-you-go](/intl.en-US/Pricing/Pay-as-you-go.md)

[Log search](/intl.en-US/Index and query/Log search.md)

[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)

[Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md)

