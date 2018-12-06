# Upgrade a Kubernetes cluster {#concept_zf5_tng_vdb .concept}

You can upgrade the version of your Kubernetes cluster in the Container Service console.

On the cluster list page, you can view the version of your Kubernetes cluster.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16648/154409483310891_en-US.png)

## Prerequisites {#section_udz_43w_ydb .section}

-   Make sure that your host can access the Internet so that the system can download the required software package.
-   A cluster upgrade may fail. We recommend that you create a snapshot for your cluster to guarantee your data security before upgrading the cluster. For more information, see [Create a snapshot](../../../../reseller.en-US/User Guide/Snapshots/Create a snapshot.md#).
-   If you are upgrading a Kubernetes cluster of version number V1.8.1 or V1.8.4 to V1.9.3, all cluster pods will be restarted. This means that applications running on the cluster will be affected. If you are upgrading a Kubernetes cluster version of a different number, cluster applications will not be affected. However, if a cluster application is highly dependent on the API server, the application may be temporarily affected by the upgrade.
-   OSS volumes will be re-mounted to the cluster because the network is reset during the cluster upgrade. Therefore, you need to re-create the pods that use the OSS volumes after the upgrade.

## Preparations {#section_ylf_vng_vdb .section}

You must make sure that your cluster is in healthy status before the upgrade.

You must to log on to a Master node. For more information, see [Access Kubernetes clusters by using SSH](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Access Kubernetes clusters by using SSH.md#) and [Connect to a Kubernetes cluster by using kubectl](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#).

1.  Run the `kubectl get cs` command to verify that all cluster modules are healthy.

    ```
    NAME                 STATUS    MESSAGE              ERROR
     scheduler            Healthy   ok
     controller-manager   Healthy   ok
     etcd-0               Healthy   {"health": "true"}
     etcd-1               Healthy   {"health": "true"}
     etcd-2               Healthy   {"health": "true"}
    ```

2.  Run the `kubectl get nodes` command to verify that all nodes are ready.

    **Note:** All nodes must be in ready status only.

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


If a node is abnormal, you can fix it on your own or you can submit a ticket to ask for technical support from Alibaba Cloud.

## Procedure {#section_fmf_vng_vdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Kubernetes, click **Clusters**.
3.  Select the target cluster and choose **More** \> **Upgrade Cluster**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16648/154409483410892_en-US.png)

4.  In the displayed dialog box, click **Upgrade**.

    The system starts to upgrade the Kubernetes version.


After the upgrade is completed, you can view the Kubernetes version of the cluster on the cluster list page and verify that the upgrade is successful.

