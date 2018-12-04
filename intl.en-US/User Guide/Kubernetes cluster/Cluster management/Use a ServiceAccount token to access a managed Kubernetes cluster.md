# Use a ServiceAccount token to access a managed Kubernetes cluster {#task_o3m_vhj_5fb .task}

This topic describes how to use a ServiceAccount token to access a managed Kubernetes cluster.

-   You have created a managed Kubernetes cluster. For more information, see [Create a managed Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a managed Kubernetes cluster.md#).
-   You have connected to the managed Kubernetes cluster by using kubectl, see [Connect to a Kubernetes cluster by using kubectl](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#).

1.  Run the following command to obtain the API server intranet endpoint: 

    ```
    $ kubectl get endpoints kubernetes
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/62640/154392631431625_en-US.png)

2.  Create a file named kubernetes-public-service.yaml and set the ip parameter to the intranet endpoint obtained in step 1. 

    ```
    kind: Service
    apiVersion: v1
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
      - ip: <API Service address>  #Set this parameter to the intranet endpoint obtained in step 1.
      ports:
      - name: https
        port: 6443
        protocol: TCP
    ```

3.  Run the following command to deploy the API server Internet endpoint: 

    ```
    $ kubectl apply -f kubernetes-public-service.yaml
    ```

4.  Run the following command to obtain the Internet SLB address, namely, EXTERNAL-IP: 

    ```
    $ kubectl get service name
    ```

    **Note:** The name parameter in the command and the name parameter in the kubernetes-public-service.yaml file of step 2 must be set to the same value. In this example, this parameter is set to kubernetes-public.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/62640/154392631431800_en-US.png)

5.  Run the following command to view the corresponding secret of the ServiceAccount \(in this example, the namespace parameter is set to default\): 

    ```
    $ kubectl get secret --namespace=namespace
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/62640/154392631431814_en-US.png)

6.  Run the following command to obtain a token value: 

    ```
    $ kubectl get secret -n --namespace=namespace -o jsonpath={.data.token} | base64 -d
    ```

    **Note:** The namespace parameter in this command and the namespace parameter in step 5 must be set to the same value.

7.  Run the following command to access the managed Kubernetes cluster: 

    ```
    $ curl -k -H 'Authorization: Bearer token' https://service-ip
    ```

    **Note:** 

    -   The value of token is the token value obtained in step 6.
    -   The value of service-ip is the Internet SLB address obtained in step 4, that is, EXTERNAL-IP.

After you run the command, the following message is displayed, indicating that you have connected to the cluster.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/62640/154392631432026_en-US.png)

