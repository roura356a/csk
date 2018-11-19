# 使用ServiceAccount Token访问托管版Kubernetes集群 {#task_o3m_vhj_5fb .task}

本文介绍如何使用ServiceAccount Token访问托管版Kubernetes集群。

-   您已经成功创建一个 Kubernetes 托管版集群，参见[创建Kubernetes 托管版集群](intl.zh-CN/用户指南/Kubernetes 集群/集群管理/创建Kubernetes 托管版集群.md#)。
-   您可以通过Kubectl连接到Kubernetes 托管版集群，参见[通过 kubectl 连接 Kubernetes 集群](intl.zh-CN/用户指南/Kubernetes 集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)。

1.  执行以下命令获取API Server 内网连接端点。 

    ```
    $ kubectl get endpoints kubernetes
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/62640/154259728531625_zh-CN.png)

2.  创建kubernetes-public-service.yaml文件，ip替换为步骤1查询到的API Server 内网连接端点。 

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
      - ip: <API Service address>  #替换为步骤1查询到的API Server内网连接端点
      ports:
      - name: https
        port: 6443
        protocol: TCP
    ```

3.  执行以下命令，获取API Server 公网连接端点。 

    ```
    $ kubectl apply -f kubernetes-public-service.yaml
    ```

4.  执行以下命令，获取公网SLB地址，即EXTERNAL-IP。 

    ```
    $ kubectl get service name
    ```

    **说明：** 此处的name与步骤2kubernetes-public-service.yaml文件中的name保持一致，本文为kubernetes-public。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/62640/154259728531800_zh-CN.png)

5.  执行以下命令，查看ServiceAccount对应的Secret，本文的namespace以default为例。 

    ```
    $ kubectl get secret --namespace=namespace
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/62640/154259728531814_zh-CN.png)

6.  执行以下命令，获取token值。 

    ```
    $ kubectl get secret -n --namespace=namespace -o jsonpath={.data.token} | base64 -d
    ```

    **说明：** 此处的namespace与步骤5中的namespace保持一致。

7.  执行以下命令，访问托管版Kubernetes集群。 

    ```
    $ curl -k -H 'Authorization: Bearer token' https://service-ip
    ```

    **说明：** 

    -   token为步骤6获取的token值。
    -   service-ip为步骤4获取的公网SLB地址，即EXTERNAL-IP。

执行命令后，出现以下信息，表明连接成功。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/62640/154259728532026_zh-CN.png)

