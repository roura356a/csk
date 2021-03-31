---
keyword: [Workflow, 暴露UI访问端点]
---

# 开启Workflow UI

Workflow提供了一套UI来展示目前工作流的状态，方便查看每个步骤的容器日志，下面为您介绍如何使用Ingress暴露UI访问端点。

-   您已成功创建一个Kubernetes集群。具体操作，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   您已连接到Kubernetes集群的Master节点。具体操作，请参见[通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。

1.  执行htpasswd命令生成auth文件，用于存放用户名密码。

    执行命令：

    ```
    htpasswd -c auth workflow
    ```

    返回结果如下：

    ```
    New password: <workflow>
    New password:
    Re-type new password:
    Adding password for user workflow
    ```

2.  执行如下命令，创建Secret来在Kubernetes集群中存放此加密文件。

    ```
    kubectl create secret generic workflow-basic-auth --from-file=auth -n argo
    ```

3.  创建并复制内容到ingress yaml文件中，并执行`kubectl apply -f ingress.yaml`命令，创建workflow-ingress路由。

    ```
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

    ![开启workflow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6429449951/p47755.png)

    您可以根据需要查看工作流的状态。

    ![查看工作量状态](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7429449951/p47756.png)


