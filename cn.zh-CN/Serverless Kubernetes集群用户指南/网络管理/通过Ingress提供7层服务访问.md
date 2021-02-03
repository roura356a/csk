# 通过Ingress提供7层服务访问

在阿里云Serverless Kubernetes集群中，您可以通过LoadBalancer Service对外提供四层服务访问，同样您也可以通过Ingress来对外提供七层服务访问，下面介绍如何在Serverless Kubernetes集群中提供七层域名服务访问。

-   您已创建一个Serverless集群，集群的VPC需要配置NAT网关，从而访问外网，下载容器镜像，详情请参见[创建Serverless Kubernetes集群](/cn.zh-CN/Serverless Kubernetes集群用户指南/快速入门/创建Serverless Kubernetes集群.md)。
-   您已通过kubectl连接到集群，请参见[通过kubectl连接Kubernetes集群](/cn.zh-CN/Serverless Kubernetes集群用户指南/集群管理/管理和访问集群/通过kubectl连接Kubernetes集群.md)。

## 使用说明

-   不指定SLB实例情况下系统会自动帮您生成一个公网SLB实例。
-   SLB实例默认前端监听端口为80（HTTP协议）和443（HTTPS协议）。
-   SLB实例HTTPS证书默认会初始化为第一个创建的Ingress配置的TLS证书，否则会初始化为系统默认证书。

    **说明：** 提供Ingress服务的SLB实例由Controller完全接管，您不能自行在SLB控制台上进行任何修改，您的任何修改都有可能造成服务异常。

-   当您指定使用已存在的SLB实例时，要求该SLB实例规格必须是性能保障型（支持ENI），同时确保80和443端口当前没有其他服务使用。

## 使用默认生成的SLB实例

当您不指定SLB实例时，系统会在第一个Ingress创建时自动生成一个性能保障型的公网SLB实例。

1.  部署测试服务。

    1.  创建并拷贝如下内容到cafe-service.yaml文件中，并执行`kubectl apply -f cafe-service.yaml`命令，部署一个coffee service和tea service。

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

        当界面呈现如下结果时，表示部署完成。

        ```
        deployment "coffee" created
        service "coffee-svc" created
        deployment "tea" created
        service "tea-svc" created
        ```

    2.  执行`kubectl get svc,deploy`命令，查看服务状态。

        ```
        NAME             TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
        svc/coffee-svc   ClusterIP   <none>       <none>        80/TCP    1m
        svc/tea-svc      ClusterIP   <none>       <none>        80/TCP    1m
        
        NAME            DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
        deploy/coffee   2         2         2            2           1m
        deploy/tea      1         1         1            1           1m
        ```

2.  配置Ingress。

    1.  创建并拷贝如下内容到`cafe-ingress.yaml`文件中，并执行`kubectl apply -f cafe-ingress.yaml`命令，通过Ingress配置coffee service和tea service对外暴露的域名和Path路径。

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

        当界面呈现如下结果时，表示Ingress配置完成。

        ```
        ingress "cafe-ingress" created
        ```

    2.  执行`kubectl get ing`，获取SLB实例IP。

        ```
        NAME           HOSTS         ADDRESS          PORTS     AGE
        cafe-ingress   foo.bar.com   139.***.**.***   80        1m
        ```

3.  测试服务访问。

    **说明：** 目前我们需要自行将域名解析到SLB实例IP上。

    本例中在hosts中添加一条DNS域名解析规则，用于测试服务访问。建议您在工作环境中对域名进行备案。

    ```
    139.***.**.***    foo.bar.com
    ```

    -   通过浏览器测试访问coffee服务。

        ![访问coffee服务](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7648649951/p10319.png)

    -   通过命令行方式测试访问coffee服务。

        ```
        curl -H "Host: foo.bar.com" http://139.***.**.***/coffee
        ```

    -   通过浏览器测试访问tea服务。

        ![访问tea服务](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7648649951/p10320.png)

    -   通过命令行方式测试访问tea服务。

        ```
        curl -H "Host: foo.bar.com" http://139.***.**.***/tea
        ```


## 使用指定的SLB实例

您可以通过注释`service.beta.kubernetes.io/alicloud-loadbalancer-id`来指定使用已存在的SLB实例，但要求该SLB实例必须为性能保障型规格（支持ENI）。

**说明：** 系统会自动初始化SLB实例的80和443端口，请确保当前没有其他服务使用。

1.  部署测试服务。

    1.  创建并拷贝如下内容到`tomcat-service.yml`文件中，并执行`kubectl apply -f tomcat-service.yml`命令，部署一个tomcat测试应用。

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

        当界面呈现如下结果时，表示tomcat应用部署完成。

        ```
        deployment "tomcat" created
        service "tomcat" created
        ```

    2.  执行`kubectl get svc,deploy tomcat`，查看应用的状态。

        ```
        NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
        svc/tomcat   ClusterIP   <none>       <none>        8080/TCP   1m
        
        NAME            DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
        deploy/tomcat   1         1         1            1           1m
        ```

2.  申请SLB实例。

    您需要在集群同Region下自行申请一个**性能保障型**SLB实例（例如slb.s2.small），可以是私网也可以是公网（依据具体需求）。参见[创建负载均衡实例](/cn.zh-CN/传统型负载均衡CLB/用户指南/实例/创建负载均衡实例.md)。本例中申请一个公网SLB实例，记录SLB实例的ID。

    ![申请SLB实例](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7648649951/p10321.png)

3.  配置TLS证书。

    您需要配置TLS证书实现HTTPS访问。

    1.  执行如下命令，生成测试TLS证书。

        ```
        openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=bar.foo.com/O=bar.foo.com"# 创建TLS证书Secret
        ```

        ```
        Generating a 2048 bit RSA private key
        .........................................................+++
        ..................................................+++
        writing new private key to 'tls.key'
        -----
        ```

        ```
        kubectl create secret tls cert-example --key tls.key --cert tls.crt
        ```

        ```
        secret "cert-example" created
        ```

    2.  执行如下命令，查看新建的TLS证书。

        ```
        kubectl get secret cert-example
        ```

        ```
        NAME           TYPE                DATA      AGE
        cert-example   kubernetes.io/tls   2         12s
        ```

    **说明：** 系统自动依据第一个创建的Ingress的TLS证书来初始化SLB的HTTPS默认证书，若需要修改HTTPS默认证书，可在SLB控制台自行修改；若需配置多个证书，可在SLB控制台HTTPS监听扩展域名下自行添加。

4.  配置Ingress。

    1.  创建并拷贝如下内容到`tomcat-ingress.yml`文件中，并执行`kubectl apply -f tomcat-ingress.yml`命令，配置Ingress。

        ```
        apiVersion: extensions/v1beta1
        kind: Ingress
        metadata:
          name: tomcat-ingress
          annotations:
            # 配置使用指定的SLB实例（SLB ID）
            service.beta.kubernetes.io/alicloud-loadbalancer-id: lb-xxxxxxxxxx            ##替换为你的SLB ID
            service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners: "true"
        spec:
          tls:
          - hosts:
            - bar.foo.com
            # 配置TLS证书
            secretName: cert-example
          rules:
          # 配置七层域名
          - host: bar.foo.com
            http:
              paths:
              # 配置Context Path
              - path: /
                backend:
                  serviceName: tomcat
                  servicePort: 8080
        ```

        当界面呈现如下结果时，表示tomcat-ingress配置完成。

        ```
        ingress "tomcat-ingress" created
        ```

    2.  执行`kubectl get ing tomcat-ingress`命令，获取SLB IP地址。

        ```
        NAME             HOSTS         ADDRESS        PORTS     AGE
        tomcat-ingress   bar.foo.com   47.***.**.**   80, 443   1m
        ```

5.  测试服务访问。

    **说明：** 目前我们需要自行将域名解析到SLB实例IP上。

    本例中在hosts中添加一条DNS域名解析规则，用于测试服务访问。建议您在工作环境中对域名进行备案。

    ```
    47.***.**.**   bar.foo.com
    ```

    -   通过浏览器测试访问tomcat服务。

        ![访问tomcat](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7648649951/p10322.png)

    -   通过命令行方式测试访问tomcat服务。

        ```
        curl -k -H "Host: bar.foo.com" https://47.***.**.**
        ```


