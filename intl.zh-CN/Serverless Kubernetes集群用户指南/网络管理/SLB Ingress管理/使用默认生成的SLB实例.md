---
keyword: 公网SLB实例
---

# 使用默认生成的SLB实例

如果您创建的Ingress没有通过Annotation指定SLB ID，ASK会自动生成一个公网SLB实例。本文介绍在该场景下如何使用该SLB提供Ingress的转发功能。

-   您已创建一个ASK集群，集群的VPC需要配置NAT网关，从而可以访问外网，下载容器镜像。具体操作，请参见[创建Serverless Kubernetes集群](/intl.zh-CN/Serverless Kubernetes集群用户指南/快速入门/创建Serverless Kubernetes集群.md)。
-   您已通过kubectl连接到集群。具体操作，请参见[通过kubectl连接Kubernetes集群](/intl.zh-CN/Serverless Kubernetes集群用户指南/集群管理/管理和访问集群/通过kubectl连接Kubernetes集群.md)。

## 操作步骤

**步骤一：部署服务**

1.  创建并拷贝以下内容到cafe-service.yaml文件中，并执行`kubectl apply -f cafe-service.yaml`命令，分别部署一个**coffee**服务和**tea**服务。

    ```
    apiVersion: apps/v1 
    kind: Deployment
    metadata:
      name: coffee
    spec:
      replicas: 2
      selector:
        matchLabels:
          app: coffee
      template:
        metadata:
          labels:
            app: coffee
        spec:
          containers:
          - name: coffee
            image: registry.cn-hangzhou.aliyuncs.com/acs-sample/nginxdemos:latest
            ports:
            - containerPort: 80
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: coffee-svc
    spec:
      ports:
      - port: 80
        targetPort: 80
        protocol: TCP
      selector:
        app: coffee
      clusterIP: None
    ---
    apiVersion: apps/v1 
    kind: Deployment
    metadata:
      name: tea
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: tea 
      template:
        metadata:
          labels:
            app: tea 
        spec:
          containers:
          - name: tea 
            image: registry.cn-hangzhou.aliyuncs.com/acs-sample/nginxdemos:latest
            ports:
            - containerPort: 80
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: tea-svc
      labels:
    spec:
      ports:
      - port: 80
        targetPort: 80
        protocol: TCP
      selector:
        app: tea
      clusterIP: None
    ```

    系统输出类似以下结果：

    ```
    deployment "coffee" created
    service "coffee-svc" created
    deployment "tea" created
    service "tea-svc" created
    ```

2.  执行以下命令查看服务状态。

    ```
    kubectl get svc,deploy
    ```

    系统输出类似以下结果：

    ```
    NAME             TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
    svc/coffee-svc   ClusterIP   <none>       <none>        80/TCP    1m
    svc/tea-svc      ClusterIP   <none>       <none>        80/TCP    1m
    
    NAME            DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
    deploy/coffee   2         2         2            2           1m
    deploy/tea      1         1         1            1           1m
    ```


**步骤二：配置Ingress**

1.  创建并拷贝以下内容到cafe-ingress.yaml文件中，并执行`kubectl apply -f cafe-ingress.yaml`命令，通过Ingress配置**coffee**和**tea**服务对外暴露的域名和path路径。

    ```
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: cafe-ingress
    spec:
      rules:
      # 配置七层域名
      - host: foo.bar.com
        http:
          paths:
          # 配置Context Path
          - path: /tea
            backend:
              serviceName: tea-svc
              servicePort: 80
          # 配置Context Path
          - path: /coffee
            backend:
              serviceName: coffee-svc
              servicePort: 80
    ```

    系统输出类似以下结果：

    ```
    ingress "cafe-ingress" created
    ```

2.  执行以下命令获取SLB实例IP。

    ```
    kubectl get ing
    ```

    系统输出类似以下结果：

    ```
    NAME           HOSTS         ADDRESS          PORTS     AGE
    cafe-ingress   foo.bar.com   139.168.XX.XX    80        1m
    ```


**步骤三：访问服务**

**说明：** 目前需要您自行将域名解析到SLB实例IP上。

本例在域名中添加一条DNS域名解析规则，用于测试服务访问。建议您在工作环境中对域名进行备案。

```
139.168.XX.XX    foo.bar.com
```

访问**coffee**服务。

-   通过浏览器访问**coffee**服务。

    ![S6](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9488574161/p245248.png)

-   通过命令行方式访问**coffee**服务。

    ```
    curl -H "Host: foo.bar.com" http://139.168.XX.XX/coffee
    ```


访问**tea**服务。

-   通过浏览器测试访问**tea**服务。

    ![S7](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9488574161/p245250.png)

-   通过命令行方式测试访问**tea**服务。

    ```
    curl -H "Host: foo.bar.com" http://139.168.XX.XX/tea
    ```


