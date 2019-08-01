# Knative 自定义域名 {#task_1374930 .task}

本文介绍了如何在 Knative Serving 中使用自定义域名。

-   您已经成功申请一个域名，参见[阿里云域名服务](../../../../intl.zh-CN/产品简介/什么是阿里云域名服务.md#)。
-   您已经成功部署 Knative，参见 [部署 Knative](intl.zh-CN/Kubernetes集群用户指南/Knative 管理/部署 Knative.md#)。

在 Knative Serving route 路由中默认使用 `example.com` 作为默认域名，route 完全定义的域名格式默认为：`{route}.{namespace}.{default-domain}`在 Knative Serving route 路由中默认使用。

## 通过 Kubectl 修改域名 {#section_z3l_wqg_zkb .section}

下面以在命名空间knative-serving中将域名修改为自定义域名为例进行介绍。

1.  [通过 kubectl 连接 Kubernetes 集群](intl.zh-CN//通过 kubectl 连接 Kubernetes 集群.md#)
2.  执行如下命令，编辑域名 config-map 配置文件 config-domain。 

    ``` {#codeblock_5j9_td1_m6i}
    kubectl edit cm config-domain --namespace knative-serving
    ```

3.  修改配置文件。 使用自定义域名（如：mydomain.com），替换掉 example.com，保存配置。

    ``` {#codeblock_g7j_z2a_izu}
    apiVersion: v1
    data:
      mydomain.com: ""
    kind: ConfigMap
    [...]
    ```


## 部署应用 {#section_8wb_r2r_wse .section}

如果你已经部署了应用，Knative 会根据域名配置 configmap 的修改，自动更新所有的 Service 和 Route。

1.  部署一个应用。请参见[部署 Serving Hello World 应用](intl.zh-CN/Kubernetes集群用户指南/Knative 管理/部署 Serving Hello World 应用.md#)。 本例中部署一个命名为helloworld-go的应用。
2.  执行以下操作，查看部署结果。 

    -   在Knative 0.7 版本下，执行如下命令，查看域名。

        ``` {#codeblock_bda_s8a_xzz}
        kubectl get route ${SVC_NAME} --output jsonpath="{.status.url}"| awk -F/ '{print $3}'`
        ```

    -   在Knative 0.6 版本下，执行如下命令，查看域名。

        ``` {#codeblock_mjz_39r_csu}
        kubectl get route helloworld-go --output jsonpath="{.status.domain}"
        ```

    当返回结果如下时，表示自定义域名已生效。

    ``` {#codeblock_6a4_n78_nsk}
    helloworld-go.default.mydomain.com
    ```


## 域名发布 {#section_uzk_yod_ou3 .section}

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com/)。
2.  在 Kubernetes 菜单下，选择**Knative** \> **服务管理**，进入服务管理页面。
3.  选择目标集群及命名空间，可以看到目标应用对应的**访问网关**。 

    ![网关地址](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1095409/156464540853096_zh-CN.png)

4.  将Knative 网关 ip 设置到对应的域名解析。

执行如下命令，查看执行结果。

``` {#codeblock_1vw_syq_9he}
curl http://helloworld-go.default.mydomain.com
```

