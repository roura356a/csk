# Deploy Knative in an ASK cluster

Knative is a Kubernetes-based serverless framework. The main objective of Knative is to develop a cloud-native and cross-platform orchestration standard for serverless applications. This topic describes how to deploy Knative in serverless Kubernetes \(ASK\) clusters.

Knative can be deployed in the following types of Container Service for Kubernetes \(ACK\) clusters: dedicated Kubernetes clusters, managed Kubernetes clusters, and ASK clusters. You can deploy only Knative Serving in ASK clusters.

## Deploy Knative when you create an ASK cluster

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  In the upper-right corner of the Clusters page, click **Create Kubernetes Cluster**.

4.  On the **Serverless Kubernetes** tab, configure the parameters. The following table lists the key parameters. For information about how to configure other parameters, see [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).

    |Parameter|Description|
    |---------|-----------|
    |Kubernetes Version|Select 1.15 or later.|
    |NAT Gateway|Select whether to automatically create a NAT gateway and configure SNAT rules for the virtual private cloud \(VPC\) where the ASK cluster is deployed. This parameter is required only when you set **VPC** to **Create VPC**.    -   If you set VPC to Create VPC, you can select whether to automatically create a NAT gateway and configure SNAT rules for the VPC.
    -   If you clear this check box, you must manually create a NAT gateway and configure SNAT rules for the VPC. Otherwise, the cluster in the VPC cannot access the Internet. |
    |Public Access|Enable or disable **Expose API Server with EIP**.    -   If you select this check box, an elastic IP address \(EIP\) is created and port 6443 used by the API server is exposed on master nodes. You can connect to and manage the cluster by using kubeconfig over the Internet.
    -   If you clear this check box, no EIP is created. You can connect to and manage the cluster by using kubeconfig only within the VPC. |
    |Knative|Select **Enable Knative** to deploy Knative in the ASK cluster.![2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6757867061/p170979.png) |

5.  Click **Create Cluster** in the upper-right corner of the page. In the Confirm message, click **OK** to start the deployment.


## Deploy Knative in the ASK cluster

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column.

4.  In the left-side navigation pane of the details page, choose **Applications** \> **Knative**.

5.  On the Knative Components page, click **Deploy Knative**.

6.  After you select the Knative components that you want to install, click **Deploy**.

    You can use Knative Serving to manage serverless applications. Pods can be automatically scaled for serverless applications upon Knative events and user requests. If no workload is processed, the number of pods is scaled to zero.


## Check the result

After Knative is deployed, you can go to the Knative Components page and verify that the state of Knative is **Installed**.

