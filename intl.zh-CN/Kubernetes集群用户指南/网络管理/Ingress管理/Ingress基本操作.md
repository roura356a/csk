---
keyword: [创建Ingress, 更新Ingress, 删除Ingress]
---

# Ingress基本操作

Ingress是Kubernetes中的一个资源对象，用来管理集群外部访问集群内部服务的方式。您可以通过Ingress资源来配置不同的转发规则，从而根据转发规则访问集群内Pod。本文介绍如何通过控制台和Kubectl方式创建、查看、更新和删除Ingress。

[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)

## 控制台操作指导

**创建Ingress**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)[容器服务管理控制台](https://partners-intl.console.aliyun.com/#/cs)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**服务与路由** \> **路由**。

5.  在路由页面单击**创建**，在**创建**对话框配置路由名称，本例中为nginx-ingress。

6.  配置路由规则。

    路由规则是指授权入站到达集群服务的规则，支持HTTP或HTTPS规则，配置项包括域名（虚拟主机名称）、URL路径、服务名称、端口配置和路由权重等。

    本例中配置添加一条复杂的路由规则，配置集群默认的测试域名和虚拟主机名称，展示基于域名的路由服务。

    ![路由规则](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1385659951/p10776.png)

    -   基于默认域名的简单路由，即使用集群的默认域名对外提供访问服务。
        -   **域名配置**：使用集群的默认域名，本例中是`test.[cluster-id].[region-id].alicontainer.com`。

            在创建路由对话框中，会显示该集群的默认域名，域名格式是`*.[cluster-id].[region-id].alicontainer.com`；您也可在集群的基本信息页面中获取。

        -   **服务配置**：配置服务的访问路径、名称以及端口。
            -   访问路径配置：您可指定服务访问的URL路径，默认为根路径/，本例中不做配置。每个路径（Path）都关联一个Backend（服务），在阿里云SLB将流量转发到Backend之前，所有的入站请求都要先匹配域名和路径。
            -   服务配置：支持服务名称、端口、服务权重等配置，即Backend配置。同一个访问路径下，支持多个服务的配置，Ingress的流量会被切分，并被转发到它所匹配的Backend。
    -   基于域名的简单扇出路由。本例中使用一个虚拟的主机名称作为测试域名对外提供访问服务，为两个服务配置路由权重，并为其中一个服务设置灰度发布规则。若您在生产环境中，可使用成功备案的域名提供访问服务。
        -   **域名配置**：本例中使用测试域名`foo.bar.com`。

            您需要修改HOSTS文件添加一条域名映射规则。

            ```
            118.178.XX.XX foo.bar.com       #IP即是Ingress的Address
            ```

        -   **服务配置**：配置服务的访问路径、服务名称、服务端口和服务权重。
            -   访问路径配置：指定服务访问的URL路径。本例中不做配置，保留根路径/。
            -   服务名称：本例中设置新旧两个服务new-nginx和old-nginx。
            -   服务端口：暴露80端口。
            -   权重设置：设置该路径下多个服务的权重。服务权重采用相对值计算方式，默认值为100，如本例中所示，新旧两个版本的服务权重值都是50，则表示两个服务的权重比例都是50%。
7.  配置TLS。

    选中**开启**TLS，配置安全的路由服务。具体请参见[Ingress支持](/intl.zh-CN/Kubernetes集群用户指南/网络管理/Ingress管理/Ingress高级用法.md)。

    -   您可选择使用已有密钥。
        1.  登录Master节点，创建tls.key和tls.crt。

            ```
            openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=foo.bar.com/O=foo.bar.com"
            ```

        2.  创建一个Secret。

            ```
            kubectl create secret tls foo.bar --key tls.key --cert tls.crt
            ```

        3.  执行命令`kubectl get secret`，您可看到该Secret已经成功创建。在Web界面可选择创建的foo.bar这个Secret。

            ![配置TLS](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1385659951/p10779.png)

    -   您可选择在TLS界面上利用已创建的TLS私钥和证书，一键创建Secret。
        1.  登录Master节点，创建tls.key和tls.crt。

            ```
            openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=foo.bar.com/O=foo.bar.com"
            ```

        2.  执行`vim tls.key`和`vim tls.crt`获取生成的私钥和证书。
        3.  将证书和私钥的内容复制到TLS新建密钥的面板。

            ![创建密钥](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1385659951/p10781.png)

8.  配置灰度发布。

    容器服务支持多种流量切分方式，适用于灰度发布以及AB测试场景。

    1.  基于Request Header的流量切分。
    2.  基于Cookie的流量切分。
    3.  基于Query Param的流量切分。
    设置灰度规则后，请求头中满足灰度发布匹配规则的请求才能被路由到新版本服务new-nginx中。如果该服务设置了100%以下的权重比例，满足灰度规则的请求会继续依据权重比例路由到对应服务。

    在本例中，设置Header请求头带有`foo=^bar$`的灰度发布规则，仅带有该请求头的客户端请求才能访问到new-nginx服务。

    ![访问服务](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1385659951/p10777.png)

    -   **服务**：路由规则配置的服务。
    -   **类型**：支持Header（请求头）、Cookie和Query（请求参数）的匹配规则。
    -   **名称和匹配值**：用户自定义的请求字段，名称和匹配值为键值对。
    -   **匹配规则**：支持正则匹配和完全匹配。
    **说明：** 灰度发布策略只支持2个Service配置。

9.  配置注解。

    单击**添加**，在**类型**下拉框中，您可以：

    -   选择**自定义注解**：输入注解名称和值，即Ingress的Annotation键值对，Ingress的注解请参见[Annotations](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/)。
    -   选择**Ingress-Nginx**：根据名称选择要配置的注解。

        您可为路由添加一条典型的重定向注解。即`nginx.ingress.kubernetes.io/rewrite-target：/`，表示将/path路径重定向到后端服务能够识别的根路径/上面。

        **说明：** 本例中未对服务配置访问路径，因此不需要配置重定向注解。重定向注解的作用是使Ingress以根路径转发到后端，避免访问路径错误配置而导致的404错误。

10. 添加标签。

    标签的作用是为Ingress添加对应的标签，标示该Ingress的特点。

    ![添加标签](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1385659951/p10783.png)

11. 最后单击**创建**，返回路由列表。

    等待一段时间，可以看到一条路由。


**查看Ingress**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)[容器服务管理控制台](https://partners-intl.console.aliyun.com/#/cs)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**服务与路由** \> **路由**。

5.  在路由页面单击目标路由**操作**列的**详情**。

    在路由详情页面查看路由详细信息。


**更新Ingress**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)[容器服务管理控制台](https://partners-intl.console.aliyun.com/#/cs)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**服务与路由** \> **路由**。

5.  在路由页面单击目标路由**操作**列的**变更**。

6.  在**更新**对话框中修改路由参数，然后单击**更新**。


**删除Ingress**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)[容器服务管理控制台](https://partners-intl.console.aliyun.com/#/cs)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**服务与路由** \> **路由**。

5.  在路由页面选择目标路由**操作**列的**更多** \> **删除**。

6.  在**提示**对话框中单击**确定**。


## Kubectl操作指导

**创建Ingress**

1.  创建Deployment和Service。

    在创建ingress资源之前，必须创建外部访问Kubernetes集群中的服务。

    1.  使用以下内容，创建test-deployment-service.yaml。

        以下Yaml文件中包含了一个名为test-web1的Deployment和一个名为web1-service的Service。

        ```
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: test-web1
          labels:
            app: test-web1
        spec:
          replicas: 1
          selector:
            matchLabels:
              app: test-web1
          template:
            metadata:
              labels:
                app: test-web1
            spec:
              containers:
              - name: test-web1
                imagePullPolicy: IfNotPresent
                image: registry.cn-hangzhou.aliyuncs.com/yilong/ingress-test:web1
                ports:
                - containerPort: 8080
        --- 
        apiVersion: v1
        kind: Service
        metadata:
          name: web1-service
        spec:
          type: ClusterIP
          selector:
            app: test-web1
          ports:
            - port: 8080
              targetPort: 8080
        ```

    2.  执行以下命令，创建Deployment和Service。

        ```
        kubectl apply -f test-deployment-service.yaml
        ```

2.  创建Ingress。

    1.  使用以下内容，创建test-ingress.yaml。

        ```
        apiVersion: networking.k8s.io/v1beta1
        kind: Ingress
        metadata:
          name: test-ingress
          namespace: default
        spec:
          rules:
          - host: test-ingress.com
            http:
              paths:
              - path: /foo
                backend:
                  serviceName: web1-service
                  servicePort: 8080
              - path: /bar
                backend:
                  serviceName: web1-service
                  servicePort: 8080
        ```

        -   `name`：Ingress的名称，本例为test-ingress。
        -   `host`：指定服务访问域名。
        -   `path`：指定访问的url路径。SLB将流量转发到`backend`之前，所有的入站请求都要先匹配`host`和`path`。
        -   `backend`：由服务名称和服务端口组成。
            -   服务名称：Ingress转发的`backend`服务名称。
            -   服务端口：服务暴露的端口。
    2.  执行以下命令，创建Ingress。

        ```
        kubectl apply -f test-ingress.yaml
        ```


**查看Ingress**

执行以下命令，查看Ingress。

```
kubectl get ingress
```

**更新Ingress**

执行以下命令，更新Ingress。

```
kubectl edit ingress <ingress名称>
```

**删除Ingress**

执行以下命令，删除Ingress。

```
kubectl delete ingress <ingress名称>
```

