# 应用管理FAQ

本文主要为您介绍Kubernetes集群中应用的常见问题。

-   [容器服务ACK应用故障排查](~~211618~~)
-   [如何手动升级Helm的版本？](#section_42y_vhz_d9t)
-   [如何支持私有镜像？](#section_b2s_ldm_84z)
-   [如何手动安装alicloud-application-controller插件？](~~87156~~)
-   [Cloud Controller Manager（CCM）组件升级检查失败](~~164988~~)
-   [在Kubernetes集群中如何支持私有镜像编排容器](~~86562~~)
-   [容器镜像服务源码绑定失败问题排查](~~185631~~)
-   [容器镜像仓库构建服务失败问题排查](~~186529~~)

## 如何手动升级Helm的版本？

1.  登录到Kubernetes集群master节点。更多信息，请参见[t16645.md\#](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。

2.  执行以下命令。

    其中镜像地址可使用对应地域的VPC域名，例如，杭州地域的机器就可以替换为registry-vpc.cn-hangzhou.aliyuncs.com/acs/tiller:v2.11.0。

    ```
    helm init --tiller-image registry.cn-hangzhou.aliyuncs.com/acs/tiller:v2.11.0 --upgrade
    ```

3.  等待tiller健康检查通过，就可以通过执行`helm version`查看版本升级情况。

    **说明：**

    -   这里只会升级Helm服务端版本，客户端可以通过直接下载对应的Client Binary使用。
    -   请下载阿里云支持的最新客户端版本[Helm client 2.11.0](https://github.com/helm/helm/releases/tag/v2.11.0)。
4.  Helm客户端和服务端版本都升级完毕后，执行helm version命令，可看到以下信息。

    ```
    helm version
    ```

    ```
    Client: &version.Version{SemVer:"v2.11.0", GitCommit:"2e55dbe1fdb5fdb96b75ff144a339489417b146b", GitTreeState:"clean"}
    Server: &version.Version{SemVer:"v2.11.0", GitCommit:"2e55dbe1fdb5fdb96b75ff144a339489417b146b", GitTreeState:"clean"}
    ```


## 如何支持私有镜像？

1.  执行以下命令。

    ```
    kubectl create secret docker-registry regsecret --docker-server=registry-internal.cn-hangzhou.aliyuncs.com --docker-username=abc@aliyun.com --docker-password=xxxxxx --docker-email=abc@aliyun.com
    ```

    **说明：**

    -   `regsecret`：指定密钥的键名称，可自行定义。
    -   `—docker-server`：指定Docker仓库地址。
    -   `—docker-username`：指定Docker仓库用户名。
    -   `—docker-password`：指定Docker仓库登录密码。
    -   `—docker-email`：指定邮件地址（选填）。
    您可以通过以下两种方法进行操作。

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
        详情请参见官方文档[使用私有仓库](https://kubernetes.io/docs/concepts/containers/images/#using-a-private-registry)。

    -   自动配置私有镜像实现无密钥编排

        **说明：** 为了避免每次使用私有镜像部署时，都需要引用密钥，您可将Secret添加到Namespace的Default Service Account中，详情请参见[Add ImagePullSecrets to a service account](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account)。

        1.  执行以下命令，查看创建的拉取私有镜像的Secret。

            ```
            kubectl get secret regsecret
            ```

            ```
            NAME        TYPE                             DATA      AGE
            regsecret   kubernetes.io/dockerconfigjson   1         13m
            ```

            本例中采用手动配置的方式，修改命名空间的默认服务帐户Default，从而将此Secret作为imagePullSecret。

        2.  创建一个sa.yaml配置文件，将服务账号Default的配置导入到该文件中。

            ```
            kubectl get serviceaccounts default -o yaml > ./sa.yaml
            
            cat  sa.yaml
            
            apiVersion: v1
            kind: ServiceAccount
            metadata:
              creationTimestamp: 2015-08-07T22:02:39Z
              name: default
              namespace: default
              resourceVersion: "243024"             ##注意该项selfLink: /api/v1/namespaces/default/serviceaccounts/default
              uid: 052fb0f4-3d50-11e5-b066-42010af0d7b6
            secrets:
            - name: default-token-uudgeoken-uudge
            ```

        3.  执行`vim sa.yaml`命令，删除resourceVersion，并增加拉取镜像的密钥配置项imagePullSecrets。修改后的配置如下。

            ```
            apiVersion: v1
            kind: ServiceAccount
            metadata:
              creationTimestamp: 2015-08-07T22:02:39Z
              name: default
              namespace: default
              selfLink: /api/v1/namespaces/default/serviceaccounts/default
              uid: 052fb0f4-3d50-11e5-b066-42010af0d7b6
            secrets:
            - name: default-token-uudge
            imagePullSecrets:                 ##增加该项
            - name: regsecret                                    
            ```

        4.  用sa.yaml配置文件替换Default的服务账号配置。

            ```
            kubectl replace serviceaccount default -f ./sa.yaml
            serviceaccount "default" replaced
            ```

        5.  以一个Tomcat编排为例，执行kubectl create -f命令创建。

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
                    image: registry-internal.cn-hangzhou.aliyuncs.com/abc/test:1.0              #替换为您自己的私有镜像地址ports:
                    - containerPort: 8080
            ```

        6.  若配置正常，Pod会启动成功。然后执行kubectl get pod tomcat-xxx -o yaml命令，你可看到以下配置项。

            ```
            spec:
              imagePullSecrets:
            - nameregsecretey
            ```


