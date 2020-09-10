# Deploy a highly reliable Ingress controller

Ingress requires high reliability because Ingress functions as the access layer through which external traffic goes into a cluster. This topic describes how to deploy a high-performance, highly reliable Ingress access layer.

## Prerequisites

-   You have created a Kubernetes cluster. For more information, see [t16639.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Proprietary Edition.md).
-   You have connected to the Master node by using SSH. For more information, see [t16642.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Manage and access clusters/Use SSH to connect to a cluster.md).

## Background information

Ingress is a set of rules that authorize external access to the services in a Kubernetes cluster, providing Layer-7 Server Load Balancer capabilities. You can configure Ingress to provide externally accessible URLs, SLB, SSL, and name-based virtual hosts.

## Highly reliable deployment architecture

To achieve high reliability, you must first resolve any SPOFs. Deploying multiple replicas is the general solution for this problem. Specifically, you can use the multi-node deployment architecture to deploy a highly reliable Ingress access layer in a Kubernetes cluster. We also recommend that you configure exclusive Ingress nodes to prevent service applications from competing for resources with the Ingress service because Ingress functions as the traffic access port of a cluster.

![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5545359951/p10101.png)

As shown in the preceding figure, multiple exclusive Ingress instances constitute an access layer that processes the inbound traffic to the cluster. Furthermore, the number of Ingress nodes can be scaled according to the traffic amount required by the backend services. If your cluster is of a moderate size, you can also deploy the Ingress service and other service applications in a hybrid way. However, we recommend that you limit the number of resources and isolate them for the Ingress and corresponding applications.

## View the cluster pod replicas deployed by default and the Internet IP address

After you create a cluster, a set of Nginx Ingress controller services that have two pod replicas are deployed within the cluster by default. The frontend of this set of services is mounted to an Internet SLB instance.

Run the following command to view the pods on which the Nginx Ingress controller services are deployed:

```
kubectl -n kube-system get pod | grep nginx-ingress-controller
```

```
nginx-ingress-controller-8648ddc696-2bshk                    1/1     Running   0          3h
nginx-ingress-controller-8648ddc696-jvbs9                    1/1     Running   0          3h
```

Run the following command to view the Internet SLB address corresponding to the nginx-ingress-lb service:

```
kubectl -n kube-system get svc nginx-ingress-lb
```

```
NAME               TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)                      AGE
nginx-ingress-lb   LoadBalancer   172.xx.x.xx   118.xxx.xxx.xx   80:32457/TCP,443:31370/TCP   21d
```

To guarantee the high performance and availability of the cluster access layer for a growing cluster, you need to expand the Ingress access layer. You can use either of the following two methods:

## Method 1: Increase the number of replicas

You can quickly enlarge the scale of the Ingress access layer by changing the number of the replicas of the Nginx Ingress controller deployment.

Run the following command to scale out the number of pod replicas to three:

```
kubectl -n kube-system scale --replicas=3 deployment/nginx-ingress-controller
deployment.extensions/nginx-ingress-controller scaled
```

Run the following command to view the pods on which the Nginx Ingress controller services are deployed:

```
kubectl -n kube-system get pod | grep nginx-ingress-controller
```

```
nginx-ingress-controller-8648ddc696-2bshk                    1/1     Running   0          3h
nginx-ingress-controller-8648ddc696-jvbs9                    1/1     Running   0          3h
nginx-ingress-controller-8648ddc696-xqmfn                    1/1     Running   0          33s
```

## Method 2: Deploy the Ingress service on a specified node

If you want the Nginx Ingress controller to run on target nodes of advanced configurations only, you can label the target nodes.

1.  Run the following command to view the cluster nodes:

    ```
    kubectl get node
    ```

    ```
    NAME                                 STATUS   ROLES    AGE   VERSION
    cn-hangzhou.i-bp11bcmsna8d4bp****   Ready    master   21d   v1.11.5
    cn-hangzhou.i-bp12h6biv9bg24l****   Ready    <none>   21d   v1.11.5
    cn-hangzhou.i-bp12h6biv9bg24l****   Ready    <none>   21d   v1.11.5
    cn-hangzhou.i-bp12h6biv9bg24l****   Ready    <none>   21d   v1.11.5
    cn-hangzhou.i-bp181pofzyyksie****   Ready    master   21d   v1.11.5
    cn-hangzhou.i-bp1cbsg6rf3580z****   Ready    master   21d   v1.11.5
    ```

    **Note:** In the output of a Worker node, the `ROLES` column displays none.

2.  Run the following commands to add the label `node-role.kubernetes.io/ingress="true"` to the Ingress node `cn-hangzhou.i-bp12h6biv9bg24lmdc2o` and the Ingress node `cn-hangzhou.i-bp12h6biv9bg24lmdc2p`:

    **Note:**

    -   The number of the labeled nodes must be greater than or equal to the number of the cluster pod replicas so that multiple pods do not run on one node.
    -   We recommend that you label Worker nodes only to deploy the Ingress service.
    ```
    kubectl label nodes cn-hangzhou.i-bp12h6biv9bg24lmdc2o node-role.kubernetes.io/ingress="true"
    node/cn-hangzhou.i-bp12h6biv9bg24lmdc2o labeled
    ```

    ```
    kubectl label nodes cn-hangzhou.i-bp12h6biv9bg24lmdc2p node-role.kubernetes.io/ingress="true"
    node/cn-hangzhou.i-bp12h6biv9bg24lmdc2p labeled
    ```

3.  Run the following command to update your deployment and add the nodeSelector setting:

    ```
    kubectl -n kube-system patch deployment nginx-ingress-controller -p '{"spec": {"template": {"spec": {"nodeSelector": {"node-role.kubernetes.io/ingress": "true"}}}}}'
    deployment.extensions/nginx-ingress-controller patched
    ```


**Result**

Run the following command to verify that the Ingress pods are deployed on the cluster nodes that are labeled by `node-role.kubernetes.io/ingress="true"`:

```
kubectl -n kube-system get pod -o wide | grep nginx-ingress-controller
```

```
nginx-ingress-controller-8648ddc696-2bshk                    1/1     Running   0          3h    172.16.2.15     cn-hangzhou.i-bp12h6biv9bg24lmdc2p   <none>
nginx-ingress-controller-8648ddc696-jvbs9                    1/1     Running   0          3h    172.16.2.145    cn-hangzhou.i-bp12h6biv9bg24lmdc2o   <none>
```

