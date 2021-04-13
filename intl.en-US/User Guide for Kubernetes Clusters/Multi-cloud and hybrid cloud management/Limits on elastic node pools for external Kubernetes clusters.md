---
keyword: [elastic node pool, external Kubernetes cluster, limits]
---

# Limits on elastic node pools for external Kubernetes clusters

You can use elastic node pools to manage a set of Elastic Compute Service \(ECS\) instances for external Kubernetes clusters. You can add ECS instance-based nodes from elastic node pools to self-managed Kubernetes clusters or Kubernetes clusters that are provided by other cloud service providers. You can also use node pools to manage the labels and taints of nodes in node pools. This topic describes the limits on elastic node pools for external Kubernetes clusters.

## Kubernetes versions

Kubernetes 1.16.9 or later is used for the self-managed Kubernetes clusters or Kubernetes clusters that are provided by other cloud service providers.

## Modes in which nodes are added to the cluster

You must use a [bootstrap token](https://kubernetes.io/docs/reference/access-authn-authz/bootstrap-tokens/#bootstrap-tokens-overview) and run the [kubeadm join](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-join/) command to add a node to a self-managed Kubernetes cluster or Kubernetes cluster that is provided by other cloud service providers.

You can run kubeadm commands to access the [cluster-info](https://kubernetes.io/docs/reference/setup-tools/kubeadm/implementation-details/#create-the-public-cluster-info-configmap) ConfigMap. This allows you to connect to the cluster. You can run the following command to check whether the **cluster-info** ConfigMap exists in the **kube-public** namespace:

```
kubectl -n kube-public get configmap
```

```
NAME           DATA   AGE
cluster-info   4      3h56m
```

## VPC connections

The self-managed Kubernetes cluster or Kubernetes cluster provided by other cloud service providers can be connected to the virtual private cloud \(VPC\) where the ACK cluster for registration is deployed. You can use Cloud Enterprise Network \(CEN\) to establish the connection. For more information, see [Overview]().

## Network plug-ins

The self-managed Kubernetes cluster or Kubernetes cluster that is provided by other cloud service providers must use the following network plug-ins: Flannel VxLAN, Calico BGP peer, and Calico BGP node-to-node mesh. If you want to use other network plug-ins, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) for confirmation.

