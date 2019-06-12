# 部署 Serving Hello World 应用示例 {#concept_525886 .concept}

本文以 Hello World 示例为您介绍 Knative 。

## 前提条件 {#section_klu_a1g_mjo .section}

-   您已经成功创建一个 Kubernetes 集群，参见[创建Kubernetes集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/创建Kubernetes集群.md#)。
-   您已经成功部署 Knative，参见[部署 Knative](intl.zh-CN/用户指南/Kubernetes集群/Knative 管理/部署 Knative.md#)。
-   您已经成功部署 Serving 组件，参见[部署组件](intl.zh-CN/用户指南/Kubernetes集群/Knative 管理/部署组件.md#)。

## 操作步骤 {#section_zag_dev_oq9 .section}

1.  [通过 kubectl 连接 Kubernetes 集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)。
2.  创建并拷贝内容到helloworld-go.yaml，并执行`kubectl apply -f helloworld-go.yaml`命令，部署一个应用。

    ``` {#codeblock_tez_w80_ehb}
    apiVersion: serving.knative.dev/v1alpha1
    kind: Service
    metadata:
      name: helloworld-go
      namespace: default
    spec:
      template:
        spec:
          containers:
            - image: registry.cn-hangzhou.aliyuncs.com/knative-sample/helloworld-go:0529
              env:
                - name: TARGET
                  value: "Go Sample v1"
    ```

3.  登录[容器服务管理控制台](https://cs.console.aliyun.com/)。
4.  在 Kubernetes 菜单下，选择**路由与负载均衡** \> **服务**，进入服务列表页面。
5.  选择目标集群和命名空间istio-system，可以看到在 istio-ingressgateway 服务的外部端点。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/474495/156030941948926_zh-CN.png)

6.  执行如下命令，获取Domain信息。

    ``` {#codeblock_x5b_xal_t36}
    $ kubectl get ksvc helloworld-go  --output=custom-columns=NAME:.metadata.name,DOMAIN:.status.domain
    NAME            DOMAIN
    helloworld-go   helloworld-go.default.example.com
    ```

7.  执行如下命令，可以看到访问结果。

    此处的112.124.XX.XX为步骤5获取的istio-ingressgateway 服务的外部端点的IP地址。

    ``` {#codeblock_lt9_o8j_7e5}
    $ curl -H "Host: helloworld-go.default.example.com" http://112.124.XX.XX
    Hello Go Sample v1!
    ```


