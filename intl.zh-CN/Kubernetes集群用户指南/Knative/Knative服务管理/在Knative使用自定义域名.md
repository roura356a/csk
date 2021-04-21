---
keyword: [Knative, 自定义域名]
---

# 在Knative使用自定义域名

本文介绍了如何在Knative Serving中使用自定义域名。

-   您已经成功申请一个域名，参见[阿里云域名服务](/intl.zh-CN/产品简介/什么是阿里云域名服务.md)。
-   您已经成功部署Knative，参见[t423002.md\#](/intl.zh-CN/Kubernetes集群用户指南/Knative/Knative组件管理/一键部署Knative.md)。

在Knative Serving Route路由中使用`example.com`作为默认域名，Route完全定义的域名格式默认为：`{route}.{namespace}.{default-domain}`。

## 通过Kubectl修改域名

下面以在命名空间knative-serving中将域名修改为自定义域名为例进行介绍。

1.  [通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。

2.  执行如下命令，编辑域名config-map配置文件config-domain。

    ```
    kubectl edit cm config-domain --namespace knative-serving
    ```

3.  修改配置文件。

    将配置文件中的example.com改为自定义域名后，保存配置。本例中的自定义域名为：mydomain.com。

    ```
    apiVersion: v1
    data:
      mydomain.com: ""
    kind: ConfigMap
    [...]
    ```


## 部署应用

如果您已经部署了应用，Knative会根据域名配置configmap，自动更新所有的Service和Route。

1.  部署一个应用。以部署一个命名为helloworld-go的应用为例，请参见[快速部署Serverless应用](/intl.zh-CN/Kubernetes集群用户指南/Knative/Knative服务管理/快速部署Serverless应用.md)。

2.  执行以下操作，查看部署结果。

    -   在Knative 0.7版本下，执行以下命令，查看域名。

        ```
        kubectl get route ${SVC_NAME} --output jsonpath="{.status.url}"| awk -F/ '{print $3}'`
        ```

    -   在Knative 0.6版本下，执行以下命令，查看域名。

        ```
        kubectl get route helloworld-go --output jsonpath="{.status.domain}"
        ```

    当返回结果如下时，表示自定义域名已生效。

    ```
    helloworld-go.default.mydomain.com
    ```


## 发布域名

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**应用** \> **Knative**。

5.  单击服务管理页签，然后单击目标服务名称。

    您可以在Knative服务详情页的**基本信息**区域，查看访问网关和域名。

6.  将Knative网关IP设置到对应的域名解析。


执行以下命令，查看执行结果。

```
curl http://helloworld-go.default.mydomain.com
```

