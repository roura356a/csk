# Collect log data of control plane components from a managed Kubernetes cluster

Container Service for Kubernetes \(ACK\) allows you to collect log data of control plane components from a managed Kubernetes cluster. The collected log data is delivered to the Log Service project that is created for ACK. This topic describes how to enable the log collection feature to collect log data of control plane components from a managed Kubernetes cluster and how to view the collected data.

Your Alibaba Cloud account has a sufficient quota of Log Service Logstores.

**Note:**

-   By default, each Alibaba Cloud account can create up to 50 Logstores.
-   Each cluster created after the log collection feature is enabled requires two Logstores.

You can gain insights into a managed cluster by analyzing the log data of the [control plane components](https://kubernetes.io/docs/concepts/overview/components/#control-plane-components). To enable the log collection feature for a standard or professional managed Kubernetes cluster, select **Collect Logs of Control Plane Components** when you create the cluster. The log data of the cluster is delivered to the specified Log Service project as log streams. You are charged based on the billing rules of Log Service on a pay-as-you-go basis. For more information, see [Pay-as-you-go](/intl.en-US/Pricing/Pay-as-you-go.md).

## Enable log collection

When you create a cluster, select **Enable** for **Log Collection for Control Plane Components** on the **Component Configurations** wizard page. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md) or [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Introduction/Create a professional managed Kubernetes cluster.md).

**Note:** **Enable** is selected by default when you create a professional managed Kubernetes cluster and cleared by default when you create a standard managed Kubernetes cluster.

![a-1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4528234161/p209385.png)

## View log data of control plane components

After you create a managed Kubernetes cluster, you can view the log data of the control plane components through one of the following methods:

View log data of control plane components in the Log Service console.

1.  Log on to the [Log Service console](https://sls.console.aliyun.com).

2.  In the Projects section, click the name of the project created for the cluster.

3.  On the **Log Storage** page, click the **Logstores** tab and click the Logstore where the log data of the control plane component is stored. You can query log data of the following components: **kube-apiserver**, **kube-scheduler**, and **kube-controller-manager**. For more information, see [Overview of Log Service](/intl.en-US/Index and query/Overview.md).


View log data of control plane components in the ACK console.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the cluster details page, click the **Cluster Resources** tab. On this tab, find and click the URL of the Log Service project.

5.  On the **Log Storage** page to which you are redirected, click the **Logstores** tab and click the Logstore where log data of the control plane component is stored. You can query log data of the following components: **kube-apiserver**, **kube-scheduler**, and **kube-controller-manager**. For more information, see [Overview of Log Service](/intl.en-US/Index and query/Overview.md).


## Logstores for control plane components

ACK allows you to collect log data of the following control plane components. The log data of the components is stored in separate Logstores. For more information about the components, see [Kubernetes Components](https://kubernetes.io/docs/concepts/overview/components/).

|Component|Logstore|Description|
|---------|--------|-----------|
|kube-apiserver|apiserver|kube-apiserver is a component of the Kubernetes control plane that exposes the Kubernetes API. For more information, see [kube-apiserver](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-apiserver/).|
|kube-controller-manager|kcm|kube-controller-manager is the control center of a Kubernetes cluster and runs controller processes. For more information, see [kube-controller-manager](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-controller-manager/).|
|kube-scheduler|scheduler|kube-scheduler is the default scheduler for a Kubernetes cluster. For more information, see [kube-scheduler](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-scheduler/).|

**Related topics**  


[Pay-as-you-go](/intl.en-US/Pricing/Pay-as-you-go.md)

[Overview](/intl.en-US/Index and query/Overview.md)

[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)

[Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Introduction/Create a professional managed Kubernetes cluster.md)

