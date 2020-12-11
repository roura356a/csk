# Uninstall a Knative component

On the **Components** page, you can uninstall one or more deployed Knative components. Container Service for Kubernetes \(ACK\) allows you to uninstall core Knative components and add-ons. This topic describes how to uninstall Knative components. **Knative Eventing** is used as an example.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Deploy a Knative component](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Deploy Knative.md)

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane, click **Knative** \> **Components**.

4.  On the Knative Components page, find the component that you want to uninstall and click **Uninstall** in the Actions column.

5.  In the Uninstall Eventing message, click **OK**.


After the component is uninstalled, go to the Knative Components page and verify that the state of **Eventing** is Not Deployed.

![Result](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2065359951/p48925.png)

