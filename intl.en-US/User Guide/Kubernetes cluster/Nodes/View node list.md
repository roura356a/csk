# View node list {#concept_a5k_xkl_vdb .concept}

You can view the node list of the Kubernetes cluster by using commands, in the Container Service console, or in the Kubernetes dashboard.

## View node list by using commands {#section_q5z_ykl_vdb .section}

**Note:** Before using commands to view the node list of the Kubernetes cluster, [EN-US\_TP\_6883.md\#](reseller.en-US/User Guide/Kubernetes cluster/Clusters/Connect to a Kubernetes cluster by using kubectl.md#) first.

After connecting to the Kubernetes cluster by using kubectl, run the following command to view the nodes in the cluster:

```
kubectl get nodes
```

**Sample output:**

```
$ kubectl get nodes
NAME STATUS AGE VERSION
iz2ze2n6ep53tch701yh9zz Ready 19m v1.6.1-2+ed9e3d33a07093
iz2zeafr762wibijx39e5az Ready 7m v1.6.1-2+ed9e3d33a07093
iz2zeafr762wibijx39e5bz Ready 7m v1.6.1-2+ed9e3d33a07093
iz2zef4dnn9nos8elyr32kz Ready 14m v1.6.1-2+ed9e3d33a07093
iz2zeitvvo8enoreufstkmz Ready 11m v1.6.1-2+ed9e3d33a07093
```

## View node list in Container Service console {#section_bcz_fll_vdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Click Kubernetes \>**Clusters \>** \> **Nodes**in the left-side navigation pane.
3.  Select the cluster from the Cluster drop-down list and then view the node list of this cluster.

## View node list in Kubernetes dashboard {#section_izx_gll_vdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Click Kubernetes \> **Clusters** in the left-side navigation pane.
3.  Click **Dashboard** at the right of the cluster to enter the Kubernetes dashboard.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6890/15395833124350_en-US.png)

4.  In the Kubernetes dashboard, click **Nodes** in the left-side navigation pane to view the node list of this cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6890/15395833124351_en-US.png)


