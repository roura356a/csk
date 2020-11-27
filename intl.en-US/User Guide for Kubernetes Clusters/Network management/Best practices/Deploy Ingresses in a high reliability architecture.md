# Deploy Ingresses in a high reliability architecture

This topic describes how to deploy Ingresses in a high reliability architecture for a cluster of Container Service for Kubernetes \(ACK\). The reliability of Ingresses is particularly important because Ingresses manage external access to Services in an ACK cluster.

-   An ACK cluster is created. For more information, see [Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md).
-   You are connected to a master node of the cluster by using SSH. For more information, see [Use SSH to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use SSH to connect to an ACK cluster.md).

An Ingress is a set of rules that regulate external access to Services in a Kubernetes cluster. Ingresses provide load balancing at Layer 7. When you configure an Ingress, you can specify the URLs, Server Load Balancer \(SLB\) instances, Secure Sockets Layer \(SSL\) connections, and virtual hosts \(with specified names\) that are required to enable external access to Services in the cluster.

## High reliability deployment architecture

The multi-replica deployment is commonly used to resolve the issue of single point of failure \(SPOF\) for high reliability. In Kubernetes clusters, Ingresses are deployed in a multi-pod architecture to ensure the high reliability of the access layer. Ingresses are the entrance to Services in a Kubernetes cluster. We recommend that you use exclusive Ingress nodes to avoid resource contention between applications and Ingress Services.

![Architecture](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5545359951/p10101.png)

In the preceding figure, the access layer is composed of multiple exclusive Ingress nodes. You can also scale the number of Ingress nodes based on the traffic volume to the backend applications. If your cluster size is small, you can also deploy Ingress Services and applications in a hybrid way. In this case, we recommend that you isolate resources and restrict resource consumption.

## Query the pods of the NGINX Ingress controller and the public IP address of the related SLB instance

After an ACK cluster is created, the NGINX Ingress controller with two pods is automatically deployed. A public-facing SLB instance is also created as the frontend load balancing Service.

1.  Run the following command to query the pods that are provisioned for the NGINX Ingress controller:

    ```
    kubectl -n kube-system get pod | grep nginx-ingress-controller
    ```

    ```
    nginx-ingress-controller-8648ddc696-2bshk                    1/1     Running   0          3h
    nginx-ingress-controller-8648ddc696-jvbs9                    1/1     Running   0          3h
    ```

2.  Run the following command to query the IP address of the frontend SLB instance:

    ```
    kubectl -n kube-system get svc nginx-ingress-lb
    ```

    ```
    NAME               TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)                      AGE
    nginx-ingress-lb   LoadBalancer   172.xx.x.xx   118.xxx.xxx.xx   80:32457/TCP,443:31370/TCP   21d
    ```


When the cluster size grows, you may need to expand the access layer to deal with the increasing traffic volume. You can use the following methods to expand the access layer:

## Method 1: Scale out the number of pods

You can scale out the number of pods that are provisioned for the NGINX Ingress controller to expand the access layer.

1.  Run the following command to scale the number of pods to 3:

    ```
    kubectl -n kube-system scale --replicas=3 deployment/nginx-ingress-controller
    deployment.extensions/nginx-ingress-controller scaled
    ```

2.  After the scaling is complete, run the following command to query the pods that are provisioned for the NGINX Ingress controller:

    ```
    kubectl -n kube-system get pod | grep nginx-ingress-controller
    ```

    ```
    nginx-ingress-controller-8648ddc696-2bshk                    1/1     Running   0          3h
    nginx-ingress-controller-8648ddc696-jvbs9                    1/1     Running   0          3h
    nginx-ingress-controller-8648ddc696-xqmfn                    1/1     Running   0          33s
    ```


## Method 2: Deploy Ingresses on nodes with higher specifications

You can add specified labels to nodes with higher specifications. Then, pods provisioned for the NGINX Ingress controller are scheduled to these nodes.

1.  Run the following command to query information about the nodes in the cluster:

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

2.  Run the following commands to add the `node-role.kubernetes.io/ingress="true"` label to the `cn-hangzhou.i-bp12h6biv9bg24lmdc2o` and `cn-hangzhou.i-bp12h6biv9bg24lmdc2p` nodes.

    **Note:**

    -   The number of labeled nodes must be equal to or more than that of the pods that are provisioned for the NGINX Ingress controller so that each pod runs on an exclusive node. This ensures the high reliability of the Ingresses.
    -   In the output that is returned, if the value of ROLES is none, it indicates that the related node is a worker node.
    -   We recommend that you deploy the NGINX Ingress controller on worker nodes.
    ```
    kubectl label nodes cn-hangzhou.i-bp12h6biv9bg24lmdc2o node-role.kubernetes.io/ingress="true"
    node/cn-hangzhou.i-bp12h6biv9bg24lmdc2o labeled
    ```

    ```
    kubectl label nodes cn-hangzhou.i-bp12h6biv9bg24lmdc2p node-role.kubernetes.io/ingress="true"
    node/cn-hangzhou.i-bp12h6biv9bg24lmdc2p labeled
    ```

3.  Run the following command to update the related Deployment by adding the nodeSelector field:

    ```
    kubectl -n kube-system patch deployment nginx-ingress-controller -p '{"spec": {"template": {"spec": {"nodeSelector": {"node-role.kubernetes.io/ingress": "true"}}}}}'
    deployment.extensions/nginx-ingress-controller patched
    ```


Run the following command to query the pods that are provisioned for the NGINX Ingress controller. In the output that is returned, you can find that the pods are scheduled to the nodes that are labeled with `node-role.kubernetes.io/ingress="true`.

```
kubectl -n kube-system get pod -o wide | grep nginx-ingress-controller
```

```
nginx-ingress-controller-8648ddc696-2bshk                    1/1     Running   0          3h    172.16.2.15     cn-hangzhou.i-bp12h6biv9bg24lmdc2p   <none>
nginx-ingress-controller-8648ddc696-jvbs9                    1/1     Running   0          3h    172.16.2.145    cn-hangzhou.i-bp12h6biv9bg24lmdc2o   <none>
```

