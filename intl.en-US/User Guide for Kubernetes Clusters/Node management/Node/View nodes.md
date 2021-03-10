# View nodes

You can view nodes of a cluster by using the kubectl command-line interface \(CLI\) or the Container Service for Kubernetes console.

## View nodes by using kubectl

**Note:** Before you run commands to view the nodes in a Kubernetes cluster, you must connect to the cluster by using kubectl. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

Use kubectl to connect to a cluster and run the following command to view the nodes in the cluster:

```
kubectl get nodes
```

The following example shows the output:

```
kubectl get nodes
NAME                      STATUS    AGE       VERSION
iz2ze2n6ep53tch701y****   Ready     19m       v1.6.1-2+ed9e3d33a07093
iz2zeafr762wibijx39****   Ready     7m        v1.6.1-2+ed9e3d33a07093
iz2zeafr762wibijx39****   Ready     7m        v1.6.1-2+ed9e3d33a07093
iz2zef4dnn9nos8elyr****   Ready     14m       v1.6.1-2+ed9e3d33a07093
iz2zeitvvo8enoreufs****   Ready     11m       v1.6.1-2+ed9e3d33a07093
```

## View nodes in the Container Service for Kubernetes console

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.


