---
keyword: [open source monitoring tool, Prometheus]
---

# Deploy Prometheus to a Kubernetes cluster

Prometheus is an open source monitoring tool for cloud-native applications. This topic describes how to deploy Prometheus to a Container Service for Kubernetes \(ACK\) cluster.

-   A Kubernetes cluster is created. For more information, see [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md).
-   The cluster is connected. Make sure that you can view node information such as tags. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

A monitoring system monitors the following two types of objects:

-   Resources: resource usage of nodes and applications. In a Kubernetes cluster, a monitoring system monitors the resource usage of nodes, pods, and the cluster.
-   Applications: internal metrics of applications. For example, the monitoring system counts the number of online users that are using an application in real time, and enables ports of the application to monitor services and generate alerts.

In a Kubernetes cluster, a monitoring system monitors the following objects:

-   Cluster components, such as kube-apiserver, kube-controller-manager, and etcd.
-   Static resource entities, such as node resource statuses and kernel events.
-   Dynamic resource entities, which are the entities of abstract workloads in Kubernetes, such as deployments, DaemonSets, and pods.
-   Custom objects in applications, which include the custom data and metrics in applications.

To monitor cluster components and static resource entities, specify monitoring methods in configuration files.

To monitor dynamic resource entities, you can deploy Prometheus to a Kubernetes cluster.

## Procedure

1.  Deploy Prometheus.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**. On the page that appears, click **ack-prometheus-operator**.

    3.  In the **Deploy** section of the App Catalog - ack-prometheus-operator page, select the cluster that you want to manage, and click **Create** to deploy Prometheus.

        ![Prometheus-operator](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6155359951/p52531.png)

        Verify the deployment result.

        1.  Run the following command to map Prometheus in the cluster to local port 9090:

            ```
            kubectl port-forward svc/ack-prometheus-operator-prometheus 9090:9090 -n monitoring
            ```

        2.  Enter localhost:9090 in the address bar of a browser to visit the Prometheus page.
        3.  In the top navigation bar, choose **Status** \> **Targets** to view all data collection tasks.

            ![Data collection tasks](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6155359951/p21082.png)

            Tasks in the **UP** state are running properly.

            ![Targets](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6155359951/p21084.png)

2.  View the aggregate data.

    1.  Run the following command to map Grafana in the cluster to local port 3000:

        ```
        kubectl -n monitoring port-forward svc/ack-prometheus-operator-grafana 3000:80
        ```

    2.  To view aggregate data, enter localhost:3000 in the address bar of a browser, and select a dashboard.

        ![Dashboard](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6155359951/p21092.png)

3.  View alert rules and set silent alerts.

    -   View alert rules

        To view the alert rules, enter `localhost:9090` in the address bar of a browser, and click **Alerts** in the top navigation bar.

        -   If an alert rule is in red, alerts are triggered based on this rule.
        -   If an alert rule is in green, no alerts are triggered based on this rule.
        ![Alerts](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6155359951/p21097.png)

    -   Set silent alerts

        Run the following command. Enter `localhost:9093` in the address bar of a browser, and click **Silence** to set silent alerts.

        ```
        kubectl --namespace monitoring port-forward svc/alertmanager-operated 9093
        ```

        ![Silence](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6155359951/p21100.png)


You can take the preceding steps to deploy Prometheus. The following examples describe how to configure Prometheus in different scenarios.

## Alert configuration

To set alert notification methods or notification templates, perform the following steps to configure the config element in the alertmanager module.

-   Set alert notification methods

    **Prometheus** that is deployed to a Kubernetes cluster can send notifications by DingTalk and email. You can perform the following steps to set alert notification methods.

    -   Configure DingTalk notifications

        On the App Catalog - ack-prometheus-operator page, click the **Parameters** tab. In the dingtalk module, set enabled to true. In the token element of the module, enter webhook URLs of your DingTalk chatbots. In the alertmanager module, set the receiver parameter in the config element to the DingTalk alert name in the receivers element. By default, the value of the receiver parameter is webhook1.

        For example, if you have two DingTalk chatbots, perform the following steps:

        1.  Replace the parameter values in the token element with webhook URLs of DingTalk chatbots.

            Copy the webhook URLs of your DingTalk chatbots and use the copied URLs to replace the parameter values of dingtalk1 and dingtalk2 in the token element. In the following example, use the webhook URLs to replace https://oapi.dingtalk.com/robot/send?access\_token=xxxxxxxxxx.

            ![Token configuration](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6155359951/p56907.png)

        2.  Modify the value of the receiver parameter.

            In the alertmanager module, find the receiver parameter in the config element, and set the parameter to the DingTalk alert name in the receivers element. In this example, the parameter values are webhook1 and webhook2.

        3.  Modify the url parameter.

            The following example is based on chatbots dingtalk1 and dingtalk2. Modify the url parameter based on the actual chatbot names.

            ![Webhook configuration](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6155359951/p56908.png)

        **Note:** You can add the configurations of more DingTalk chatbots in the receivers element.

    -   Configure email notifications

        On the App Catalog - ack-prometheus-operator page, click the **Parameters** tab. Set the parameters in the second red box of the following figure. In the alertmanager module, set the receiver parameter in the config element to the email alert name in the receivers element. By default, the email alert name is mail.

        ![Email notifications](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7155359951/p54783.png)

-   Set alert notification templates

    In the templateFiles element of the alertmanager module on the Parameters tab, you can customize alert notification templates.

    ![alert](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/9330854061/p179686.png)


## Storage configuration

Monitoring data that is generated by Prometheus can be stored in Time Series Database \(TSDB\) or on disks. You can perform the following steps to configure data storage.

-   Store data in TSDB

    On the App Catalog - ack-prometheus-operator page, click the **Parameters** tab. In the tsdb module, set enabled to true. Then, set the url parameter for remoteRead and remoteWrite. .

    ![TSDB](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/9330854061/p179688.png)

-   Store data on disks

    By default, data collected by Prometheus in Kubernetes is stored on disks. You can configure disk storage in the prometheus or alertmanager module on the Parameters tab. On the App Catalog - ack-prometheus-operator page, click the **Parameters** tab. Find the storage element in the alertmanager module or the storageSpec element in the prometheus module, and set the parameters. storageClassName specifies the disk category, accessModes specifies the access mode, and storage specifies the storage capacity.

    ![prometheus](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/9330854061/p179689.png)

    **Note:** Assume that you want to configure an SSD to store data. In the storageSpec element, set `storageClassName` to alicloud-disk-ssd, `accessModes` to ReadWriteOnce, and `storage` to 50Gi.

    ![ssd](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/9330854061/p179690.png)

    To verify the configuration, you can choose **Storage & Snapshots** \> **Disks** in the Elastic Compute Service \(ECS\) console.

    For information about how to reuse a disk, see [Use Alibaba Cloud disks as volumes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Disk volumes/Use Alibaba Cloud disks as volumes.md).


## Enable the Prometheus adapter for auto scaling

To enable the Prometheus adapter, you can set enabled to true in the prometheusAdapter module and customize metrics. In this case, the Kubernetes cluster can automatically scale the number of pods based on the custom metrics. This improves resource usage.

You can perform the following steps to enable the Prometheus adapter. On the App Catalog - ack-prometheus-operator page, click the **Parameters** tab. In the prometheusAdapter module, set enabled to true.

![HPA](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/9330854061/p179691.png)

You can run the following command to verify the configuration. For information about how to customize metrics, see [Prometheus adapter](https://github.com/DirectXMan12/k8s-prometheus-adapter/blob/master/docs/config-walkthrough.md).

```
kubectl get --raw "/apis/custom.metrics.k8s.io/v1beta1"
```

## Mount a custom ConfigMap to Prometheus

This topic describes how to mount a ConfigMap to the /etc/prometheus/configmaps/ path of a pod.

If you deploy Prometheus for the first time, perform the following steps:

On the App Catalog - ack-prometheus-operator page, click the **Parameters** tab. In the configMaps element of the prometheus module, enter the name of the custom ConfigMap.

If Prometheus has been deployed to the cluster, perform the following steps:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Releases**.

5.  On the **Helm** tab, find the application you want to update, and click **Update** in the Actions column. The Update Release dialog box appears.

6.  In the configMaps element of the prometheus and alertmanager modules, enter the name of the custom ConfigMap and click **Update**.

    ![config](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0430854061/p179693.png)

    **Note:** Assume that you want to mount a custom ConfigMap named `special-config` and this ConfigMap contains the configuration file of Prometheus. To use the configuration file as the value of the --config.file parameter when the Prometheus pod starts, enter special-config in the configMaps element of the prometheus module. After you perform this operation, the ConfigMap is mounted to the Prometheus pod in the /etc/prometheus/configmaps/ path.

    The following figure shows an example of the YAML file for special-config.

    ![config yaml](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0430854061/p179696.png)

    The following figure shows how to set the configMaps element in the prometheus module.

    ![configmap](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0430854061/p179697.png)


## Grafana configuration

-   Mount a dashboard to Grafana

    To mount a dashboard as a ConfigMap to a Grafana pod, perform the following steps. On the App Catalog - ack-prometheus-operator page, click the **Parameters** tab. Set the parameters in the `extraConfigmapMounts` element, as shown in the following figure.

    ![operator](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0430854061/p179717.png)

    **Note:**

    -   Make sure that a dashboard is stored as a ConfigMap in the Kubernetes cluster.

        This ConfigMap must have a label that is also attached to other ConfigMaps.

    -   In the `extraConfigmapMounts` element of the grafana module, set the ConfigMap name and mount path.
    -   Set mountPath to /tmp/dashboards/.
    -   Set configMap to the name of your custom ConfigMap.
    -   Set name to the name of the JSON file that stores the dashboard information.
-   Enable persistence of dashboards

    You can perform the following steps to enable persistence of Grafana dashboards:

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).
    2.  In the left-side navigation pane, click **Clusters**.
    3.  On the Clusters page, click the name of the cluster that you want to manage or click **Applications** in the **Actions** column for the cluster.
    4.  In the left-side navigation pane, click **Releases**.
    5.  Find ack-prometheus-operator, and click **Update** in the Actions column. The **Update Release** dialog box appears.
    6.  In the Update Release dialog box, find the persistence element in the grafana module, and set the parameters, as shown in the following figure.
    ![Enable persistence of Grafana dashboards](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8155359951/p103389.png)

    You can export Grafana dashboards in the JSON format. For more information, see [Export a Grafana dashboard](https://grafana.com/docs/reference/export_import/#export-and-import).


## FAQ

-   What do I do if I fail to receive DingTalk alert notifications?
    1.  Obtain the webhook URL of your DingTalk chatbot. For more information, see [Scenario 1: Use DingTalk to raise alerts upon Kubernetes events](/intl.en-US/User Guide for Kubernetes Clusters/Monitoring management/Monitor events.md).
    2.  On the Parameters tab, find the dingtalk module, set enabled to true, and enter the webhook URL of your DingTalk chatbot in the token element. For more information, see **Configure DingTalk alert notifications** in [Alert configuration](#section_6r6_hsk_wc0).
-   What do I do if the following error message appears when I deploy Prometheus to a cluster?

    The following code shows the error message:

    ```
    Can't install release with errors: rpc error: code = Unknown desc = object is being deleted: customresourcedefinitions.apiextensions.k8s.io "xxxxxxxx.monitoring.coreos.com" already exists
    ```

    The error message shows that the cluster fails to clear custom resource definitions \(CRDs\) of the previous deployment. Run the following commands to delete CRDs and then deploy Prometheus again:

    ```
    kubectl delete crd prometheuses.monitoring.coreos.com
    kubectl delete crd prometheusrules.monitoring.coreos.com
    kubectl delete crd servicemonitors.monitoring.coreos.com
    kubectl delete crd alertmanagers.monitoring.coreos.com
    ```

-   What do I do if I fail to receive email alert notifications?

    Make sure that the value of `smtp_auth_password` is the SMTP authorization code instead of the logon password of the email account. Make sure that the STMP server endpoint includes a port number.

-   What do I do if the following error message appears when I update a YAML file?

    If the configuration file of Tiller is overlarge, the cluster cannot be accessed. To solve this issue, you can delete some annotations in the configuration file and mount the file to a pod as a ConfigMap. You can enter the name of the custom ConfigMap in the configMaps element of the prometheus and alertmanager modules. For more information, see the second method in [Mount a custom ConfigMap to Prometheus](#section_20m_m2b_ki9).

-   How do I enable the features of Prometheus after I deploy it to a cluster?

    On the management page of the cluster where Prometheus is deployed, click **Releases** in the left-side navigation pane, and click the **Helm** tab. Find **ack-prometheus-operator**, and click **Update** in the Actions column. In the Update Release dialog box, set enabled to true for the features that you want to enable, and click **Update**.

-   How do I select data storage: TSDB or disks?

    TSDB storage is available to restricted regions. However, disk storage is supported in all regions. The following figure shows the data retention policy.

    ![disk](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0430854061/p179724.png)

-   What do I do if a Grafana dashboard fails to display data?

    On the management page of the cluster, click **Releases** in the left-side navigation pane, and click the **Helm** tab. Find **ack-prometheus-operator**, and click **Update** in the Actions column. In the Update Release dialog box, find **clusterVersion** and check whether the value of clusterVersion is correct. If the cluster version is earlier than V1.16, set clusterVersion to 1.14.8-aliyun.1. If the cluster version is V1.16 or later, set clusterVersion to 1.16.6-aliyun.1.


