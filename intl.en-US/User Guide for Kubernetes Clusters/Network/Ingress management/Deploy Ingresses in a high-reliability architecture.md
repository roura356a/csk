---
keyword: [Ingress Controller, Ingress access layer]
---

# Deploy Ingresses in a high-reliability architecture

An Ingress is a set of rules that authorize external access to Services within a Kubernetes cluster. Ingresses provide Layer 7 load balancing. You can configure Ingresses to specify the URL, Server Load Balancer \(SLB\) instance, Secure Sockets Layer \(SSL\) connections, and name-based Web Hosting instances that are required to enable external access. The high reliability of Ingresses is important because Ingresses manage external access to Services within a cluster. This topic describes how to deploy Ingresses in a high-reliability architecture for a Container Service for Kubernetes \(ACK\) cluster.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   You are connected to the master node of the ACK cluster through SSH. For more information, see [Use SSH to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use SSH to connect to an ACK cluster.md).

## High-reliability deployment architecture

A multi-replica deployment architecture is a common solution used to provide high reliability and resolve single point of failures \(SPOFs\). In ACK clusters, Ingresses are deployed in a multi-node architecture to ensure the high reliability of the access layer. Ingresses are the entrance to Kubernetes Services. We recommend that you use exclusive Ingress nodes to avoid scenarios where applications and Ingresses compete for resources.

![Architecture](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5545359951/p10101.png)

The access layer in the preceding figure is composed of multiple exclusive Ingress nodes. You can also scale the number of Ingress nodes based on the traffic volume to the backend applications. If your cluster size is small, you can deploy Ingresses and applications together. In this case, we recommend that you isolate resources and restrict resource consumption.

## Query the pods of the NGINX Ingress controller and the public IP address of the SLB instance

After an ACK cluster is created, the NGINX Ingress controller with two pods is automatically deployed. A public-facing SLB instance is also created as the frontend load balancing Service.

1.  To query the pods that are provisioned for the NGINX Ingress controller, run the following command:

    ```
    kubectl -n kube-system get pod | grep nginx-ingress-controller
    ```

    ```
    nginx-ingress-controller-8648ddc696-2bshk                    1/1     Running   0          3h
    nginx-ingress-controller-8648ddc696-jvbs9                    1/1     Running   0          3h
    ```

2.  To query the IP address of the frontend SLB instance, run the following command:

    ```
    kubectl -n kube-system get svc nginx-ingress-lb
    ```

    ```
    NAME               TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)                      AGE
    nginx-ingress-lb   LoadBalancer   172.xx.x.xx   118.xxx.xxx.xx   80:32457/TCP,443:31370/TCP   21d
    ```


## Deploy an Ingress access layer with high reliability

When the cluster size grows, you must expand the access layer to ensure high performance and high availability of the access layer. You can use the following methods to expand the access layer:

-   **Method 1: Increase the number of pods**

    You can increase the number of pods that are provisioned for the Deployment of the NGINX Ingress controller to expand the access layer.

    1.  Run the following command to scale the number of pods to 3:

        ```
        kubectl -n kube-system scale --replicas=3 deployment/nginx-ingress-controller
        deployment.extensions/nginx-ingress-controller scaled
        ```

    2.  After you scale the number of pods, run the following command to query the pods that are provisioned for the NGINX Ingress controller:

        ```
        kubectl -n kube-system get pod | grep nginx-ingress-controller
        ```

        ```
        nginx-ingress-controller-8648ddc696-2bshk                    1/1     Running   0          3h
        nginx-ingress-controller-8648ddc696-jvbs9                    1/1     Running   0          3h
        nginx-ingress-controller-8648ddc696-xqmfn                    1/1     Running   0          33s
        ```

-   **Method 2: Deploy Ingresses on nodes with higher specifications**

    You can add labels to nodes with higher specifications. Then, pods provisioned for the NGINX Ingress controller are scheduled to these nodes.

    1.  To query information about the nodes in the cluster, run the following command:

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

    2.  Run the following command to add the `node-role.kubernetes.io/ingress="true"` label to the `cn-hangzhou.i-bp12h6biv9bg24lmdc2o` and `cn-hangzhou.i-bp12h6biv9bg24lmdc2p` nodes.

        ```
        kubectl label nodes cn-hangzhou.i-bp12h6biv9bg24lmdc2o node-role.kubernetes.io/ingress="true"
        node/cn-hangzhou.i-bp12h6biv9bg24lmdc2o labeled
        ```

        ```
        kubectl label nodes cn-hangzhou.i-bp12h6biv9bg24lmdc2p node-role.kubernetes.io/ingress="true"
        node/cn-hangzhou.i-bp12h6biv9bg24lmdc2p labeled
        ```

        **Note:**

        -   The number of labeled nodes must be equal to or greater than the number of pods that are provisioned for the NGINX Ingress controller. This ensures that each pod runs on an exclusive node.
        -   If the value of ROLES is none in the returned response, it indicates that the related node is a worker node.
        -   We recommend that you add labels to and deploy Ingresses on worker nodes.
    3.  Run the following command to update the related Deployment by adding the nodeSelector field:

        ```
        kubectl -n kube-system patch deployment nginx-ingress-controller -p '{"spec": {"template": {"spec": {"nodeSelector": {"node-role.kubernetes.io/ingress": "true"}}}}}'
        deployment.extensions/nginx-ingress-controller patched
        ```

    4.  Run the following command to query the pods that are provisioned for the NGINX Ingress controller. The result indicates that pods are scheduled to the nodes that are labeled with `node-role.kubernetes.io/ingress="true"`.

        ```
        kubectl -n kube-system get pod -o wide | grep nginx-ingress-controller
        ```

        ```
        nginx-ingress-controller-8648ddc696-2bshk                    1/1     Running   0          3h    172.16.2.15     cn-hangzhou.i-bp12h6biv9bg24lmdc2p   <none>
        nginx-ingress-controller-8648ddc696-jvbs9                    1/1     Running   0          3h    172.16.2.145    cn-hangzhou.i-bp12h6biv9bg24lmdc2o   <none>
        ```


