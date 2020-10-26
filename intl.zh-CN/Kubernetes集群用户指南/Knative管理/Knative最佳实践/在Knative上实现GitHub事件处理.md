# 在Knative上实现GitHub事件处理

本文介绍了如何在Knative上实现GitHub事件处理。

-   您已经成功部署Serving组件、Eventing组件及add-on组件，参见[部署组件](/intl.zh-CN/Kubernetes集群用户指南/Knative管理/Knative组件管理/部署组件.md)。
-   您已经在Knative上自定义域名并完成域名配置。参见[Knative自定义域名](/intl.zh-CN/Kubernetes集群用户指南/Knative管理/Knative服务管理/Knative自定义域名.md)。

## 创建服务

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

3.  在集群管理页左侧导航栏中，单击**Knative** \> **服务管理**。

4.  单击右上角的**创建服务**。

5.  设置集群名称、命名空间、服务名称，选择所要使用的镜像和镜像版本等配置信息。

    **说明：**

    本示例中，界面参数取值参考如下：

    -   **服务名称**：自定义该服务的名称。本例为github-event-display。
    -   **镜像名称**：您可以单击选择镜像，在弹出的对话框中选择所需的镜像并单击**确定**。您还可以填写私有registry。填写的格式为domainname/namespace/imagename:tag。本例中为registry.cn-shanghai.aliyuncs.com/knative-release/eventing-sources-event\_display。
    -   **镜像版本**：您可以单击选择镜像版本选择镜像的版本。若不指定，默认为latest。本例中为bf45b3eb1e7fc4cb63d6a5a6416cf696295484a7662e0cf9ccdf5c080542c21d。
    界面其他参数详细信息请参见[参数说明](/intl.zh-CN/Kubernetes集群用户指南/Knative管理/Knative服务管理/创建Knative服务.md)。

6.  单击**创建**。


## 创建GitHub Token

1.  创建Personal access token。

    Personal access token用于访问GitHub API。请参见[Personal access tokens](https://github.com/settings/tokens)。

    下面是设置一个GitHubSource Sample token的示例。

    ![创建github token](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/6995659951/p58037.png)

    **说明：** 需要开启`repo:public_repo`和`admin:repo_hook`，以便通过公共仓库触发Event事件，并为这些公共仓库创建webhooks。

    您可以自定义token的名称。

2.  [通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群管理/连接集群/通过kubectl连接Kubernetes集群.md)。

3.  secretToken内容可以通过下述方式生成随机字符串。

    ```
    head -c 8 /dev/urandom | base64
    ```

4.  更新githubsecret.yaml。

    如果生成的是personal\_access\_token\_value token，则需要设置secretToken。示例如下：

    ```
    apiVersion: v1
    kind: Secret
    metadata:
      name: githubsecret
    type: Opaque
    stringData:
      accessToken: personal_access_token_value
      secretToken: asdfasfdsaf
    ```

5.  执行如下命令，创建GitHub Token。

    ```
    kubectl --namespace default apply githubsecret.yaml
    ```


## 创建GitHub事件源

您可以创建GitHubSource用于接收GitHub产生的事件。

1.  [通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群管理/连接集群/通过kubectl连接Kubernetes集群.md)。

2.  创建并拷贝以下内容到github-source.yaml文件中。

    ```
    apiVersion: sources.eventing.knative.dev/v1alpha1
    kind: GitHubSource
    metadata:
      name: githubsourcesample
    spec:
      eventTypes:
        - pull_request
      ownerAndRepository: <YOUR USER>/<YOUR REPO>
      accessToken:
        secretKeyRef:
          name: githubsecret
          key: accessToken
      secretToken:
        secretKeyRef:
          name: githubsecret
          key: secretToken
      sink:
        apiVersion: serving.knative.dev/v1alpha1
        kind: Service
        name: github-event-display
    ```

3.  执行以下命令，在命名空间default中创建GitHubSource。

    ```
    kubectl --namespace default apply github-source.yaml
    ```


在GitHub repository中**Settings** \> **Webhooks**，查看会有一个验证成功的Hook URL。

**说明：** 域名需要备案，否则无法进行访问。

在GitHub repository中执行以下命令，创建一个`pull request`，此时会产生Event事件。

```
kubectl --namespace default get pods
kubectl --namespace default logs github-event-display-XXXX user-container
```

在Knative Eventing可以看到类似下面的事件结果。

```
2018/11/08 18:25:34 Message Dumper received a message: POST / HTTP/1.1
Host: github-event-display.knative-demo.svc.cluster.local
Accept-Encoding: gzip
Ce-Cloudeventsversion: 0.1
Ce-Eventid: a8d4cf20-e383-11e8-8069-46e3c8ad****
Ce-Eventtime: 2018-11-08T18:25:32.819548012Z
Ce-Eventtype: dev.knative.source.github.pull_request
Ce-Source: https://github.com/someuser/somerepo/pull/1
Content-Length: 21060
Content-Type: application/json
User-Agent: Go-http-client/1.1
X-B3-Parentspanid: b2e514c3dbe94c03
X-B3-Sampled: 1
X-B3-Spanid: c85e346d89c8be4e
X-B3-Traceid: abf6292d458fb8e7
X-Envoy-Expected-Rq-Timeout-Ms: 60000
X-Envoy-Internal: true
X-Forwarded-For: 12*.*.*.*, 12*.*.*.*
X-Forwarded-Proto: http
X-Request-Id: 8a2201af-5075-9447-b593-ec3a243a****

{"action":"opened","number":1,"pull_request": ...}
```

