# Enable NPD in an external cluster

node-problem-detector \(NPD\) provides a unified approach to manage Kubernetes clusters that are deployed across regions. This topic describes how to enable NPD on an application that runs in an external Kubernetes cluster.

An external Kubernetes cluster is registered in the Container Service for Kubernetes \(ACK\) console. For more information, see [Register an external Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Register an external Kubernetes cluster.md).

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**.

3.  On the **App Catalog** page, click the **Alibaba Cloud Apps** tab. On the Alibaba Cloud Apps tab, find and click **ack-node-problem-detector**.

    In the upper-right corner of the **App Catalog** page, you can enter **ack-node-problem-detector** into the Name search bar and click the search icon.

4.  On the **App Catalog - ack-node-problem-detector** page, select an external cluster to deploy the application in the **Deploy** section.

5.  On the **App Catalog - ack-node-problem-detector** page, click the **Parameters** tab, set the parameters, and click **Create** in the **Deploy** section.

    |Parameter|Description|
    |---------|-----------|
    |alibaba\_cloud\_plugins|Delete **ram\_role\_check**.If the instance does not have GPUs, delete **nvidia\_gpu\_check**. |
    |serviceaccount|Specify a service account that has administrator privileges. You can run `kubectl -n kube-system get sa` to query service accounts. For more information, see [Use kubectl on Cloud Shell to manage ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl on Cloud Shell to manage ACK clusters.md).|
    |env|Set the **AccessKeyId**, **AccessKeySecret**, and **RegionId** parameters.|
    |sls|enabled|To archive events in Log Service, set **enabled** to **true**.|
    |topic|Enter the name of the cluster.|
    |project|Specify the Log Service project that corresponds to your cluster.|
    |logstore|Specify a Logstore under the Log Service project. To use the event center feature in Log Service, set the value to **k8s-event**.|
    |internal|If a leased line connects the cluster and the virtual private cloud \(VPC\) where the cluster is deployed, set the value to **true**. Otherwise, set the value to **false**.|
    |dingtalk|enabled|To send alerts to DingTalk groups, set **enabled** to **true**.|
    |monitorkinds|Select the type of alert that you want to receive. Valid values:    -   Node
    -   Pod
To send alerts only through DingTalk, set the value to **Node**.|
    |token|Enter the token of a DingTalk chatbot. You can find the token in the URL of a DingTalk chatbot.|


The following figure shows an example of DingTalk alerts:

![cluster_NPD_03](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5829404061/p86372.png)

**Related topics**  


[Overview of registered clusters in the ACK console](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Overview of registered clusters in the ACK console.md)

