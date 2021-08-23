---
keyword: [应用管理, 常见问题, FAQ]
---

# 应用FAQ

本文主要为您介绍Kubernetes集群中应用的常见问题。

-   [容器服务ACK应用故障排查](~~211618~~)
-   [如何手动升级Helm的版本？](#section_42y_vhz_d9t)
-   [如何支持私有镜像？](#section_b2s_ldm_84z)
-   [Cloud Controller Manager（CCM）组件升级检查失败](~~164988~~)
-   [在Kubernetes集群中如何支持私有镜像编排容器](~~86562~~)
-   [容器镜像服务源码绑定失败问题排查](~~185631~~)
-   [容器镜像仓库构建服务失败问题排查](~~186529~~)
-   [如何在国外地域的ACK集群中使用国内地域的容器镜像服务企业版的镜像？](#section_b3a_kr8_y6p)

## 如何手动升级Helm的版本？

1.  登录到Kubernetes集群。更多信息，请参见[通过kubectl工具连接集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl工具连接集群.md)。

2.  执行以下命令安装Tiller。

    其中镜像地址可使用对应地域的VPC域名，例如，杭州地域的机器替换为`registry-vpc.cn-hangzhou.aliyuncs.com/acs/tiller:v2.11.0`。

    ```
    helm init --tiller-image registry.cn-hangzhou.aliyuncs.com/acs/tiller:v2.11.0 --upgrade
    ```

3.  等待tiller健康检查通过，执行`helm version`命令查看版本升级情况。

    **说明：**

    -   这里只会升级Helm服务端版本，客户端可以通过直接下载对应的Client Binary使用。
    -   请下载阿里云支持的最新客户端版本[Helm client 2.11.0](https://github.com/helm/helm/releases/tag/v2.11.0)。
4.  Helm客户端和服务端版本都升级完毕后，执行以下命令查看Helm版本。

    ```
    helm version
    ```

    预期输出：

    ```
    Client: &version.Version{SemVer:"v2.11.0", GitCommit:"2e55dbe1fdb5fdb96b75ff144a339489417b****", GitTreeState:"clean"}
    Server: &version.Version{SemVer:"v2.11.0", GitCommit:"2e55dbe1fdb5fdb96b75ff144a339489417b****", GitTreeState:"clean"}
    ```


## 如何支持私有镜像？

1.  执行以下命令创建Secret。

    ```
    kubectl create secret docker-registry regsecret --docker-server=registry-internal.cn-hangzhou.aliyuncs.com --docker-username=abc****@aliyun.com --docker-password=**** --docker-email=abc****@aliyun.com
    ```

    **说明：**

    -   `regsecret`：指密钥的键名称，可自定义。
    -   `—docker-server`：指Docker仓库地址。
    -   `—docker-username`：指Docker仓库用户名。
    -   `—docker-password`：指Docker仓库登录密码。
    -   `—docker-email`：指邮件地址。
    您可以通过以下两种方法操作：

    -   手动配置私有镜像

        YAML文件加入密钥参数。

        ```
        containers:
            - name: foo
              image: registry-internal.cn-hangzhou.aliyuncs.com/abc/test:1.0
        imagePullSecrets:
            - name: regsecret
        ```

        **说明：**

        -   `imagePullSecrets`是声明拉取镜像时需要指定密钥。
        -   `regsecret`必须和上面生成密钥的键名一致。
        -   `image`中的Docker仓库名称必须和`--docker-server`中的Docker仓库名一致。
        更多信息，请参见[使用私有仓库](https://kubernetes.io/docs/concepts/containers/images/#using-a-private-registry)。

    -   自动配置私有镜像实现无密钥编排

        **说明：** 为了避免每次使用私有镜像部署时，都需要引用密钥，您可将Secret添加到Namespace的Default Service Account中。更多信息，请参见[Add ImagePullSecrets to a service account](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account)。

        1.  执行以下命令，查看创建的拉取私有镜像的Secret。

            ```
            kubectl get secret regsecret
            ```

            预期输出：

            ```
            NAME        TYPE                             DATA      AGE
            regsecret   kubernetes.io/dockerconfigjson   1         13m
            ```

            本例中采用手动配置的方式，修改命名空间的默认服务账号**Default**，从而将此Secret作为imagePullSecret。

        2.  创建一个sa.yaml配置文件，将服务账号Default的配置导入到该文件中。

            ```
            kubectl get serviceaccounts default -o yaml > ./sa.yaml
            ```

        3.  执行以下命令查看sa.yaml文件详情。

            ```
            cat  sa.yaml
            ```

            预期输出：

            ```
            apiVersion: v1
            kind: ServiceAccount
            metadata:
              creationTimestamp: 2015-08-07T22:02:39Z
              name: default
              namespace: default
              resourceVersion: "243024"             #注意该项selfLink: /api/v1/namespaces/default/serviceaccounts/default。
              uid: 052fb0f4-3d50-11e5-b066-42010af0****
            secrets:
            - name: default-token-uudgeoken-uudge
            ```

        4.  执行`vim sa.yaml`命令，删除resourceVersion，并增加拉取镜像的密钥配置项imagePullSecrets。修改后的配置如下所示：

            ```
            apiVersion: v1
            kind: ServiceAccount
            metadata:
              creationTimestamp: 2015-08-07T22:02:39Z
              name: default
              namespace: default
              selfLink: /api/v1/namespaces/default/serviceaccounts/default
              uid: 052fb0f4-3d50-11e5-b066-42010af0****
            secrets:
            - name: default-token-uudge
            imagePullSecrets:                 #增加该项。
            - name: regsecret                                    
            ```

        5.  执行以下命令将sa.yaml配置文件替换Default的服务账号配置。

            ```
            kubectl replace serviceaccount default -f ./sa.yaml
            ```

            预期输出：

            ```
            serviceaccount "default" replaced
            ```

        6.  创建Tomcat示例应用。

            Tomcat编排示例tomcat.yaml文件如下所示：

            ```
            apiVersion: apps/v1 
            kind: Deployment
            metadata:
              name: tomcat-deployment
              labels:
                app: tomcat
            spec:
              replicas: 1
              selector:
                matchLabels:
                  app: tomcat
              template:
                metadata:
                  labels:
                    app: tomcat
                spec:
                  containers:
                  - name: tomcat
                    image: registry-internal.cn-hangzhou.aliyuncs.com/abc/test:1.0              #替换为您自己的私有镜像地址Ports。
                    - containerPort: 8080
            ```

            执行以下命令创建Tomcat应用。

            ```
            kubectl create -f tomcat.yaml
            ```

        7.  Pod启动成功后执行以下命令，可看到预期的配置。

            ```
            kubectl get pod tomcat-**** -o yaml
            ```

            预期输出：

            ```
            spec:
              imagePullSecrets:
            - nameregsecretey
            ```


## 如何在国外地域的ACK集群中使用国内地域的容器镜像服务企业版的镜像？

在此种场景下，您需要在国内地域购买标准版和高级版的容器镜像服务企业版，在国外地域购买基础版的容器镜像服务企业版。

完成购买后，您需要使用同步实例的方法将国内地域的镜像同步到国外地域，具体操作，请参见[同步实例]()。在国外地域的容器镜像服务企业版中获取镜像地址，然后在国外地域的ACK集群中使用镜像地址创建应用。

如果您使用容器镜像服务个人版，同步镜像的速度将非常慢。如果您使用的是自建仓库，您需要购买GA加速。

**说明：** 自建仓库和购买GA加速的成本比购买容器镜像服务企业版高，推荐您使用容器镜像服务企业版。

关于容器镜像服务企业版的计费方式，请参见[计费说明]()。

