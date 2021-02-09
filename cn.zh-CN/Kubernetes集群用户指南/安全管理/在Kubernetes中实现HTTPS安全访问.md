---
keyword: [HTTPS安全访问, HTTPS访问配置]
---

# 在Kubernetes中实现HTTPS安全访问

容器服务ACK集群支持多种应用访问的形式，最常见形式如`<SLB-Instance-IP>:<Port>`、`<NodeIP>:<NodePort>`和域名访问等。ACK集群默认不支持HTTPS访问，如果您希望能够通过HTTPS进行应用的访问，容器服务ACK和阿里云负载均衡服务为您提供安全的HTTPS访问。本文通过实际案例演示的HTTPS访问配置，帮助您在容器服务ACK中配置自己的证书。

-   [创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)。
-   创建集群的服务器证书，包括公钥证书和私钥。
    -   您可通过以下命令快速创建集群的服务器证书。

        ```
        openssl genrsa -out tls.key 2048
        ```

        输出：

        ```
        Generating RSA private key, 2048 bit long modulus
        ................................................................+++
        ........................................................................................+++
        e is 65537 (0x10001)
        ```

        ```
        ls
        ```

        输出：

        ```
        You are about to be asked to enter information that will be incorporated
        ...
        -----
        Country Name (2 letter code) [XX]:CN
        State or Province Name (full name) []:zhejiang
        Locality Name (eg, city) [Default City]:hangzhou
        Organization Name (eg, company) [Default Company Ltd]:alibaba
        Organizational Unit Name (eg, section) []:test
        Common Name (eg, your name or your server's hostname) []:foo.bar.com           #注意，您需要正确配置域名。
        Email Address []:a@alibaba.com
        ```

    -   您也可以选择购买阿里云签发证书。具体操作，请参见[选择阿里云签发证书](/cn.zh-CN/CLB用户指南/证书管理/创建证书/选择阿里云签发证书.md)。

根据访问的方式不同，当前可以分为两种配置证书的方式：

-   在前端SLB上配置证书。
-   在Ingress中配置证书。

## 在SLB上配置HTTPS证书

该方式有如下特点：

-   优点：证书配置在SLB上，为应用外部访问的入口，在集群内部进行应用的访问依然用的是HTTP访问方式。
-   缺点：需要维护较多的域名与IP地址的对应关系。
-   适用场景：应用不使用Ingress暴露访问方式，通过LoadBalancer类型的Service进行应用访问的暴露。

准备工作：

您已在该ACK集群中创建一个Nginx应用，该应用采用LoadBalancer类型的服务（Service）对外提供访问。更多信息，请参见[使用镜像创建无状态Deployment应用](/cn.zh-CN/Kubernetes集群用户指南/应用管理/使用镜像创建无状态Deployment应用.md)。

示例：

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**服务与路由** \> **服务**。

5.  选择集群的命名空间和服务，单击外部端点，您可通过`<SLB IP>:<Port>`的方式访问该应用。

    ![nginx](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3218782161/p241037.png)

6.  登录[负载均衡管理控制台](https://slb.console.aliyun.com/)。

7.  配置**SSL证书**。

    -   如果您是通过命令方式创建集群的服务器证书，您需要使用前提条件中创建的公钥证书和私钥上传非阿里云签发证书。具体操作，请参见[上传非阿里云签发证书](/cn.zh-CN/CLB用户指南/证书管理/创建证书/上传非阿里云签发证书.md)。
    -   如果您是通过购买方式获取阿里云签发证书，请跳过此步骤。关于创建阿里云签发证书的操作，请参见[选择阿里云签发证书](/cn.zh-CN/CLB用户指南/证书管理/创建证书/选择阿里云签发证书.md)。
    在证书列表中，找到目标证书名称下面的证书ID。

8.  在容器服务管理控制台的服务列表中，找到之前创建的服务，单击右侧**操作**列下的**更新**。

9.  在**更新服务**对话框中的注解区域，添加以下两个注解内容。

    ![注解](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3218782161/p241100.png)

    |注解|名称|值|
    |--|--|--|
    |注解一|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-protocol-port|https:443|
    |注解二|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-cert-id|$\{YOUR\_CERT\_ID\}|

    **说明：** 将$\{YOUR\_CERT\_ID\}替换成[步骤7配置SSL证书](#step_0js_m4a_04m)生成的证书ID。

    您还可以使用YAML方式添加注解内容，完整YAML示例如下：

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-protocol-port: "https:443"
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-cert-id: "${YOUR_CERT_ID}"
      name: nginx
      namespace: default
    spec:
      ports:
      - name: https
        port: 443
        protocol: TCP
        targetPort: 80
      - name: http
        port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      type: LoadBalancer
    ```

    **说明：** HTTPS的443端口对应的targetPort端口需要配置成HTTP的端口80。

10. 访问HTTPS的Nginx应用，在浏览器中输入`https://<slb-instance-ip>`并进行访问。

    ![https](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3218782161/p241101.png)


## 在Ingress上配置证书

该方法有以下特点：

-   优点：无需改动SLB的配置。每一个应用都可以通过Ingress管理自己的证书，互不干扰。
-   适用场景：每个应用都需要单独的证书进行访问，或者集群中存在需要证书才能访问的应用。

准备工作：

您已在该Kubernetes集群中创建一个Tomcat应用，该应用的服务（Service）采用ClusterIP的方式提供访问。本例中准备使用Ingress对外提供HTTPS访问服务。更多信息，请参见[使用镜像创建无状态Deployment应用](/cn.zh-CN/Kubernetes集群用户指南/应用管理/使用镜像创建无状态Deployment应用.md)。

示例：

1.  根据前提条件中准备好的证书执行以下命令创建Secret。

    **说明：** 在这里需要正确配置域名，否则后续通过HTTPS访问会有问题。

    ```
    kubectl create secret tls secret-https --key tls.key --cert tls.crt      
    ```

2.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

3.  在控制台左侧导航栏中，单击**集群**。

4.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

5.  在集群管理页左侧导航栏中，选择**命名空间与配额**，单击右上角**创建**。

6.  在创建路由对话框中，配置可HTTPS访问的路由，完成后单击**创建**。

    更多详细的路由配置信息，请参见[创建路由（Ingress）](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Ingress管理/Ingress基本操作.md)。本例中进行如下配置。

    -   **名称**：输入该路由的名称。
    -   **域名**：即是前面配置的正确域名，与ssl证书中配置的保持一致。
    -   **服务**：选择Tomcat应用对应的Service，端口为8080。
    -   **开启TLS**：开启TLS后，选择已创建的Secret。
    您也可采用YAML文件的方式创建路由（Ingress），本例对应的YAML示例文件如下。

    ```
    apiVersion: extensions/v1beta1
    
    kind: Ingress
    
    metadata:
    
       name: tomcat-https
    
    spec:
    
      tls:
    
      - hosts:
    
        - foo.bar.com
    
        secretName: secret-https
    
      rules:
    
      - host: foo.bar.com
    
        http:
    
          paths:
    
          - path: /
    
            backend:
    
              serviceName: tomcat-svc
    
              servicePort: 8080
    ```

7.  返回路由列表，查看创建的路由（Ingress），本例中域名为`foo.bar.com`，并查看端点和域名，您也可进入路由详情页进行查看。

    **说明：** 本例中以`foo.bar.com`作为测试域名，您需要在hosts文件中创建一条记录。

    ```
    47.110.119.203  foo.bar.com                   #其中IP地址即是路由的端点。
    ```

    ![路由](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0195659951/p161116.png)

8.  在浏览器中访问`https://foo.bar.com`。

    **说明：** 由于创建了TLS证书访问，所以要用HTTPS来进行域名访问，针对该应用，本例以`foo.bar.com`为示例，在本地进行解析。在具体使用场景中，请使用备案过的域名。

    ![路由访问](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0195659951/p13837.png)


