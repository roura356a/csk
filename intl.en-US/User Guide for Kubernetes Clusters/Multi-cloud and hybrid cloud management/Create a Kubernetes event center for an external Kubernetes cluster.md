# Create a Kubernetes event center for an external Kubernetes cluster

You can create a Kubernetes event center for an external Kubernetes cluster to record the changes to the cluster, including pod configuration changes and component errors. Kubernetes event centers collect, store, and visualize cluster events in real time. They allow you to query and analyze these events, and configure alerts. This topic describes how to create a Kubernetes event center for an external Kubernetes cluster.

An external Kubernetes cluster is registered in the Container Service for Kubernetes \(ACK\) console. For more information, see [Register an external Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Register an external Kubernetes cluster.md).

## Step 1: Deploy NPD

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the **App Catalog** page, click the **Alibaba Cloud Apps** tab. On the Alibaba Cloud Apps tab, find and click **ack-node-problem-detector**.

    In the upper-right corner of the **App Catalog** page, you can enter **ack-node-problem-detector** into the Name search box and click the search icon to perform a fuzzy match.

4.  On the **App Catalog - ack-node-problem-detector** page, click the **Parameters** tab and modify the following settings:

    |Parameter|Description|
    |---------|-----------|
    |alibaba\_cloud\_plugins|Delete **ram\_role\_check**.If the instances in your cluster do not have GPUs, delete **nvidia\_gpu\_check**. |
    |serviceaccount|Specify a service account that has administrator privileges. You can run the `kubectl -n kube-system get sa` command to query service accounts. For more information, see [Use kubectl on Cloud Shell to manage ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl on Cloud Shell to manage ACK clusters.md).|
    |env|Set **AccessKeyId**, **AccessKeySecret**, and **RegionId**.|
    |sls|enabled|To archive events in Log Service, set **enabled** to **true**.|
    |topic|Enter the name of the cluster where you want to deploy NPD.|
    |project|Specify the Log Service project that is used for your cluster.|
    |logstore|Specify an existing Logstore under the Log Service project. To use the Kubernetes event center of Log Service, set the value to **k8s-event**.|
    |internal|If a leased line connects the cluster and the virtual private cloud \(VPC\) where the cluster is deployed, set the value to **true**. Otherwise, set the value to **false**.|
    |dingtalk|enabled|To send alerts to DingTalk groups, set **enabled** to **true**.|
    |monitorkinds|Select the type of alert that you want to receive. Valid values:    -   Node
    -   Pod
To send alerts only through DingTalk, set the value to **Node**.|
    |token|Enter the token of a DingTalk chatbot. You can find the token in the URL of a DingTalk chatbot.|

5.  On the **App Catalog - ack-node-problem-detector** page, select the external cluster to deploy the NPD in the **Deploy** section and click **Create**.


## Step 2: Create a Kubernetes event center

1.  Log on to the [Log Service console](https://sls.console.aliyun.com).

2.  In the **Log Application** section, click **K8s Event Center**.

3.  In the upper-right corner of the **K8s Event Center** page, click **Add**.

4.  In the **Add Event Center** panel, set the parameters.

    -   Select **Existing Project** and select a project from the **Project** drop-down list.

        **Note:** You must set **Project** to the **Log Service project** that is used for your cluster.

    -   Select **Kubernetes Cluster** and select an existing Kubernetes cluster from the **Kubernetes Cluster** drop-down list. If you use this method to create a Kubernetes event center, a Log Service project named k8s-log-\{cluster-id\} is automatically created for the event center.
5.  Click **Next**.

    **Note:** After you create a Kubernetes event center, a Logstore named k8s-event is automatically created in the associated project. Relevant dashboards and alerts are also created for the event center.


After the configuration is complete, you can use the Kubernetes event center. For more information, see [Use a Kubernetes event center](https://help.aliyun.com/document_detail/150476.html?spm=a2c6h.12873639.0.0.53116056SIry63#title-sn5-lr5-dmk).

After the Kubernetes event center is created for the external Kubernetes cluster, you can check event overviews, viewing event details, check pod lifecycles, configure alerts, and customize queries by using the event center.

**Related topics**  


[Introduction to registered external clusters](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Introduction to registered external clusters.md)

