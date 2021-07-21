# 使用ServiceAccount Token访问Kubernetes集群

本文介绍如何使用ServiceAccount Token访问Kubernetes集群，该功能适用于所有的Kubernetes集群，本例中为托管版集群。

-   您已经成功创建一个Kubernetes托管版集群，参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   您可以通过Kubectl连接到Kubernetes托管版集群，参见[通过Kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl管理Kubernetes集群.md)。

1.  执行以下命令获取API Server内网连接端点。

    ```
    kubectl get endpoints kubernetes
    ```

    ![内网连接端点](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1475659951/p31625.png)

2.  创建kubernetes-public-service.yaml文件，ip替换为步骤1查询到的API Server内网连接端点。

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
      - ip: <API Service address>  #替换为步骤1查询到的API Server内网连接端点
      ports:
      - name: https
        port: 6443
        protocol: TCP
    ```

3.  执行以下命令，部署公网Endpoints。

    ```
    kubectl apply -f kubernetes-public-service.yaml
    ```

4.  执行以下命令，获取公网SLB地址，即EXTERNAL-IP。

    ```
    kubectl get service <name>
    ```

    **说明：** 此处的<name\>与步骤2kubernetes-public-service.yaml文件中的name的值保持一致，本文为kubernetes-public。

    ![获取公网SLB地址](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1475659951/p31800.png)

5.  执行以下命令，查看ServiceAccount对应的Secret，本文的namespace以default为例。

    ```
     kubectl get secret --namespace=<namespace>
    ```

    ![查看密钥](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1475659951/p31814.png)

6.  执行以下命令，获取Token值。

    ```
    kubectl get secret --namespace=<namespace>  <name> -o jsonpath={.data.token} | base64 -d
    ```

    **说明：** 此处的<namespace\>与步骤5中的<namespace\>保持一致。<name\>是步骤5中找到ServiceAccount对应的Secret名称。

7.  执行以下命令，访问托管版Kubernetes集群。

    ```
    curl -k -H 'Authorization: Bearer token' https://service-ip
    ```

    **说明：**

    -   token为步骤6获取的Token值。
    -   service-ip为步骤4获取的公网SLB地址，即EXTERNAL-IP。

执行命令后，出现以下信息，表明连接成功。

![连接成功](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0483139061/p32026.png)

