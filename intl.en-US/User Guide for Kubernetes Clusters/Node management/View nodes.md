# View nodes {#task_1664343 .task}

You can view nodes in a cluster through commands, the Container Service console, or the Kubernetes dashboard.

## Through commands {#section_cyj_fhl_6bb .section}

**Note:** Before you run commands to view the nodes in a cluster, you need to [EN-US\_TP\_16645.md\#](reseller.en-US//Connect to a Kubernetes cluster by using kubectl.md#).

Connect to the Kubernetes cluster through kubectl and run the following command to view nodes in the cluster.

``` {#codeblock_qs8_jj7_3ig}
kubectl get nodes
```

Sample responses:

``` {#codeblock_gxc_r5l_rb4}
$ kubectl get nodes
NAME                      STATUS    AGE       VERSION
iz2ze2n6ep53tch701y****   Ready     19m       v1.6.1-2+ed9e3d33a07093
iz2zeafr762wibijx39****   Ready     7m        v1.6.1-2+ed9e3d33a07093
iz2zeafr762wibijx39****   Ready     7m        v1.6.1-2+ed9e3d33a07093
iz2zef4dnn9nos8elyr****   Ready     14m       v1.6.1-2+ed9e3d33a07093
iz2zeitvvo8enoreufs****   Ready     11m       v1.6.1-2+ed9e3d33a07093
```

## Through the Container Service console {#section_mjp_yzl_zy6 .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane, choose **Clusters** \> **Nodes** to go to the Nodes page.
3.  Select a cluster to view nodes in the cluster. 

    ![View nodes](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1319241/156905604255407_en-US.png)


