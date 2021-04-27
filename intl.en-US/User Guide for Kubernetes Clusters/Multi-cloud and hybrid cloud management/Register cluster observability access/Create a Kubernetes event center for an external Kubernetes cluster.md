# Create a Kubernetes event center for an external Kubernetes cluster

You can create a Kubernetes event center to record events of an external Kubernetes cluster. These events include changes to pod configurations and component anomalies. The Kubernetes event center collects, stores, and visualizes cluster events in real time. The event center allows you to query and analyze the events and configure alerts. This topic describes how to create a Kubernetes event center for an external Kubernetes cluster.

You have connected to an external Kubernetes cluster through Container Service for Kubernetes. For more information, see [Create a cluster registration proxy and register an on-premises cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of registered clusters/Create a cluster registration proxy and register an on-premises cluster.md).

## Scenarios

For more information about scenarios in which the Kubernetes event center is used, see [Event monitoring](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Event monitoring.md).

## Step 1: Deploy node-problem-detector

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the **App Catalog** page, click the **Alibaba Cloud Apps** tab. On the Alibaba Cloud Apps tab, find and click **ack-node-problem-detector**.

    In the upper-left corner of the **App Catalog** page, you can enter **ack-node-problem-detector** into the Name search box and click the search icon to perform a fuzzy match.

4.  On the **App Catalog - ack-node-problem-detector** page, click the **Parameters** tab and modify the following settings.

    |Parameter|Description|
    |---------|-----------|
    |alibaba\_cloud\_plugins|Delete **ram\_role\_check**. If the nodes of the external Kubernetes cluster are not equipped with GPUs, delete **nvidia\_gpu\_check**. |
    |serviceaccount|Specify a service account that has administrator privileges. You can run the `kubectl -n kube-system get sa` command to query service accounts. For more information, see [Use kubectl on Cloud Shell to manage ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl on Cloud Shell to manage ACK clusters.md).|
    |env|Set the **AccessKeyId**, **AccessKeySecret**, and **RegionId** parameters.|
    |sls|enabled|To archive events in Log Service, set **enabled** to **true**.|
    |topic|Enter the name of the cluster in which you want to deploy NPD.|
    |project|Specify the Log Service project that is used for the cluster.|
    |logstore|Specify an existing Logstore under the Log Service project. To use the Kubernetes event center of Log Service, set the value to **k8s-event**.|
    |internal|If an Express Connect circuit is used to connect the cluster and the virtual private cloud \(VPC\) where the cluster is deployed, set the value to **true**. Otherwise, set the value to **false**.|
    |dingtalk|enabled|To send alerts to DingTalk groups, set **enabled** to **true**.|
    |monitorkinds|Select the type of alert that you want to receive. Valid values:    -   Node
    -   Pod
To send alerts to only DingTalk, set the value to **Node**.|
    |token|Enter the token of a DingTalk chatbot. You can obtain the token from the URL of a DingTalk chatbot.|
    |eventbridge|enabled|To send event alerts to EventBridge, set **enabled** to **true**.|

5.  On the **App Catalog - ack-node-problem-detector** page, select the external cluster to deploy the NPD in the **Deploy** section and click **Create**.


## Step 2: Create a Kubernetes event center

1.  Log on to the [Log Service console](https://sls.console.aliyun.com).

2.  In the **Log Application** section, click **K8s Event Center**.

3.  In the upper-right corner of the **K8s Event Center** page, click **Add**.

4.  In the **Add Event Center** panel, set the parameters.

    -   Select **Existing Project** and select a project from the **Project** drop-down list.

        **Note:** You must set **Project** to the **Log Service project** that is used for the cluster.

    -   Select **Kubernetes Cluster** and then select the Kubernetes cluster that you created from the **Kubernetes Cluster** drop-down list. If you use this method to create a Kubernetes event center, a Log Service project named k8s-log-\{cluster-id\} is automatically created for the event center.
5.  Click **Next**.

    **Note:** After you create a Kubernetes event center, a Logstore named k8s-event is automatically created in the associated project. Relevant dashboards and alerts are also created for the event center.


After the configuration is complete, you can use the Kubernetes event center. For more information, see [Use a Kubernetes event center](https://help.aliyun.com/document_detail/150476.html?spm=a2c6h.12873639.0.0.53116056SIry63#title-sn5-lr5-dmk).

After the Kubernetes event center is created for the external Kubernetes cluster, you can use the event center to check event overviews, view event details, check pod lifecycles, configure alerts, and customize queries.

**Related topics**  


[Overview of registered clusters](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of registered clusters/Overview of registered clusters.md)

