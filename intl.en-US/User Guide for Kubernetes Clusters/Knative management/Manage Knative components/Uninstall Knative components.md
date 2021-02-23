# Uninstall Knative components

On the **Components** page, you can uninstall one or more Knative components that you have deployed. Container Service for Kubernetes \(ACK\) allows you to uninstall core Knative components and add-ons. This topic describes how to uninstall Knative components. **Knative Eventing** is used as an example.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Deploy Knative](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Deploy Knative.md)

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane of the details page, choose **Applications** \> **Knative**.

4.  On the Components tab, select the component that you want to uninstall and click **Uninstall**.

5.  In the Uninstall Eventing message, click **OK**.


After the component is uninstalled, go to the Components page and verify that the state of **Eventing** is Not Deployed.

![Result](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2065359951/p48925.png)

