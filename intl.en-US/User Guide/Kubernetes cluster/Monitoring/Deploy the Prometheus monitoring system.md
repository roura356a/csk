# Deploy the Prometheus monitoring system {#concept_nzh_x4x_4fb .concept}

Prometheus is an open source monitoring tool for cloud native applications. This topic describes how to deploy the Prometheus monitoring system by using Alibaba Cloud Container Service for Kubernetes.

## Background information {#section_ifs_pty_pfb .section}

A monitoring system monitors the following two types of objects:

-   Resource, which is the resource usage of a node or application. The monitoring system of Container Service for Kubernetes monitors node resource usage, cluster resource usage, and pod resource usage.
-   Application, which includes internal application metrics. For example, The monitoring system collects statistics regarding the number of online users that use an application in real time, and performs service-level monitoring and alarming for the application by exposing ports.

The following are the objects monitored in a Kubernetes cluster:

-   System components, which are built-in components of the Kubernetes cluster, such as apiserver, controller-manager, and etcd.
-   Static resource entities, which include node resource status and kernel events.
-   Dynamic resource entities, which are abstract workload entities of Kubernetes, such as deployment, DaemonSet, and pods.
-   Customized application objects, which includes the data and metrics that require customization within an application.

To monitor system components and static resource entities, specify monitoring methods for them in the configuration file.

To monitor dynamic resource entities, we recommend that you deploy the Prometheus monitoring system.

## Prerequisites {#section_csx_4ty_pfb .section}

-   You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   You have connected to the Master node so that you can view node labels and other information. For more information, see [Connect to a Kubernetes cluster by using kubectl](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#).

## Deploy the Prometheus monitoring system {#section_jtv_wty_pfb .section}

1.  Run the following command to download the prometheus-operator code:

    ```
    git clone https://github.com/AliyunContainerService/prometheus-operator 
    ```

2.  Run the following command to deploy the Prometheus monitoring system:

    ```
    cd prometheus-operator/contrib/kube-prometheus
    kubectl apply -f manifests
    ```

3.  Run the following command to set the Prometheus access:

    ```
    kubectl --namespace monitoring port-forward svc/prometheus-k8s 9090
    ```

4.  View the deployment result
    1.  To view Prometheus, access`localhost:9090` in a browser.

        **Note:** By default, Prometheus cannot be accessed through the Internet. You must use your local proxy to access it.

    2.  Select **Targets** under the **Status** menu to view all collection tasks.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24503/154407963321082_en-US.png)

        If the status of all tasks is **UP**, all collection tasks are running properly.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24503/154407963321084_en-US.png)


## View and display data aggregation {#section_ljp_yty_pfb .section}

1.  Run the following command to access Grafana:

    ```
    kubectl --namespace monitoring port-forward svc/grafana 3000
    ```

2.  Access `localhost:3000` in a browser and then select a dashboard to view data aggregation.

    **Note:** The default user name and password are both admin.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24503/154407963321092_en-US.png)


## View alerting rules and set alert silencing {#section_ygc_15y_pfb .section}

-   **View alerting rules**

    Open `localhost:9090` in your browser and click the**Alerts** menu to view the current alerting rules.

    -   Red: indicates that an alert is triggered.
    -   Green: indicates normal status.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24503/154407963321097_en-US.png)

-   **Set alert silencing**

    Run the following command, open`localhost:9093` in your browser, and select **Silenced** to set alert silencing:

    ```
    kubectl --namespace monitoring port-forward svc/alertmanager-main 9093
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24503/154407963321100_en-US.png)


