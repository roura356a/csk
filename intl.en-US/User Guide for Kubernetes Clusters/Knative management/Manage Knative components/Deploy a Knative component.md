# Deploy a Knative component

If you have skipped a component when you deploy Knative components in batches, you can go to the **Components** page, find the component, and deploy it. Container Service for Kubernetes \(ACK\) allows you to deploy core Knative components and add-ons. This topic describes how to deploy a Knative component in a Container Service for Kubernetes \(ACK\) cluster.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Deploy Knative](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Deploy Knative.md)

If you have skipped a component when you deploy Knative components in batches, perform the following steps to deploy the component:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane of the details page, choose **Applications** \> **Knative**.

4.  Find the Knative component that displays **Not Deployed** in the **Status** column and click **Deploy** in the Actions column.

    ![Deploy the component](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9955359951/p48920.png)

5.  In the Deploy Eventing message, click **OK**.


After the component is deployed, go to the Components page and verify that the state of Knative Eventing is Deployed.

![Result](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9955359951/p48922.png)

**Related topics**  


[Deploy Knative](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Deploy Knative.md)

[Upgrade Knative Serving](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Upgrade Knative Serving.md)

