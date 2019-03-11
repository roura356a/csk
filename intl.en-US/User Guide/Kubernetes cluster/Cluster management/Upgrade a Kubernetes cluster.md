# Upgrade a Kubernetes cluster {#concept_zf5_tng_vdb .concept}

This topic describes how to upgrade the Kubernetes version of your cluster in the Alibaba Cloud Container Service for Kubernetes console.

On the cluster list page, you can view the Kubernetes version of your cluster.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16648/155228647110891_en-US.png)

## Limits {#section_udz_43w_ydb .section}

-   The cluster node instances must be able to access the Internet so that the system can download the required software package.
-   We recommend that you create a snapshot for your cluster to guarantee your data security before upgrading the cluster. For more information, see [Create a snapshot](../../../../../reseller.en-US/Snapshots/Use snapshots/Create a snapshot.md#).
-   If you are upgrading a Kubernetes cluster of version 1.8.1 or 1.8.4 to version 1.9.3, all cluster pods will be restarted. This means that applications running on the cluster will be affected. If you are upgrading a Kubernetes cluster of another version, cluster applications will not be affected. However, if a cluster application is highly dependent on the API server, the application may be temporarily affected by the upgrade.
-   When a cluster is being upgraded, the network is reset, which means OSS volumes will be remounted to the cluster. As a result, you need to re-create the pods that use OSS volumes after the upgrade.

## Preparations {#section_ylf_vng_vdb .section}

You must make sure that the target cluster is in the healthy status before the upgrade. To do so, follow these steps:

1.  Log on to the Master node. For more information, see [Access Kubernetes clusters by using SSH](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Access a Kubernetes cluster by using SSH.md#) and [Connect to a Kubernetes cluster by using kubectl](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#).
2.  Run the `kubectl get cs` command to verify that all cluster modules are in the healthy status.

    ```
    NAME                 STATUS    MESSAGE              ERROR
     scheduler            Healthy   ok
     controller-manager   Healthy   ok
     etcd-0               Healthy   {"health": "true"}
     etcd-1               Healthy   {"health": "true"}
     etcd-2               Healthy   {"health": "true"}
    ```

3.  Run the `kubectl get nodes` command to verify that all nodes are in the ready status.

    **Note:** All nodes must be in the ready status.

    ```
    kubectl get nodes
     NAME                   STATUS    ROLES     AGE       VERSION
     cn-shanghai.i-xxxxxx   Ready     master    38d       v1.9.3
     cn-shanghai.i-xxxxxx   Ready     <none>    38d       v1.9.3
     cn-shanghai.i-xxxxxx   Ready     <none>    38d       v1.9.3
     cn-shanghai.i-xxxxxx   Ready     <none>    38d       v1.9.3
     cn-shanghai.i-xxxxxx   Ready     master    38d       v1.9.3
     cn-shanghai.i-xxxxxx   Ready     master    38d       v1.9.3
    ```


If a node is abnormal, you can either repair it manually or open a ticket.

## Procedure {#section_fmf_vng_vdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Clusters** \> **Clusters**.
3.  On the right of the target cluster, choose **More** \> **Upgrade Cluster**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16648/155228647139655_en-US.png)

4.  In the displayed dialog box, click **Upgrade**.

