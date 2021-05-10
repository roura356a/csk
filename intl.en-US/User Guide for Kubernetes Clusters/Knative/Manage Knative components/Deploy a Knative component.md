---
keyword: [Knative components, deploy]
---

# Deploy a Knative component

If you have skipped a component when you deploy Knative, you can go to the **Components** tab to install the skipped component. Container Service for Kubernetes \(ACK\) allows you to deploy core Knative components and add-ons. This topic describes how to deploy a Knative component in a Container Service for Kubernetes \(ACK\) cluster.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Deploy Knative](/intl.en-US/User Guide for Kubernetes Clusters/Knative/Manage Knative components/Deploy Knative.md)

If you have skipped components when you deploy Knative, perform the following steps to deploy these components:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Applications** \> **Knative**.

5.  Find the Knative components that display **Not Deployed** in the **Status** column and click **Deploy** in the Actions column.

    ![Deploy the component](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9955359951/p48920.png)

6.  In the message that appears, click **Confirm**.


After the components are deployed, go to the Components tab and verify that these components are in the Deployed state.

![Deployment result](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9955359951/p48922.png)

**Related topics**  


[Deploy Knative](/intl.en-US/User Guide for Kubernetes Clusters/Knative/Manage Knative components/Deploy Knative.md)

[Upgrade a Knative component](/intl.en-US/User Guide for Kubernetes Clusters/Knative/Manage Knative components/Upgrade a Knative component.md)

