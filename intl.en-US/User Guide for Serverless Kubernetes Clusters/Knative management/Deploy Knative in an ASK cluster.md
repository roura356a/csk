# Deploy Knative in an ASK cluster

Knative is a Kubernetes-based serverless framework. It aims to develop a cloud-native and cross-platform orchestration standard for serverless applications. This topic describes how to deploy Knative in a serverless Kubernetes \(ASK\) cluster.

Knative 0.13.0 and later can be deployed in clusters of Kubernetes 1.15 and later. Knative can be deployed in the following types of Kubernetes clusters: dedicated clusters, managed clusters, and serverless clusters. You can deploy Knative when you create an ASK cluster or on the Add-ons page of an existing ASK cluster. You can deploy only Knative Serving in ASK clusters.

## Deploy Knative when you create an ASK cluster

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Serverless Clusters**.

3.  On the Clusters page, click **Create Serverless Kubernetes Cluster** in the upper-right corner of the page.

4.  Configure parameters of the ASK cluster. The following table describes important parameters. For information about how to configure other parameters, see [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).

    |Parameter|Description|
    |---------|-----------|
    |Kubernetes Version|Select 1.15 or later.|
    |NAT Gateway|Select whether to automatically create a NAT gateway and configure SNAT rules for the VPC where the ASK cluster resides. This parameter is required only if you set **VPC** to **Create VPC**.    -   If you set VPC to Create VPC, you can select whether to automatically configure SNAT rules for the VPC.
    -   If you select not to automatically create a NAT gateway, you must manually perform these operations. This ensures secure access from instances in the VPC to the Internet. |
    |Public Access|Select or clear **Expose API Server with EIP**.    -   If you select this check box, an elastic IP address \(EIP\) is created, and port 6443 used by API Server is opened on master nodes. You can connect to and manage the cluster by using kubeconfig over the Internet.
    -   If you clear this check box, no EIP is created. You can only connect to and manage the cluster by using kubeconfig within the VPC. |
    |Knative|Select **Enable Knative** to deploy Knative in the ASK cluster.|

5.  Click **Create Cluster** in the upper-right corner of the page. In the Confirm dialog box, click **OK** to start the deployment.


## Deploy Knative on the Add-ons page

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Serverless Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column.

4.  In the left-side navigation pane, choose **Operations** \> **Add-ons**.

5.  On the page that appears, find the Knative component and click **Install**.

6.  In the **Confirm** dialog box, click **Confirm**.

    **Note:** You cannot uninstall the Knative component on the Add-ons page.


## Verify the result

Check the Knative status on the Add-ons page after you install Knative.

