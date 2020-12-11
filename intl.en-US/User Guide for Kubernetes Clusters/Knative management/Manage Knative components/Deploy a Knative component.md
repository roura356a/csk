# Deploy a Knative component

If you have skipped a component when you deploy Knative components in bulk, go to the **Components** page, and find and deploy the skipped component. Alibaba Cloud Container Service for Kubernetes \(ACK\) allows you to deploy core Knative components and add-ons. This topic describes show how to deploy a Knative component by using **Knative Eventing** as an example.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Deploy a Knative component](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Deploy Knative.md)

If you have skipped a component when you deploy Knative components in bulk, perform the following steps to deploy the skipped component:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Knative** \> **Services**.

3.  Find the Knative component that is in the **Not Deployed** **state** and click **Deploy** in the Actions column.

    ![Deploy a Knative component](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9955359951/p48920.png)

4.  In the Deploy Eventing message, click **Confirm**.


After the deployment is complete, go to the Knative Components page and you can find that Knative Eventing is in the Deployed state.

![Result](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9955359951/p48922.png)

**Related topics**  


[Upgrade Knative Serving](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Upgrade Knative Serving.md)

