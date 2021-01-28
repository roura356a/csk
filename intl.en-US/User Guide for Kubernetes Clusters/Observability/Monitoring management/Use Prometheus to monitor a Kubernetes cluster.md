---
keyword: [open source monitoring tool, Prometheus]
---

# Use Prometheus to monitor a Kubernetes cluster

Prometheus is an open source tool that can be used to monitor cloud-native applications. This topic describes how to deploy Prometheus in a Container Service for Kubernetes \(ACK\) cluster.

-   A Kubernetes cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   You are connected to the cluster. Make sure that you can view node information such as tags. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

Typically, a monitoring system monitors the following types of object:

-   Resources: resource usage of nodes and applications. In a Kubernetes cluster, the monitoring system monitors the resource usage of nodes, pods, and the cluster.
-   Applications: internal metrics of applications. For example, the monitoring system dynamically counts the number of online users who are using an application, and enables ports of the application to monitor services and generate alerts.

In a Kubernetes cluster, the monitoring system monitors the following objects:

-   Cluster components: the components of the Kubernetes cluster, such as kube-apiserver, kube-controller-manager, and etcd.
-   Static resource entities: the node resource status and kernel events.
-   Dynamic resource entities: the entities of abstract workloads in Kubernetes, such as Deployments, DaemonSets, and pods.
-   Custom objects in applications: the custom data and metrics in applications.

To monitor cluster components and static resource entities, specify the monitoring methods in the configuration files.

To monitor dynamic resource entities for a Kubernetes cluster, you can deploy Prometheus in the Kubernetes cluster.

## Procedure

1.  Deploy Prometheus.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**. On the page that appears, click **ack-prometheus-operator**.

    3.  In the **Deploy** section of the App Catalog - ack-prometheus-operator page, select the cluster that you want to manage, and click **Create** to deploy Prometheus.

        Check the deployment result.

        1.  Run the following command to map Prometheus in the cluster to local port 9090:

            ```
            kubectl port-forward svc/ack-prometheus-operator-prometheus 9090:9090 -n monitoring
            ```

        2.  Enter localhost:9090 in the address bar of a browser to visit the Prometheus page.
        3.  In the top navigation bar, choose **Status** \> **Targets** to view all data collection tasks.

            ![Data collection tasks](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6155359951/p21082.png)

            Tasks in the **UP** state are running properly.

            ![Targets](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6155359951/p21084.png)

2.  View the aggregated data.

    1.  Run the following command to map Grafana in the cluster to local port 3000:

        ```
        kubectl -n monitoring port-forward svc/ack-prometheus-operator-grafana 3000:80
        ```

    2.  To view the aggregated data, enter localhost:3000 in the address bar of a browser, and select a dashboard.

        ![Dashboard](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6155359951/p21092.png)

3.  View alert rules and set silent alerts.

    -   View alert rules

        To view the alert rules, enter `localhost:9090` in the address bar of a browser, and click **Alerts** in the top navigation bar.

        -   If an alert rule is in red, alerts are triggered based on this rule.
        -   If an alert rule is in green, no alerts are triggered based on this rule.
        ![Alerts](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6155359951/p21097.png)

    -   Set silent alerts

        Run the following command. Enter `localhost:9093` in the address bar of a browser, and click **Silence** to set silent alerts.

        ```
        kubectl --namespace monitoring port-forward svc/alertmanager-operated 9093
        ```

        ![Silence](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6155359951/p21100.png)


You can follow the preceding steps to deploy Prometheus. The following examples describe how to configure Prometheus in different scenarios.

## Alert configuration

To set alert notification methods or notification templates, perform the following steps to configure the config field in the alertmanager section:

-   Set alert notification methods

    **Prometheus** that is deployed in a Kubernetes cluster can send notifications by using DingTalk or emails. You can perform the following steps to set alert notification methods:

    -   Configure DingTalk notifications

        On the App Catalog - ack-prometheus-operator page, click the **Parameters** tab. In the dingtalk field, set enabled to true. In the token field, enter the webhook URLs of your DingTalk chatbots. In the alertmanager section, specify a DingTalk chatbot name for the receiver parameter in the config field. By default, the value of the receiver parameter is webhook.

        For example, if you have two DingTalk chatbots, perform the following steps:

        1.  Replace the parameter values in the token field with the webhook URLs of the DingTalk chatbots.

            Copy the webhook URLs of the DingTalk chatbots and replace the parameter values of dingtalk1 and dingtalk2 in the token field with the copied URLs. In this example, https://oapi.dingtalk.com/robot/send?access\_token=xxxxxxxxxx is replaced by the webhook URLs.

            ![Token configuration](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6155359951/p56907.png)

        2.  Modify the value of the receiver parameter.

            In the alertmanager section, find the receiver parameter in the config field, and specify a DingTalk chatbot name for the parameter. In this example, webhook1 and webhook2 are used.

        3.  Modify the url parameter.

            Replace the value of the url parameter with the actual chatbot names. In this example, dingtalk1 and dingtalk2 are used.

            ![Webhook configuration](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6155359951/p56908.png)

        **Note:** You can add multiple DingTalk chatbots based on your business requirements.

    -   Configure email notifications

        On the App Catalog - ack-prometheus-operator page, click the **Parameters** tab. Set the parameters in the second red box of the following figure. In the alertmanager section, set the receiver parameter in the config field to the email alert name in the receivers field. By default, the email alert name is mail.

        ![Email notifications](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7155359951/p54783.png)

-   Set alert notification templates

    In the templateFiles field of the alertmanager section on the Parameters tab, you can customize alert notification templates.

    ![Template settings](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5651867061/p56918.png)


## Storage configuration

Monitoring data that is generated by Prometheus can be stored in Time Series Database \(TSDB\) or on disks. You can perform the following steps to configure data storage:

-   Store data in TSDB

    On the App Catalog - ack-prometheus-operator page, click the **Parameters** tab. In the tsdb section, set enabled to true. Then, set the url parameter for remoteRead and remoteWrite.

    ![Store data in TSDB](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5651867061/p54784.png)

-   Store data on disks

    By default, data collected by Prometheus in Kubernetes is stored on disks. You can configure disk storage in the prometheus or alertmanager section on the Parameters tab. On the App Catalog - ack-prometheus-operator page, click the **Parameters** tab. Find the storage field in the alertmanager section or the storageSpec field in the prometheus section, and set the parameters. storageClassName specifies the disk category, accessModes specifies the access mode, and storage specifies the storage capacity.

    ![Store data on disks](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5651867061/p54800.png)

    **Note:** Assume that you want to configure an SSD to store data. In the storageSpec field, set `storageClassName` to alicloud-disk-ssd, `accessModes` to ReadWriteOnce, and `storage` to 50Gi, as shown in the following figure.

    ![Disk configuration](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5651867061/p56923.png)

    To check the configuration, you can choose **Storage & Snapshots** \> **Disks** in the Elastic Compute Service \(ECS\) console.

    For information about how to reuse a disk, see [Use Alibaba Cloud disks as volumes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Disk volumes/Use Alibaba Cloud disks as volumes.md).


## Enable the Prometheus adapter for auto scaling

To enable the Prometheus adapter, you can set enabled to true in the prometheusAdapter section and customize metrics. In this case, the Kubernetes cluster can automatically scale the number of pods based on the custom metrics. This improves resource usage.

To enable the Prometheus adapter, click the **Parameters** tab on the App Catalog - ack-prometheus-operator page. In the prometheusAdapter section, set enabled to true.

![Enable the Prometheus adapter](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5651867061/p54785.png)

You can run the following command to verify the configuration. For information about how to customize metrics, see [Prometheus adapter](https://github.com/DirectXMan12/k8s-prometheus-adapter/blob/master/docs/config-walkthrough.md).

```
kubectl get --raw "/apis/custom.metrics.k8s.io/v1beta1"
```

## Mount a custom ConfigMap to Prometheus

This topic describes how to mount a ConfigMap to the /etc/prometheus/configmaps/ path of a pod.

If this is the first time you deploy Prometheus, perform the following steps:

On the App Catalog - ack-prometheus-operator page, click the **Parameters** tab. In the configMaps field of the prometheus section, enter the name of the custom ConfigMap.

![Mount a ConfigMap](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7382381161/p56963.png)

If Prometheus has been deployed to the cluster, perform the following steps:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Releases**.

5.  On the **Helm** tab, find the application that you want to update and click **Update** in the Actions column. The Update Release dialog box appears.

6.  In the configMaps field of the prometheus and alertmanager sections, enter the name of the custom ConfigMap and click **Update**.

    ![Update the configuration](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5651867061/p56964.png)

    **Note:** Assume that you want to mount a custom ConfigMap named `special-config` and this ConfigMap contains the configuration file of Prometheus. To use the configuration file as the value of the --config.file parameter when the Prometheus pod starts, enter special-config in the configMaps field of the prometheus section. After you perform this operation, the ConfigMap is mounted to the Prometheus pod in the /etc/prometheus/configmaps/ path.

    The following figure shows an example of the YAML file for special-config.

    ![special-config](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5651867061/p57825.png)

    The following figure shows how to set the configMaps field in the prometheus section.

    ![configmaps](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5651867061/p57828.png)


## Grafana configuration

-   Mount a dashboard to Grafana

    To mount a dashboard as a ConfigMap to a Grafana pod, click the **Parameters** tab on the App Catalog - ack-prometheus-operator page. Set the parameters in the `extraConfigmapMounts` field, as shown in the following figure.

    ![Mount a dashboard to Grafana](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5651867061/p57799.png)

    **Note:**

    -   Make sure that a dashboard is stored as a ConfigMap in the Kubernetes cluster.

        This ConfigMap must have a label that is also attached to other ConfigMaps.

    -   In the `extraConfigmapMounts` field of the grafana section, set the ConfigMap name and mount path.
    -   Set mountPath to /tmp/dashboards/.
    -   Set configMap to the name of your custom ConfigMap.
    -   Set name to the name of the JSON file that stores the dashboard information.
-   Enable persistence of dashboards

    You can perform the following steps to enable persistence of Grafana dashboards:

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).
    2.  In the left-side navigation pane, click **Clusters**.
    3.  On the Clusters page, click the name of the cluster that you want to manage or click **Applications** in the **Actions** column.
    4.  In the left-side navigation pane, click **Releases**.
    5.  Find ack-prometheus-operator and click **Update** in the Actions column. The **Update Release** dialog box appears.
    6.  In the Update Release dialog box, find the persistence field in the grafana section and set the parameters, as shown in the following figure.
    ![Enable persistence of Grafana dashboards](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8155359951/p103389.png)

    You can export Grafana dashboards in JSON format. For more information, see [Export a Grafana dashboard](https://grafana.com/docs/reference/export_import/#export-and-import).


## FAQ

-   What can I do if I fail to receive DingTalk alert notifications?
    1.  Obtain the webhook URL of your DingTalk chatbot. For more information, see [Scenario 1: Use DingTalk to raise alerts upon Kubernetes events](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Event monitoring.md).
    2.  On the Parameters tab, find the dingtalk section, set enabled to true, and then enter the webhook URL of your DingTalk chatbot in the token field. For more information, see **Configure DingTalk alert notifications** in [Alert configuration](#section_6r6_hsk_wc0).
-   What can I do if the following error message appears when I deploy Prometheus in a cluster?

    The error message is:

    ```
    Can't install release with errors: rpc error: code = Unknown desc = object is being deleted: customresourcedefinitions.apiextensions.k8s.io "xxxxxxxx.monitoring.coreos.com" already exists
    ```

    The error message indicates that the cluster fails to clear custom resource definitions \(CRDs\) of the previous deployment. Run the following commands to delete CRDs and then deploy Prometheus again:

    ```
    kubectl delete crd prometheuses.monitoring.coreos.com
    kubectl delete crd prometheusrules.monitoring.coreos.com
    kubectl delete crd servicemonitors.monitoring.coreos.com
    kubectl delete crd alertmanagers.monitoring.coreos.com
    ```

-   What can I do if I fail to receive email alert notifications?

    Make sure that the value of `smtp_auth_password` is the SMTP authorization code instead of the logon password of the email account. Make sure that the STMP server endpoint includes a port number.

-   What can I do if the following error message appears when I update a YAML file?

    If the configuration file of Tiller is overlarge, the cluster cannot be accessed. To solve this issue, you can delete some annotations in the configuration file and mount the file to a pod as a ConfigMap. You can enter the name of the custom ConfigMap in the configMaps field of the prometheus and alertmanager sections. For more information, see the second method in [Mount a custom ConfigMap to Prometheus](#section_20m_m2b_ki9).

-   How do I enable the features of Prometheus after I deploy it in a cluster?

    On the management page of the cluster where Prometheus is deployed, click **Releases** in the left-side navigation pane, and click the **Helm** tab. Find **ack-prometheus-operator** and click **Update** in the Actions column. In the Update Release dialog box, set enabled to true for the features that you want to enable and click **Update**.

-   How do I select data storage: TSDB or disks?

    TSDB storage is available to limited regions. However, disk storage is supported in all regions. The following figure shows the data retention policy.

    ![Data retention policy](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6651867061/p57817.png)

-   What can I do if a Grafana dashboard fails to display data?

    On the management page of the cluster, click **Releases** in the left-side navigation pane, and click the **Helm** tab. Find **ack-prometheus-operator** and click **Update** in the Actions column. In the Update Release dialog box, find **clusterVersion** and check whether the value of clusterVersion is correct. If the cluster version is earlier than V1.16, set clusterVersion to 1.14.8-aliyun.1. If the cluster version is V1.16 or later, set clusterVersion to 1.16.6-aliyun.1.

-   What can I do if I fail to install Prometheus after I delete the namespace of Prometheus?

    After you delete the namespace of Prometheus, the settings of the resources may be retained. In this case, you may fail to install Prometheus again. You can perform the following operations to delete resource settings:

    1.  Delete role-based access control \(RBAC\) permissions.
        1.  Delete the ClusterRole.

            ```
            kubectl delete ClusterRole ack-prometheus-operator-grafana-clusterrole
            kubectl delete ClusterRole ack-prometheus-operator-kube-state-metrics
            kubectl delete ClusterRole psp-ack-prometheus-operator-kube-state-metrics
            kubectl delete ClusterRole psp-ack-prometheus-operator-prometheus-node-exporter
            kubectl delete ClusterRole ack-prometheus-operator-operator
            kubectl delete ClusterRole ack-prometheus-operator-operator-psp
            kubectl delete ClusterRole ack-prometheus-operator-prometheus
            kubectl delete ClusterRole ack-prometheus-operator-prometheus-psp
            ```

        2.  Delete the ClusterRoleBinding.

            ```
            kubectl delete ClusterRoleBinding ack-prometheus-operator-grafana-clusterrolebinding
            kubectl delete ClusterRoleBinding ack-prometheus-operator-kube-state-metrics
            kubectl delete ClusterRoleBinding psp-ack-prometheus-operator-kube-state-metrics
            kubectl delete ClusterRoleBinding psp-ack-prometheus-operator-prometheus-node-exporter
            kubectl delete ClusterRoleBinding ack-prometheus-operator-operator
            kubectl delete ClusterRoleBinding ack-prometheus-operator-operator-psp
            kubectl delete ClusterRoleBinding ack-prometheus-operator-prometheus
            kubectl delete ClusterRoleBinding ack-prometheus-operator-prometheus-psp
            ```

    2.  Delete the CustomResourceDefinition \(CRD\).

        ```
        kubectl delete crd alertmanagerconfigs.monitoring.coreos.com
        kubectl delete crd alertmanagers.monitoring.coreos.com
        kubectl delete crd podmonitors.monitoring.coreos.com
        kubectl delete crd probes.monitoring.coreos.com
        kubectl delete crd prometheuses.monitoring.coreos.com
        kubectl delete crd prometheusrules.monitoring.coreos.com
        kubectl delete crd servicemonitors.monitoring.coreos.com
        kubectl delete crd thanosrulers.monitoring.coreos.com
        ```


