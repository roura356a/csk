---
keyword: [Ingress Controller, 灰度发布, 蓝绿发布, AB测试]
---

# 通过Ingress实现灰度发布和蓝绿发布

当对服务进行版本更新升级时，需要使用到滚动升级、分批暂停发布、蓝绿发布以及灰度发布等发布方式。本文将介绍在ACK集群中如何通过Ingress Controller来实现应用服务的灰度发布。

## 体验实验室

为了帮助您快速上手本教程，ACK为您提供了可以动手操作的交互式实验环境。如您需要体验，请访问[通过Ingress进行灰度发布](https://start.aliyun.com/handson/Tn0HcdCZ/grap_publish_by_ingress)。

## 前提条件

您已创建一个ACK集群。具体操作，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。

## 背景信息

灰度及蓝绿发布是为新版本创建一个与老版本完全一致的生产环境，在不影响老版本的前提下，按照一定的规则把部分流量切换到新版本，当新版本试运行一段时间没有问题后，将用户的全量流量从老版本迁移至新版本。

其中AB测试就是一种灰度发布方式，一部分用户继续使用老版本的服务，将一部分用户的流量切换到新版本，如果新版本运行稳定，则逐步将所有用户迁移到新版本。

## 应用场景

**基于客户端请求的流量切分场景**

假设当前线上环境，您已经有一套服务Service A对外提供7层服务，此时上线了一些新的特性，需要发布上线一个新的版本Service A‘。但又不想直接替换Service A服务，而是希望将请求头中包含`foo=bar`或者cookie中包含`foo=bar`的客户端请求转发到Service A‘服务中。待运行一段时间稳定后，可将所有的流量从Service A切换到Service A‘服务中，再平滑地将Service A服务下线。

![场景一](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/2391031161/p9950.png)

**基于服务权重的流量切分场景**

假设当前线上环境，您已经有一套服务Service B对外提供7层服务，此时修复了一些问题，需要发布上线一个新的版本Service B‘。但又不想将所有客户端流量切换到新版本Service B‘中，而是希望将20%的流量切换到新版本Service B‘中。待运行一段时间稳定后，再将所有的流量从Service B切换到Service B‘服务中，再平滑地将Service B服务下线。

![场景二](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/2391031161/p9951.png)

针对以上多种不同的应用发布需求，阿里云容器服务Ingress Controller支持了多种流量切分方式：

-   基于Request Header的流量切分，适用于灰度发布以及AB测试场景。
-   基于Cookie的流量切分，适用于灰度发布以及AB测试场景。
-   基于Query Param的流量切分，适用于灰度发布以及AB测试场景。
-   基于服务权重的流量切分，适用于蓝绿发布场景。

## 注解说明

Ingress Controller通过下列Annotation来支持应用服务的灰度发布机制。

-   nginx.ingress.kubernetes.io/service-match

    该注解用来配置新版本服务的路由匹配规则。

    ```
    nginx.ingress.kubernetes.io/service-match: | 
        <service-name>: <match-rule>
    # 参数说明：
    # service-name：服务名称，满足match-rule的请求会被路由到该服务中。
    # match-rule：路由匹配规则
    #
    # 路由匹配规则：
    # 1. 支持的匹配类型
    # - header：基于请求头，支持正则匹配和完整匹配。
    # - cookie：基于cookie，支持正则匹配和完整匹配。
    # - query：基于请求参数，支持正则匹配和完整匹配。
    #
    # 2. 匹配方式
    # - 正则匹配格式：/{regular expression}/，//表明采用正则方式匹配。
    # - 完整匹配格式："{exact expression}"，""表明采用完整方式匹配。
    ```

    路由匹配规则配置示例：

    ```
    # 请求头中满足foo正则匹配^bar$的请求被转发到新版本服务new-nginx中。
    new-nginx: header("foo", /^bar$/)
    # 请求头中满足foo完整匹配bar的请求被转发到新版本服务new-nginx中。
    new-nginx: header("foo", "bar")
    # cookie中满足foo正则匹配^sticky-.+$的请求被转发到新版本服务new-nginx中。
    new-nginx: cookie("foo", /^sticky-.+$/)
    # query param中满足foo完整匹配bar的请求被转发到新版本服务new-nginx中。
    new-nginx: query("foo", "bar")
    ```

-   nginx.ingress.kubernetes.io/service-weight

    该注解用来配置新旧版本服务的流量权重。

    ```
    nginx.ingress.kubernetes.io/service-weight: | 
        <new-svc-name>:<new-svc-weight>, <old-svc-name>:<old-svc-weight>
    参数说明：
    new-svc-name：新版本服务名称
    new-svc-weight：新版本服务权重
    old-svc-name：旧版本服务名称
    old-svc-weight：旧版本服务权重
    ```

    服务权重配置示例：

    ```
    nginx.ingress.kubernetes.io/service-weight: | 
        new-nginx: 20, old-nginx: 60
    ```


## 步骤一：部署服务

部署Nginx服务并通过Ingress Controller对外提供7层域名访问。

1.  创建Deployment和Service。

    1.  创建nginx.yaml。

        ```
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: old-nginx
        spec:
          replicas: 2
          selector:
            matchLabels:
              run: old-nginx
          template:
            metadata:
              labels:
                run: old-nginx
            spec:
              containers:
              - image: registry.cn-hangzhou.aliyuncs.com/acs-sample/old-nginx
                imagePullPolicy: Always
                name: old-nginx
                ports:
                - containerPort: 80
                  protocol: TCP
              restartPolicy: Always
        ---
        apiVersion: v1
        kind: Service
        metadata:
          name: old-nginx
        spec:
          ports:
          - port: 80
            protocol: TCP
            targetPort: 80
          selector:
            run: old-nginx
          sessionAffinity: None
          type: NodePort
        ```

    2.  执行以下命令，创建Deployment和Service。

        ```
        kubectl apply -f nginx.yaml
        ```

2.  部署Ingress。

    1.  创建ingress.yaml。

        ```
        apiVersion: networking.k8s.io/v1beta1
        kind: Ingress
        metadata:
          name: gray-release
        spec:
          rules:
          - host: www.example.com
            http:
              paths:
              # 老版本服务
              - path: /
                backend:
                  serviceName: old-nginx
                  servicePort: 80
        ```

    2.  执行以下命令，部署Ingress。

        ```
        kubectl apply -f ingress.yaml
        ```

3.  测试访问情况。

    1.  执行以下命令，获取外部IP。

        ```
        kubectl get ingress
        ```

    2.  执行以下命令，查看路由访问情况。

        ```
        curl -H "Host: www.example.com"  http://<EXTERNAL_IP>
        ```

        预期输出：

        ```
        old
        ```


## 步骤二：灰度发布新版本服务

发布一个新版本的Nginx服务并配置路由规则。

1.  部署新版本的Deployment和Service。

    1.  创建nginx1.yaml

        ```
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: new-nginx
        spec:
          replicas: 1
          selector:
            matchLabels:
              run: new-nginx
          template:
            metadata:
              labels:
                run: new-nginx
            spec:
              containers:
              - image: registry.cn-hangzhou.aliyuncs.com/acs-sample/new-nginx
                imagePullPolicy: Always
                name: new-nginx
                ports:
                - containerPort: 80
                  protocol: TCP
              restartPolicy: Always
        ---
        apiVersion: v1
        kind: Service
        metadata:
          name: new-nginx
        spec:
          ports:
          - port: 80
            protocol: TCP
            targetPort: 80
          selector:
            run: new-nginx
          sessionAffinity: None
          type: NodePort
        ```

    2.  部署新版本的Deployment和Service。

        ```
        kubectl apply -f nginx1.yaml
        ```

2.  设置访问新版本服务的路由规则。

    ACK支持设置以下三种路由规则，您可以根据实际情况选择路由规则。

    -   设置满足特定规则的客户端才能访问新版本服务。以下示例仅请求头中满足`foo=bar`的客户端请求才能路由到新版本服务。
        1.  按照以下内容，修改步骤[2](#step_ee9_ta0_d84)创建的Ingress。

            ```
            apiVersion: networking.k8s.io/v1beta1
            kind: Ingress
            metadata:
              name: gray-release
              annotations:
                # 请求头中满足正则匹配foo=bar的请求才会被路由到新版本服务new-nginx中
                nginx.ingress.kubernetes.io/service-match: | 
                  new-nginx: header("foo", /^bar$/)
            spec:
              rules:
              - host: www.example.com
                http:
                  paths:
                  # 老版本服务
                  - path: /
                    backend:
                      serviceName: old-nginx
                      servicePort: 80
                  # 新版本服务
                  - path: /
                    backend:
                      serviceName: new-nginx
                      servicePort: 80
            ```

        2.  查看路由访问情况。

            -   执行以下命令，访问服务。

                ```
                curl -H "Host: www.example.com"  http://<EXTERNAL_IP>
                ```

                预期输出：

                ```
                old
                ```

            -   执行以下命令，请求头中满足`foo=bar`的客户端请求访问服务。

                ```
                curl -H "Host: www.example.com" -H "foo: bar" http://<EXTERNAL_IP>
                ```

                预期输出：

                ```
                new
                ```

            重复执行以上命令，可以看到，仅请求头中满足`foo=bar`的客户端请求才能路由到新版本服务。

    -   在满足特定规则的基础上设置一定比例的请求被路由到新版本服务中。以下示例要求请求头中满足`foo=bar`的客户端请求，且仅允许其中50%的流量被路由到新版本服务中。
        1.  按照以下内容，修改步骤[2](#step_ee9_ta0_d84)创建的Ingress。

            ```
            apiVersion: networking.k8s.io/v1beta1
            kind: Ingress
            metadata:
              name: gray-release
              annotations:
                # 请求头中满足正则匹配foo=bar的请求才会被路由到新版本服务new-nginx中
                nginx.ingress.kubernetes.io/service-match: |
                    new-nginx: header("foo", /^bar$/)
                # 在满足上述匹配规则的基础上仅允许50%的流量会被路由到新版本服务new-nginx中
                nginx.ingress.kubernetes.io/service-weight: |
                    new-nginx: 50, old-nginx: 50
            spec:
              rules:
              - host: www.example.com
                http:
                  paths:
                  # 老版本服务
                  - path: /
                    backend:
                      serviceName: old-nginx
                      servicePort: 80
                  # 新版本服务
                  - path: /
                    backend:
                      serviceName: new-nginx
                      servicePort: 80
            ```

        2.  查看路由访问情况。

            -   执行以下命令，访问服务。

                ```
                curl -H "Host: www.example.com"  http://<EXTERNAL_IP>
                ```

                预期输出：

                ```
                old
                ```

            -   执行以下命令，请求头中满足`foo=bar`的客户端请求访问服务。

                ```
                curl -H "Host: www.example.com" -H "foo: bar" http://<EXTERNAL_IP>
                ```

                预期输出：

                ```
                new
                ```

            重复执行以上命令。可以看到，仅请求头中满足`foo=bar`的客户端请求，且只有50%的流量才能路由到新版本服务。

    -   设置一定比例的请求被路由到新版本服务中，以下示例中仅50%的流量被路由到新版本服务中。
        1.  按照以下内容，修改步骤[2](#step_ee9_ta0_d84)创建的Ingress。

            ```
            apiVersion: networking.k8s.io/v1beta1
            kind: Ingress
            metadata:
              name: gray-release
              annotations:
                  # 允许50%的流量被路由到新版本服务new-nginx中
                  nginx.ingress.kubernetes.io/service-weight: |
                      new-nginx: 50, old-nginx: 50
            spec:
              rules:
              - host: www.example.com
                http:
                  paths:
                  # 老版本服务
                  - path: /
                    backend:
                      serviceName: old-nginx
                      servicePort: 80
                  # 新版本服务
                  - path: /
                    backend:
                      serviceName: new-nginx
                      servicePort: 80
            ```

        2.  执行以下命令，查看路由访问情况。

            ```
            curl -H "Host: www.example.com" http://<EXTERNAL_IP>
            ```

            重复执行以上命令，可以看到仅50%的流量路由到新版本服务。


## 步骤三：删除老版本服务

系统运行一段时间后，当新版本服务已经稳定并且符合预期后，需要下线老版本的服务 ，仅保留新版本服务在线上运行。

1.  按照以下内容，修改步骤[2](#step_ee9_ta0_d84)创建的Ingress。

    ```
    apiVersion: networking.k8s.io/v1beta1
    kind: Ingress
    metadata:
      name: gray-release
    spec:
      rules:
      - host: www.example.com
        http:
          paths:
          # 新版本服务
          - path: /
            backend:
              serviceName: new-nginx
              servicePort: 80
    ```

2.  执行以下命令，查看路由访问情况。

    ```
    curl -H "Host: www.example.com" http://<EXTERNAL_IP>
    ```

    预期输出：

    ```
    new
    ```

    重复执行以上命令，可以看到请求全部被路由到了新版本的服务。

3.  删除旧版本的Deployment和Service。

    1.  执行以下命令，删除旧版本的Deployment。

        ```
        kubectl delete deploy <Deployment名称>
        ```

    2.  执行以下命令，删除旧版本的Service。

        ```
        kubectl delete svc <Service名称>
        ```


