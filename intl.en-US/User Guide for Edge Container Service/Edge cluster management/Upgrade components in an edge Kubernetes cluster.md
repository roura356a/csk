---
keyword: [upgrade the components in an edge Kubernetes cluster, edge Kubernetes cluster]
---

# Upgrade components in an edge Kubernetes cluster

This topic describes how to upgrade the components in an edge Kubernetes cluster. This allows you to perform fine-grained version upgrades for the components in a cluster of the latest Kubernetes version.

-   An edge Kubernetes cluster is created. For more information, see [Create a managed edge Kubernetes cluster](/intl.en-US/User Guide for Edge Container Service/Edge cluster management/Create a managed edge Kubernetes cluster.md).
-   Docker is installed on your on-premises machine. For more information, see [Install Docker](https://docs.docker.com/install/).

## Procedure

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the **Clusters** page, find the cluster that you want to manage and choose **More** \> **Manage System Components** in the **Actions** column.

4.  On the **Add-ons** page, you can perform one of the following operations:

    -   Find the component that you want to install and click **Install**.
    -   Find the component that you want to uninstall and click **Uninstall**.
    -   Find the component that you want to upgrade and click **Upgrade**.
5.  Upgrade the **edge-tunnel-server** and **edge-tunnel-agent** components.

    **Note:** If the Kubernetes version of your cluster is 1.12.6-aliyunedge.1, you must perform the following steps to upgrade **edge-tunnel-server** and **edge-tunnel-agent**.

    1.  Manually delete the DaemonSets, Deployments, and Services that are related to the **frps or frpc** component. To do this, perform the following steps:

        1.  In the left-side navigation pane, click **Clusters**.
        2.  On the **Clusters** page, click the name of the cluster that you want to manage or click **Details** in the **Actions** column.
        3.  On the **details** page of the cluster, click the **Connection Information** tab. Click the **Public Access** tab and copy the content of the **kubeconfig** file to the $HOME/.kube/config file of your on-premises machine.
        4.  Run the following command:

            ```
            docker run -v ~/.kube:/root/.kube registry.cn-hangzhou.aliyuncs.com/acs/edge-upgrade-addon:v1.0 tunnel
            ```

    2.  On the **Add-ons** page, find **edge-tunnel-server** and click **Upgrade** in the **Actions** column.

    3.  On the **Add-ons** page, find the **edge-tunnel-agent** component and click **Upgrade** in the **Actions** column.


**Related topics**  


[Upgrade an edge cluster](/intl.en-US/User Guide for Edge Container Service/Edge cluster management/Upgrade an edge cluster.md)

