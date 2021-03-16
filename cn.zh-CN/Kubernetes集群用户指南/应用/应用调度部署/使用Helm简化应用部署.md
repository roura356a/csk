# 使用Helm简化应用部署

本文档介绍Helm的基本概念和使用方式，演示在阿里云的Kubernetes集群上利用Helm来部署示例应用WordPress和Spark。

-   通过Helm部署应用之前，利用阿里云容器服务来创建Kubernetes集群。参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。

    在Kubernetes集群创建的同时，Tiller将会被自动部署到集群之中，并且在所有的master节点上自动安装Helm CLI以及配置指向阿里云的Chart存储库。

-   查看您集群中Kubernetes的版本。

    仅支持Kubernetes版本1.8.4及以上的集群。对于1.8.1版本的集群，您可以在集群列表中进行**集群升级**操作。


在Kubernetes中，应用管理是需求最多、挑战最大的领域。Helm项目提供了一个统一软件打包方式，支持版本控制，简化Kubernetes应用分发与部署中的复杂性。阿里云容器服务在应用目录管理功能中集成了Helm工具，并进行了功能扩展，支持官方Repository，让您快速部署应用。您可以通过命令行或容器服务控制台界面两种方式进行部署。

## Helm基本概念

Helm是由Deis发起的一个开源工具，有助于简化部署和管理Kubernetes应用。

Helm可以理解为Kubernetes的包管理工具，可以方便地发现、共享和使用Kubernetes构建的应用，它包含以下几个基本概念。

-   Chart：一个Helm包，其中包含了运行一个应用所需要的镜像、依赖和资源定义等，还可能包含Kubernetes集群中的服务定义，类似Homebrew中的formula、APT的dpkg或者Yum的rpm文件。
-   Release：在Kubernetes集群上运行的Chart的一个实例。在同一个集群上，一个Chart可以安装很多次。每次安装都会创建一个新的release。例如一个MySQL Chart，如果想在服务器上运行两个数据库，就可以把这个Chart安装两次。每次安装都会生成自己的Release，会有自己的Release名称。
-   Repository：用于发布和存储Chart的存储库。

## Helm组件

Helm采用客户端/服务器架构，由如下组件组成：

-   Helm CLI是Helm客户端，可以在Kubernetes集群的master节点或者本地执行。
-   Tiller是服务器端组件，在Kubernetes集群上运行，并管理Kubernetes应用程序的生命周期。
-   Repository是Chart存储库，Helm客户端通过HTTP协议来访问存储库中Chart的索引文件和压缩包。

## 通过控制台界面部署应用

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  登录[容器服务管理控制台](https://partners-intl.console.aliyun.com/#/cs)。

3.  单击左侧导航栏中的**市场** \> **应用目录**，进入应用目录列表页面。

4.  在**阿里云应用**页签选择一个chart（本示例选择WordPress），单击该chart，进入chart详情页面。

5.  在页面右侧，填写部署的基本信息。

    -   **集群**：应用要部署到的集群。
    -   **命名空间**：选择命名空间。默认为default。
    -   **发布名称**：填写应用发布的名称。
    ![部署基本信息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0085659951/p10397.png)

6.  单击**参数**，对配置进行修改。

    本示例中使用云盘的动态数据卷绑定一个PVC，参见[云盘存储卷使用说明](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/云盘存储卷/云盘存储卷使用说明.md)。

    **说明：** 您需要预先创建一个云盘存储卷（PV），并且存储卷的容量不能小于PVC定义的数值。

    ![修改参数配置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0085659951/p10398.png)

7.  配置完成后，单击**创建**，部署成功后，默认进入该应用的发布页面。

8.  在控制台左侧导航栏单击**集群**，单击目标集群**操作**列的**应用管理**，在集群管理页左侧导航栏中单击**服务**，找到对应的服务，您可获取http/https外部端点的地址。

9.  单击外部端点的访问地址，进入WordPress博客发布页面。


## 通过命令行部署应用

通过命令行部署应用时，您可以SSH登录Kubernetes集群（Helm CLI已自动安装并已配置Repository）进行操作，参见[t16642.md\#](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过SSH访问Kubernetes集群.md)。您也可以在本地安装配置kubectl和Helm CLI。

本示例以在本地安装配置kubectl和Helm CLI并部署WordPress和Spark应用为例进行说明。

1.  安装配置kubectl和Helm CLI。

    1.  在本地计算机上安装和配置kubectl。

        参见[通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。

        若要查看Kubernetes目标集群的信息，键入命令`kubectl cluster-info`。

    2.  在本地计算机上安装Helm。

        安装方法，参见[Install Helm](https://github.com/helm/helm)。

2.  部署WordPress。

    下面我们将利用Helm，来部署一个WordPress博客网站。

    1.  输入以下命令。

        ```
        helm install --name wordpress-test stable/wordpress
        ```

        **说明：** 阿里云Kubernetes服务提供块存储（云盘）的动态存储卷支持，您需要预先创建一个云盘存储卷。

        得到以下的结果。

        ```
        NAME:   wordpress-test
        LAST DEPLOYED: Mon Nov  20 19:01:55 2017
        NAMESPACE: default
        STATUS: DEPLOYED
        ...
        ```

    2.  执行以下命令查看WordPress的release和service。

        ```
        helm list
        kubectl get svc
        ```

    3.  执行以下命令查看WordPress相关的Pod，并等待其状态变为Running。

        ```
        kubectl get pod
        ```

    4.  执行以下命令获得WordPress的访问地址。

        ```
        echo http://$(kubectl get svc wordpress-test-wordpress -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
        ```

        通过上面的URL，可以在浏览器上看到熟悉的WordPress站点。

        也可以根据Charts的说明，利用如下命令获得WordPress站点的管理员用户和密码。

        ```
        echo Username: user
        echo Password: $(kubectl get secret --namespace default wordpress-test-wordpress -o jsonpath="{.data.wordpress-password}" | base64 --decode)
        ```

    5.  如需彻底删除WordPress应用，可输入如下命令。

        ```
        helm delete --purge wordpress-test
        ```


## 使用第三方的Chart存储库

您除了可以使用预置的阿里云的Chart存储库，也可以使用第三方的Chart存储库（前提是网络是可达的）。使用如下命令格式添加第三方Chart存储库。

```
helm repo add存储库名 存储库URL
helm repo update
```

关于Helm相关命令的说明，您可以参见[Helm文档](https://docs.helm.sh/helm/#helm-repo-add)。

## 参考信息

Helm催生了社区的发展壮大，越来越多的软件提供商，如Bitnami等公司，开始提供高质量的Charts。您可以在`https://kubeapps.com/`中寻找和发现已有的Charts。

