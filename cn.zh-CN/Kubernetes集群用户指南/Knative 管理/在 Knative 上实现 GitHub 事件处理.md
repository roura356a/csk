# 在 Knative 上实现 GitHub 事件处理 {#task_1909303 .task}

本文介绍了如何在 Knative 上实现 GitHub 事件处理。

-   您已经成功部署 Serving 组件、Eventing组件及add-on组件，参见[部署组件](cn.zh-CN/Kubernetes集群用户指南/Knative 管理/部署组件.md#)。
-   您已经在Knative上自定义域名并完成域名配置。参见[Knative 自定义域名](cn.zh-CN/Kubernetes集群用户指南/Knative 管理/Knative 自定义域名.md#)。

## 创建服务 {#section_2r0_pam_t2w .section}

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，选择**Knative** \> **服务管理**，进入服务管理页面。
3.  单击右上角的**创建服务**。
4.  设置集群名称、命名空间、服务名称，选择所要使用的镜像和镜像版本等配置信息。 

    ![设置服务信息](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1513191/156689901357948_zh-CN.png)

    **说明：** 

    本示例中，界面参数取值参考如下：

    -   **服务名称**：自定义该服务的名称。本例为github-event-display。
    -   **镜像名称**：您可以单击选择镜像，在弹出的对话框中选择所需的镜像并单击**确定**。您还可以填写私有 registry。填写的格式为domainname/namespace/imagename:tag。本例中为registry.cn-shanghai.aliyuncs.com/knative-release/eventing-sources-event\_display 。
    -   **镜像版本**：您可以单击选择镜像版本选择镜像的版本。若不指定，默认为 latest。本例中为bf45b3eb1e7fc4cb63d6a5a6416cf696295484a7662e0cf9ccdf5c080542c21d。
    界面其他参数详细信息请参见[参数说明](cn.zh-CN/Kubernetes集群用户指南/Knative 管理/创建 Knative 服务.md#step_bw0_9pr_p44)。

5.  单击**创建**。

## 创建 GitHub Token {#section_kqt_599_32i .section}

1.  创建 Personal access token。 Personal access token用于访问 GitHub API。请参见[Personal access tokens](https://github.com/settings/tokens)。

    下面是设置一个 GitHubSource Sample token 的示例。

    ![创建github token](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1513191/156689901458037_zh-CN.png)

    **说明：** 需要开启 `repo:public_repo` 和 `admin:repo_hook`，以便通过公共仓库触发 Event 事件，并为这些公共仓库创建 webhooks 。

    您可以自定义token 的名称。

2.  [通过 kubectl 连接 Kubernetes 集群](cn.zh-CN/Kubernetes集群用户指南/集群管理/管理与访问集群/通过 kubectl 连接 Kubernetes 集群.md#)。
3.  更新githubsecret.yaml。 如果生成的是 personal\_access\_token\_value token，则需要设置 secretToken 。示例如下：

    ``` {#codeblock_8qa_hd2_vsc}
    apiVersion: v1
    kind: Secret
    metadata:
      name: githubsecret
    type: Opaque
    stringData:
      accessToken: personal_access_token_value
      secretToken: asdfasfdsaf
    ```

4.  执行如下命令，创建 GitHub Token。 

    ``` {#codeblock_wks_ufy_8s8}
    kubectl --namespace default apply githubsecret.yaml
    ```


## 创建 GitHub 事件源 {#section_q18_tl6_cx8 .section}

您可以创建 GitHubSource 用于接收 GitHub 产生的事件。

1.  [通过 kubectl 连接 Kubernetes 集群](cn.zh-CN/Kubernetes集群用户指南/集群管理/管理与访问集群/通过 kubectl 连接 Kubernetes 集群.md#)。
2.  创建并拷贝以下内容到github-source.yaml文件中。 

    ``` {#codeblock_q0i_whm_da8}
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

3.  执行以下命令，在命名空间default中创建 GitHubSource。 

    ``` {#codeblock_ax0_uck_wd1}
    kubectl --namespace default apply github-source.yaml
    ```


在 GitHub repository 中 **Settings** \> **Webhooks**，查看会有一个验证成功的 Hook URL。

**说明：** 域名需要备案，否则无法进行访问。

在 GitHub repository 中执行以下命令，创建一个 `pull request`，此时会产生Event事件。

``` {#codeblock_aw7_q08_y52}
kubectl --namespace default get pods
kubectl --namespace default logs github-event-display-XXXX user-container
```

在 Knative Eventing 可以看到类似下面的事件结果。

``` {#codeblock_adf_s1v_if8}
2018/11/08 18:25:34 Message Dumper received a message: POST / HTTP/1.1
Host: github-event-display.knative-demo.svc.cluster.local
Accept-Encoding: gzip
Ce-Cloudeventsversion: 0.1
Ce-Eventid: a8d4cf20-e383-11e8-8069-46e3c8ad2b4d
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
X-Forwarded-For: 127.0.0.1, 127.0.0.1
X-Forwarded-Proto: http
X-Request-Id: 8a2201af-5075-9447-b593-ec3a243aff52

{"action":"opened","number":1,"pull_request": ...}
```

