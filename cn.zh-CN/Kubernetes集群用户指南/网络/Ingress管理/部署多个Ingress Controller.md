---
keyword: [部署独立的Nginx Ingress Controller, 容器服务Kubernetes集群]
---

# 部署多个Ingress Controller

本文介绍如何在阿里云容器服务Kubernetes集群中同时部署多套独立的Nginx Ingress Controller对外提供不同的服务访问。

-   已创建Kubernetes集群。具体操作，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   已通过kubectl连接kubernetes集群。具体操作，请参见[通过kubectl工具连接集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl工具连接集群.md)。

您可以通过调整阿里云容器服务Kubernetes集群中默认的Nginx Ingress Controller配置，使用私网SLB实例。更多信息，请参见[部署Ingress Controller使用私网SLB](/cn.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/部署Ingress Controller使用私网SLB.md)，文中提到的两种模式可以满足大部分需求场景。但对于一些特殊场景，例如，集群内有部分公网服务需要通过公网Ingress方式来对外暴露提供访问，但是又有部分私网服务仅仅只希望对同VPC内非Kubernetes集群内的服务提供访问，而又不允许能被公网访问到，您可以通过部署两套独立的Nginx Ingress Controller服务，其前端绑定不同网络类型的SLB实例来满足这类需求场景。

![1](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/9063659951/p81390.png)

## 部署新的Nginx Ingress Controller服务

当您成功创建一个ACK集群后，默认情况下，集群内部已经部署了一套拥有2个Pod副本的Nginx Ingress Controller服务，其前端挂载在一个公网SLB实例上。

您可以通过以下步骤在ACK集群中再部署一套完全独立的Nginx Ingress Controller服务。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  在**阿里云应用**页签，选中并单击**ack-ingress-nginx**。

4.  在应用目录-ack-ingress-nginx页面，单击**参数**页签并在右侧**创建**面板中设置目标集群、命名空间及发布名称，然后单击**创建**。

    主要参数设置如下表。

    |参数|描述|
    |--|--|
    |controller.image.repository|ingress-nginx镜像地址。对于国外地域，建议更改为对应的Region ID。|
    |controller.image.tag|ingress-nginx镜像版本，请参见[Nginx Ingress Controller](/cn.zh-CN/产品发布记录/组件介绍与变更记录/网络/Nginx Ingress Controller.md)。|
    |controller.ingressClass|设置该Ingress Controller可监听的目标Ingress Class标识。 **说明：** 同一个集群中不同套Ingress Controller监听的Ingress Class标识必须唯一，且不能设置为nginx关键字（nginx是集群默认Ingress Controller的监听标识）。 |
    |controller.replicaCount|设置该Ingress Controller Pod副本数。|
    |controller.service.enabled|是否开启公网SLB访问，不需要开启则设置为false。|
    |controller.service.internal.enabled|是否开启私网SLB访问，需要开启则设置为true。|
    |controller.kind|设置IngressController部署形态，可选值：Deployment和DaemonSet。|

5.  查看部署的Nginx Ingress Controller服务。

    1.  在控制台左侧导航栏中，单击**集群**。

    2.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

    3.  在集群管理页左侧导航栏中，选择**应用** \> **Helm**。

        在**Helm**页面可以看到新的Nginx Ingress Controller服务已经成功部署。

        ![Nginx Ingress](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/0163659951/p158138.png)


## 访问测试

本文部署一个测试应用，并配置通过新部署的Nginx Ingress Controller来对外暴露提供服务访问。

1.  部署一个Nginx测试应用。

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx
    spec:
      replicas: 1
      selector:
        matchLabels:
          run: nginx
      template:
        metadata:
          labels:
            run: nginx
        spec:
          containers:
          - image: nginx
            imagePullPolicy: Always
            name: nginx
            ports:
            - containerPort: 80
              protocol: TCP
          restartPolicy: Always
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      sessionAffinity: None
      type: NodePort
    ```

2.  通过Ingress来对外暴露提供服务访问。

    ```
    apiVersion: networking.k8s.io/v1beta1
    kind: Ingress
    metadata:
      name: nginx
      annotations:
        #注意这里要设置为您前面配置的INGRESS_CLASS。
        kubernetes.io/ingress.class: "<YOUR_INGRESS_CLASS>"
    spec:
      rules:
      - host: foo.bar.com
        http:
          paths:
          - path: /
            backend:
              serviceName: nginx
              servicePort: 80
    ```

    **说明：** 您需要配置注释`kubernetes.io/ingress.class`。

    在完成部署应用后，执行以下操作，查看该Ingress资源对应的端点IP地址与新部署的Nginx Ingress Controller服务的IP地址。

    -   执行以下命令查看集群默认部署的nginx-ingress-lb服务对应的公网SLB地址。

        ```
         kubectl -n kube-system get svc nginx-ingress-lb
        ```

        预期输出：

        ```
        NAME               TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
        nginx-ingress-lb   LoadBalancer   172.19.7.30   47.95.97.115   80:31429/TCP,443:32553/TCP   2d
        ```

    -   执行以下命令查看新部署的nginx-ingress-lb服务对应的公网SLB地址。

        ```
        kubectl -n <YOUR_NAMESPACE> get svc nginx-ingress-lb
        ```

        预期输出：

        ```
        NAME               TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
        nginx-ingress-lb   LoadBalancer   172.19.6.227   39.105.252.62   80:30969/TCP,443:31325/TCP   39m
        ```

    -   执行以下命令查看该应用的Ingress配置。

        ```
        kubectl get ing
        ```

        预期输出：

        ```
        NAME      HOSTS         ADDRESS         PORTS     AGE
        nginx     foo.bar.com   39.105.252.62   80        5m
        ```

    通过以上预期输出可得，该Ingress资源对应的端点IP地址与新部署的Nginx Ingress Controller服务的一致。

3.  分别通过集群默认的Nginx Ingress Controller服务和新部署的Nginx Ingress Controller服务来访问该应用。

    ```
     # 通过集群默认的Nginx Ingress Controller服务访问该应用（预期返回404）。
      curl -H "Host: foo.bar.com" http://47.95.97.115
    default backend - 404                                                                                                                                                                                        
      # 通过新部署的Nginx Ingress Controller服务访问该应用（预期返回nginx页面）。curl -H "Host: foo.bar.com" http://39.105.252.62
    <!DOCTYPE html>
    <html>
    <head>
    <title>Welcome to nginx!</title>
    <style>
        body {
            width: 35em;
            margin: 0 auto;
            font-family: Tahoma, Verdana, Arial, sans-serif;
        }
    </style>
    </head>
    <body>
    <h1>Welcome to nginx!</h1>
    <p>If you see this page, the nginx web server is successfully installed and
    working. Further configuration is required.</p>
    
    <p>For online documentation and support please refer to
    <a href="http://nginx.org/">nginx.org</a>.<br/>
    Commercial support is available at
    <a href="http://nginx.com/">nginx.com</a>.</p>
    
    <p><em>Thank you for using nginx.</em></p>
    </body>
    </html>
    ```


从上文测试访问情况可以看到，通过不同的Nginx Ingress Controller暴露的服务彼此完全是独立的，这特别适用于同一集群内部分服务需要提供公网访问能力，但又有部分服务仅仅只希望为同VPC内非Kubernetes集群的其他服务提供访问的场景。

[部署高可靠Ingress Controller](/cn.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/部署高可靠Ingress Controller.md)

[部署Ingress Controller使用私网SLB](/cn.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/部署Ingress Controller使用私网SLB.md)

