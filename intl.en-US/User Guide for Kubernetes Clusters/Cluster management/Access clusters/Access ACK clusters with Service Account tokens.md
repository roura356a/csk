# Access ACK clusters with Service Account tokens

This topic describes how to access clusters of Container Service for Kubernetes \(ACK\) with Service Account tokens. This method is applicable to all types of ACK clusters. In this example, a cluster of ACK Managed Edition is used.

-   A cluster of ACK Managed Edition is created. For more information, see [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   The created cluster can be connected by using kubectl. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

1.  Run the following command to query the internal endpoint of the Kubernetes API server.

    ```
    kubectl get endpoints kubernetes
    ```

    ![Internal endpoint of the Kubernetes API server](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6435359951/p31625.png)

2.  Create a file named kubernetes-public-service.yaml and copy the following content into the file. Set the value of the ip field to the endpoint that is obtained in Step 1.

    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: kubernetes-public
    spec:
      type: LoadBalancer
      ports:
      - name: https
        port: 443
        protocol: TCP
        targetPort: 6443
    ---
    apiVersion: v1
    kind: Endpoints
    metadata:
      name: kubernetes-public
      namespace: default
    subsets:
    - addresses:
      - ip: <API Service address>  # Specify the IP address of the internal endpoint that is obtained in Step 1.
      - name: https
        port: 6443
        protocol: TCP
    ```

3.  Run the following command to create a LoadBalancer type service with a public endpoint.

    ```
    kubectl apply -f kubernetes-public-service.yaml
    ```

4.  Run the following command to query the public IP address of the public-facing Server Load Balancer \(SLB\) instance that is created in Step 2. You can find the IP address in the EXTERNAL-IP field of the result.

    ```
    kubectl get service name
    ```

    **Note:** You must set name to the value same as name in the kubernetes-public-service.yaml file created in Step 2. In this example, the value is kubernetes-public.

    ![Query the public IP address of the public-facing SLB instance](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6435359951/p31800.png)

5.  Run the following command to query the Secret that is used to store the Service Account token. In this example, the Secret in the default namespace is queried.

    ```
     kubectl get secret --namespace=namespace
    ```

    ![Query the Secret](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6435359951/p31814.png)

6.  Run the following command to query the value of the token that is obtained in Step 5.

    ```
    kubectl get secret -n --namespace=namespace -o jsonpath={.data.token} | base64 -d
    ```

    **Note:** You must set namespace to the value same as namespace in Step 5.

7.  Run the following command to access the cluster of ACK Managed Edition.

    ```
    curl -k -H 'Authorization: Bearer token' https://service-ip
    ```

    **Note:**

    -   Set token to the value that is obtained in Step 6.
    -   Set service-ip to the public IP address \(the IP address in the EXTERNAL-IP field\) that is obtained in Step 4.

If the output is as shown in the following figure, it indicates that you are connected to the cluster.

![Connection established](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1393139061/p32026.png)

