# 删除ARMS Prometheus和ACK Prometheus

本文介绍如何删除ARMS Prometheus和ACK Prometheus。

## 通过控制台删除ARMS Prometheus和ACK Prometheus

您可以通过控制台删除ARMS Prometheus和ACK Prometheus，删除后不会有残留。删除ARMS Prometheus和ACK Prometheus的操作相同，以下以删除ARMS Prometheus为例。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**应用** \> **Helm**。

5.  在Helm页面单击arms-prometheus**操作**列的**删除**。

    **说明：** 如果您删除的是ACK Prometheus，您需要在在Helm页面单击ack-prometheus-operator**操作**列的**删除**。

6.  在**删除应用**对话框中单击**确定**。


## 通过命令行删除ARMS Prometheus和ACK Prometheus

如果您通过删除Helm Chart中的部分组件来删除Prometheus，例如只删除命名空间，会导致资源删除后有残留配置，影响再次安装。这时您需要通过命令行手动删除Prometheus的残余配置。

通过命令行删除ARMS Prometheus。

1.  删除arms-prom namespace，并连带删除该命名空间下所有资源。

    1.  查看arms-prom namespace。

        ```
        kubectl get namespaces
        ```

    2.  删除arms-prom namespace。

        ```
        kubectl delete namespace arms-prom
        ```

2.  删除RBAC权限。

    1.  查看ClusterRoles。

        ```
        kubectl get ClusterRoles
        ```

    2.  删除ClusterRole。

        ```
        kubectl delete ClusterRole arms-kube-state-metrics
        kubectl delete ClusterRole arms-node-exporter
        kubectl delete ClusterRole arms-prom-ack-arms-prometheus-role
        kubectl delete ClusterRole arms-prometheus-oper3
        ```

    3.  查看ClusterRoleBinding。

        ```
        kubectl get ClusterRoleBinding
        ```

    4.  删除ClusterRoleBinding。

        ```
        kubectl delete ClusterRoleBinding arms-node-exporter
        kubectl delete ClusterRoleBinding arms-prom-ack-arms-prometheus-role-binding
        kubectl delete ClusterRoleBinding arms-prometheus-oper-bind2
        kubectl delete ClusterRoleBinding kube-state-metrics
        ```


通过命令行删除ACK Prometheus。

1.  删除monitoring命名空间，并连带删除该命名空间下所有资源。

    1.  查看monitoring命名空间。

        ```
        kubectl get namespaces
        ```

    2.  删除monitoring命名空间。

        ```
        kubectl delete namespace monitoring
        ```

2.  删除RBAC权限。

    1.  查看ClusterRoles。

        ```
        kubectl get ClusterRoles
        ```

    2.  删除ClusterRole。

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

    3.  查看ClusterRoleBinding。

        ```
        kubectl get ClusterRoleBinding
        ```

    4.  删除ClusterRoleBinding。

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

3.  删除CRD。

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


