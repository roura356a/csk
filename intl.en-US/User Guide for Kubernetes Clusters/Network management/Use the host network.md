# Use the host network

If a pod runs in the host network of the node where the pod is deployed, the pod can use the network namespace and network resources of the node. In this case, the pod can access loopback devices, listen to addresses, and monitor the traffic of other pods on the node.

-   A Container Service for Kubernetes \(ACK\) cluster is created. For more information, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).
-   The ACK cluster can be accessed with kubectl. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

1.  Open the YAML file host-network.yaml and set `hostNetwork` to true under spec.

    The following example shows the YAML file:

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
    spec:
      hostNetwork: true
      containers:
      - name: nginx
        image: nginx
    ```

2.  Run the following command to create a pod:

    ```
    kubectl apply -f host-network.yaml
    ```

3.  Run the following command to check whether the pod runs in the host network of the node where the pod is deployed:

    ```
    kubectl get pod -o wide
    ```

    The command output shows that the IP address of the pod is the same as that of the node. This indicates that the pod runs in the host network of the node.

    ```
    NAME    READY   STATUS    RESTARTS   AGE   IP              NODE                           NOMINATED NODE
    nginx   1/1     Running   0          29s   192.168.XX.XX   cn-zhangjiakou.192.168.XX.XX   <none>
    ```


