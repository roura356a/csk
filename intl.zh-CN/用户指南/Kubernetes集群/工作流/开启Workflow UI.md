# 开启Workflow UI {#concept_270146 .concept}

Workflow 提供了一套 UI 来展示目前工作流的状态，方便查看每个步骤的容器日志，下面为您介绍如何使用 Ingress 暴露UI访问端点。

## 前提条件 {#section_z8h_j6q_zi8 .section}

-   您已成功创建一个 Kubernetes 集群，参见[创建Kubernetes集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/创建Kubernetes集群.md#)。
-   您已连接到Kubernetes集群的Master节点，参见[通过 kubectl 连接 Kubernetes 集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)。

## 操作步骤 {#section_9is_w2g_zmd .section}

1.  执行 htpasswd 命令生成auth文件，用于存放用户名密码。

    ``` {#codeblock_y3v_blp_hrs}
    $ htpasswd -c auth workflow
    New password: <workflow>
    New password:
    Re-type new password:
    Adding password for user workflow
    ```

2.  执行如下命令，创建secret 来在 Kubernetes 集群中存放此加密文件。

    ``` {#codeblock_rhs_n14_w8w}
    $ kubectl create secret generic workflow-basic-auth --from-file=auth -n argo
    ```

3.  创建并拷贝内容到ingress yaml文件中，并执行`kubectl apply -f ingress.yaml`命令，创建workflow-ingress路由。

    ``` {#codeblock_fky_sh8_x65}
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: workflow-ingress
      namespace: argo
      annotations:
        # type of authentication
        nginx.ingress.kubernetes.io/auth-type: basic
        # name of the secret that contains the user/password definitions
        nginx.ingress.kubernetes.io/auth-secret: workflow-basic-auth
        # message to display with an appropriate context why the authentication is required
        nginx.ingress.kubernetes.io/auth-realm: 'Authentication Required - workflow'
    spec:
      rules:
      - host: workflow.<yourTestHost>
        http:
          paths:
          - path: /
            backend:
              serviceName: argo-ui
              servicePort: 80
    ```

    **说明：** 此处的`host`需要替换成您对应的集群地址（即为**集群信息**中的测试域名的值，例如：workflow.cfb131.cn-zhangjiakou.alicontainer.com）。

4.  在浏览器输入workflow.<yourTestHost\>，按照提示输入密码就能看到如下界面。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/222974/155860782247755_zh-CN.png)

    你可以根据需要查看工作流的状态。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/222974/155860782247756_zh-CN.png)


