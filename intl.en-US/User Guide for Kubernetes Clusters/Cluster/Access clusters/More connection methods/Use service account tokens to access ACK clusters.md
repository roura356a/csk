# Use service account tokens to access ACK clusters

This topic describes how to access Container Service for Kubernetes \(ACK\) clusters by using service account tokens. This method is applicable to all types of ACK cluster. In this example, a managed Kubernetes cluster is used.

-   A managed Kubernetes cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   The created cluster can be connected by using kubectl. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md).

1.  Run the following command to query the internal endpoint of the Kubernetes API server:

    ```
    kubectl get endpoints kubernetes
    ```

    ![Internal endpoint of the Kubernetes API server](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/6435359951/p31625.png)

2.  Create a kubernetes-public-service.yaml file and copy the following content to the file. Set the value of the ip field to the internal endpoint that is obtained in Step 1.

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
      - ip: <API Service address>  # Replace with the internal endpoint of the Kubernetes API server obtained in Step 1.
      ports:
      - name: https
        port: 6443
        protocol: TCP
    ```

3.  Run the following command to create a LoadBalancer Service with an external endpoint:

    ```
    kubectl apply -f kubernetes-public-service.yaml
    ```

4.  Run the following command to query the IP address of the Internet-facing Sever Load Balancer \(SLB\) instance. You can find the IP address in the EXTERNAL-IP field of the output.

    ```
    kubectl get service <name>
    ```

    **Note:** Set <name\> to the value of the name field in the kubernetes-public-service.yaml file created in Step 2. In this example, the value is kubernetes-public.

    ![Query the IP address of the Internet-facing SLB instance](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/6435359951/p31800.png)

5.  Run the following command to query the Secret that is used to store the service account token. In this example, the Secret in the default namespace is queried.

    ```
     kubectl get secret --namespace=<namespace>
    ```

    ![Query the Secret](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/6435359951/p31814.png)

6.  Run the following command to query the value of the token:

    ```
    kubectl get secret --namespace=<namespace>  <name> -o jsonpath={.data.token} | base64 -d
    ```

    **Note:** Set <namespace\> to the namespace that is used in Step 5. Set <name\> to the Secret that is returned in Step 5.

7.  Run the following command to access the managed Kubernetes cluster:

    ```
    curl -k -H 'Authorization: Bearer token' https://service-ip
    ```

    **Note:**

    -   Set token to the value that is obtained in Step 6.
    -   Set service-ip to the IP address of the Internet-facing SLB instance. You can find the IP address in the EXTERNAL-IP field of the output in Step 4.

If the output is as shown in the following figure, it indicates that you are connected to the cluster.

![Connection established](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/1393139061/p32026.png)

