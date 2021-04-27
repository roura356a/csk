---
keyword: [Application Center, deploy an application to multiple clusters, clusters in different regions]
---

# Deploy and manage an application in clusters across regions

If you have created multiple Container Service for Kubernetes \(ACK\) clusters across regions with your Alibaba Cloud account and the clusters use different environment variables, you can use Application Center to deploy an application to these clusters at the same time. This topic describes how to deploy an application to two ACK clusters that are deployed in different regions. The application is deployed by using two different Docker images.

An ACK cluster with Internet access is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

## Step 1: Install Application Center

Deploy the Application Center controller in the primary cluster. The cluster named `ack-hangzhou` is the primary cluster where the Application Center controller is deployed in this example. A cluster named `ack-beijing` is added to Application Center as an external cluster. Then, you can manage both clusters in Application Center.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Multi-cluster** \> **Application Center**.

3.  On the Applications page, select `ack-hangzhou` on the **Install Controller** wizard page and click **Install**.

4.  On the **Add Clusters** wizard page, find the cluster `ack-beijing` and click **Add** to add the cluster to Application Center.

5.  Click **Get Started** to go to the Applications page.


## Step 2: Create an application

In this example, the application [demo-multicluster](https://github.com/AliyunContainerService/appcenter-samples/tree/master/examples/demo-multicluster) is deployed in the `ack-hangzhou` and `ack-beijing` clusters that are in different regions. The application is deployed by using two different Docker images.

1.  In the upper-right corner of the **Applications** page, click **+ NEW APP** and set the following parameters to create an application by using a Helm chart:

    -   General settings
        -   Application Name: demo-multicluster is entered in this example.
        -   SYNC POLICY: Manual is selected in this example.
    -   Source settings
        -   Data source: GIT is selected in this example.
        -   Repository URL: The repository URL is set to https://github.com/AliyunContainerService/appcenter-samples.git in this example.
        -   Revision: The master branch is used in this example.
        -   Path: The path is set to examples/demo-multicluster in this example.
    -   Destination settings
        -   Cluster: The cluster ack-hangzhou and the default namespace are used in this example.
        -   Cluster: The cluster ack-beijing and the default namespace are used in this example.
    -   Helm: The default file values.yaml is specified in this example.
2.  After you set the parameters, click **CREATE**.

3.  After you perform the preceding steps, two applications named **demo-cluster-0** and **demo-cluster-1** are displayed on the **Applications** page. Both of them belong to the same application group: **demo-multicluster**.


## Step 3: Configure the applications based on their regions

Deploy the application **demo-cluster-0** in the cluster **ack-hangzhou**. Use the file values-hangzhou.yaml to set parameters. The `Docker image` used by the application is `registry-vpc.cn-hangzhou.aliyuncs.com/acs/rollouts-demo:blu`.

1.  On the **Applications** page, click the **demo-cluster-0** card.

2.  Click **APP DETAILS**. In the panel that appears, click the **PARAMETERS** tab and click **EDIT**.

    ![R1-ch](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3091259161/p207280.png)

3.  Set **VALUES FILES** to **values-hangzhou.yaml** and click **SAVE**.


Deploy the application **demo-cluster-1** in the cluster **ack-beijing**. Use the values-beijing.yaml file to set parameters. The `Docker image` used by the application is `registry-vpc.cn-beijing.aliyuncs.com/acs/rollouts-demo:green`.

1.  On the **Applications** page, click the **demo-cluster-1** card.

2.  Click **APP DETAILS**. In the panel that appears, click the **PARAMETERS** tab and click **EDIT**.

3.  Set **VALUES FILES** to **values-beijing.yaml** and click **SAVE**.


## Step 4: Deploy the applications at a time

1.  In the upper-right corner of the **Applications** page, click **SYNC APPS**

2.  Select **APPLICATION SET: DEMO-MULTICLUSTER** and click **SYNC**.

3.  If the **Complete** message appears, it indicates that the applications are deployed.


## View the application topology and status

-   On the **Applications** page, you can view the status of the applications that you have deployed.
-   On the **Applications** page, click the card of the application that you want to view to go to the application details page. Then, you can view the topology and status of the application. The following figure shows the details page of the application **demo-multicluster-1**.

    ![R-2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3091259161/p207310.png)


## Access the applications

An NGINX Ingress is used to expose the application demo-multicluster in this example.

-   Access the domain name of the application that is deployed in the cluster **ack-hangzhou**, as shown in the following figure.

    ![s-3](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3091259161/p208022.png)

-   Access the domain name of the application that is deployed in the cluster **ack-beijing**, as shown in the following figure.

    ![s-4](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3091259161/p208040.png)


