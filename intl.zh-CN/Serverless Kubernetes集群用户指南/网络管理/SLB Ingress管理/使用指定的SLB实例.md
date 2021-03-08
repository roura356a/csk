---
keyword: 指定的SLB实例
---

# 使用指定的SLB实例

本文介绍在创建的Ingress中通过Annotation指定了SLB ID场景下，如何使用该SLB提供Ingress的转发功能。

-   您已创建一个ASK集群，集群的VPC需要配置NAT网关，从而可以访问外网，下载容器镜像。具体操作，请参见[创建Serverless Kubernetes集群](/intl.zh-CN/Serverless Kubernetes集群用户指南/快速入门/创建Serverless Kubernetes集群.md)。
-   您已通过kubectl连接到集群。具体操作，请参见[通过kubectl连接Kubernetes集群](/intl.zh-CN/Serverless Kubernetes集群用户指南/集群管理/管理和访问集群/通过kubectl连接Kubernetes集群.md)。
-   您已经创建一个和ASK集群在同一个VPC的性能保障型规格（支持ENI）SLB实例 。
    -   如果您在ASK集群同VPC下已经有一个SLB实例，您可以直接登录[负载均衡管理控制台](https://slb.console.aliyun.com/slb/cn-hangzhou/slbs)的**实例管理**页面获取SLB ID。
    -   如果您没有SLB ID，您需要在集群同VPC下创建一个性能保障型SLB实例（例如：slb.s2.small），该实例类型可以是私网或公网。具体操作，请参见[创建负载均衡实例](/intl.zh-CN/传统型负载均衡CLB/CLB用户指南/实例/创建负载均衡实例.md)。
    -   本文操作示例中申请的是公网SLB实例。

## 操作步骤

**说明：** Ingress Controller会自动初始化SLB实例的80和443端口，请确保当前没有其他服务使用。

**步骤一：部署服务**

1.  创建并拷贝以下内容到tomcat-service.yml文件中，并执行`kubectl apply -f tomcat-service.yml`命令，部署一个**tomcat**测试应用。

    ```
    apiVersion: apps/v1 
    kind: Deployment
    metadata:
      name: tomcat
    spec:
      replicas: 1
      selector:
        matchLabels:
          run: tomcat
      template:
        metadata:
          labels:
            run: tomcat
        spec:
          containers:
          - image: tomcat:7.0
            imagePullPolicy: Always
            name: tomcat
            ports:
            - containerPort: 8080
              protocol: TCP
          restartPolicy: Always
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: tomcat
    spec:
      ports:
      - port: 8080
        protocol: TCP
        targetPort: 8080
      selector:
        run: tomcat
      clusterIP: None
    ```

    系统输出类似以下结果：

    ```
    deployment "tomcat" created
    service "tomcat" created
    ```

2.  执行以下命令查看应用状态。

    ```
    kubectl get svc,deploy tomcat
    ```

    系统输出类似以下结果：

    ```
    NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
    svc/tomcat   ClusterIP   <none>       <none>        8080/TCP   1m
    
    NAME            DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
    deploy/tomcat   1         1         1            1           1m
    ```


**步骤二：配置Ingress**

1.  创建并拷贝如下内容到tomcat-ingress.yml文件中，并执行`kubectl apply -f tomcat-ingress.yml`命令，配置Ingress。

    ```
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: tomcat-ingress
      annotations:
        # 配置使用指定的SLB实例（SLB ID）。
        service.beta.kubernetes.io/alicloud-loadbalancer-id: lb-xxxxxxxxxx            ##替换为你的SLB ID。
        service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners: "true"
    spec:
      rules:
      # 配置七层域名。
      - host: bar.foo.com
        http:
          paths:
          # 配置Context Path。
          - path: /
            backend:
              serviceName: tomcat
              servicePort: 8080
    ```

    系统输出类似以下结果：

    ```
    ingress "tomcat-ingress" created
    ```

2.  执行以下命令获取SLB实例IP。

    ```
    kubectl get ing tomcat-ingress
    ```

    系统输出类似以下结果：

    ```
    NAME             HOSTS         ADDRESS        PORTS     AGE
    tomcat-ingress   bar.foo.com   47.168.XX.XX   80, 443   1m
    ```


**步骤三：访问服务**

**说明：** 目前需要您自行将域名解析到SLB实例IP上。

本例在域名中添加一条DNS域名解析规则，用于测试服务访问。建议您在工作环境中对域名进行备案。

```
47.168.XX.XX    bar.foo.com
```

-   通过浏览器访问**tomcat**服务。

    ![S10](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3322584161/p245328.png)

-   通过命令行方式访问**tomcat**服务。

    ```
    curl -k -H "Host: bar.foo.com" https://47.168.XX.XX
    ```


