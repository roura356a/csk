# Deploy Knative Eventing

Knative provides the Knative Eventing component to build an event-driven framework for serverless applications. Knative Eventing has designed an all-in-one eventing system for event-driven serverless applications. Knative Eventing provides event management capabilities, such as producing and consuming events. This topic describes how to deploy Knative Eventing in a serverless Kubernetes \(ASK\) cluster.

-   [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md)
-   [Deploy Knative in an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Knative management/Manage Knative components/One-click Deploy Knative in an ASK cluster.md)

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Applications** \> **Knative**.

5.  In the **Core Components** section on the **Components** tab, find Eventing and click **Deploy** in the **Actions** column.

6.  In the **Deploy Eventing** dialog box, the **PrivateZone is not enabled for the cluster. Please enable PrivateZone.** note appears. Perform the following steps to enable PrivateZone.

    **Note:** If the **PrivateZone is not enabled for the cluster. Please enable PrivateZone.** note is not displayed in the **Deploy Eventing** dialog box, you can directly go to Step [7](#step_msv_ok7_xll).

    1.  Run the following command to edit the eci-profile file.

        ```
        kubectl -n kube-system edit configmap eci-profile
        ```

    2.  Set `privatezone` to `true`. Save the change and close the eci-profile file.

        ```
        apiVersion: v1
        data:
          ...
          privatezone: "true"
          ...
        kind: ConfigMap
        metadata:
          name: eci-profile
          namespace: kube-system
        ```

7.  In the **Deploy Eventing** dialog box, click **OK**.


## Deployment result

After Knative Eventing is deployed, the component is in the **Deployed** state on the **Components** tab.

![eventing](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4926524161/p207749.png)

