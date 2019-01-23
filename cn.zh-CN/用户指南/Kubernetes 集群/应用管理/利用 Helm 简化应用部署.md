# 利用 Helm 简化应用部署 {#concept_fvz_4vm_vdb .concept}

在 Kubernetes 中，应用管理是需求最多、挑战最大的领域。Helm 项目提供了一个统一软件打包方式，支持版本控制，可以大大简化 Kubernetes 应用分发与部署中的复杂性。

阿里云容器服务在应用目录管理功能中集成了 Helm 工具，并进行了功能扩展，支持官方 Repository，让您快速部署应用。您可以通过命令行或容器服务控制台界面两种方式进行部署。

本文档介绍 Helm 的基本概念和使用方式，演示在阿里云的 Kubernetes 集群上利用 Helm 来部署示例应用 WordPress 和 Spark。

## Helm 基本概念 {#section_sjv_cwm_vdb .section}

Helm 是由 Deis 发起的一个开源工具，有助于简化部署和管理 Kubernetes 应用。

Helm 可以理解为 Kubernetes 的包管理工具，可以方便地发现、共享和使用为 Kubernetes 构建的应用，它包含几个基本概念

-   **Chart**：一个 Helm 包，其中包含了运行一个应用所需要的镜像、依赖和资源定义等，还可能包含 Kubernetes 集群中的服务定义，类似 Homebrew 中的 formula、APT 的 dpkg 或者 Yum 的 rpm 文件。
-   **Release**：在 Kubernetes 集群上运行的 Chart 的一个实例。在同一个集群上，一个 Chart 可以安装很多次。每次安装都会创建一个新的 release。例如一个 MySQL Chart，如果想在服务器上运行两个数据库，就可以把这个 Chart 安装两次。每次安装都会生成自己的 Release，会有自己的 Release 名称。
-   **Repository**：用于发布和存储 Chart 的存储库。

## Helm 组件 {#section_ujv_cwm_vdb .section}

Helm 采用客户端/服务器架构，由如下组件组成：

-   Helm CLI 是 Helm 客户端，可以在 Kubernetes 集群的 master 节点或者本地执行。
-   Tiller 是服务器端组件，在 Kubernetes 集群上运行，并管理 Kubernetes 应用程序的生命周期。
-   Repository 是 Chart 存储库，Helm 客户端通过 HTTP 协议来访问存储库中 Chart 的索引文件和压缩包。

## 使用 Helm 部署应用 {#section_z1l_fwm_vdb .section}

**前提条件**

-   通过 Helm 部署应用之前，利用阿里云容器服务来创建 Kubernetes 集群。参见[创建Kubernetes集群](cn.zh-CN/用户指南/Kubernetes 集群/集群管理/创建Kubernetes集群.md#)。

    在 Kubernetes 集群创建的同时，Tiller 将会被自动部署到集群之中，并且在所有的 master 节点上自动安装 Helm CLI 以及配置指向阿里云的 Chart 存储库。

-   查看您集群中 Kubernetes 的版本。

    仅支持 Kubernetes 版本 1.8.4 及以上的集群。对于 1.8.1 版本的集群，您可以在集群列表中进行**集群升级**操作。


## 通过控制台界面部署应用 {#section_ozs_qxm_vdb .section}

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**市场** \> **应用目录**，进入应用目录列表页面。
3.  选择一个 chart（本示例选择 WordPress），单击该 chart，进入 chart 详情页面。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16661/154821683210396_zh-CN.png)

4.  在页面右侧，填写部署的基本信息。

    -   **集群**：应用要部署到的集群。
    -   **命名空间**：选择命名空间。默认为 default。
    -   **发布名称**：填写应用发布的名称。本例中为 test。
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16661/154821683310397_zh-CN.png)

5.  单击**参数**，对配置进行修改。

    本示例中使用云盘的动态数据卷绑定一个PVC，参见[使用阿里云云盘](cn.zh-CN/用户指南/Kubernetes 集群/存储管理/使用阿里云云盘.md#)。

    **说明：** 您需要预先创建一个云盘存储卷（PV），并且存储卷的容量不能小于PVC定义的数值。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16661/154821683310398_zh-CN.png)

6.  配置完成后，单击**创建**，部署成功后，默认进入该应用的发布页面。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16661/154821683310399_zh-CN.png)

7.  单击左侧导航栏中的**应用** \> **服务**，选择所需的集群和命名空间，找到对应的服务，您可获取 http/https 外部端点的地址。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16661/154821683310400_zh-CN.png)

8.  单击上面的访问地址，进入 WordPress 博客发布页面。

## 通过命令行部署应用 {#section_ekv_cwm_vdb .section}

通过命令行部署应用时，您可以 SSH 登录 Kubernetes 集群的 master 节点 （Helm CLI 已自动安装并已配置Repository）进行操作，参见[SSH访问Kubernetes集群](cn.zh-CN/用户指南/Kubernetes 集群/集群管理/SSH访问Kubernetes集群.md#) 。您也可以在本地安装配置 kubectl 和 Helm CLI。

本示例以在本地安装配置 kubectl 和 Helm CLI 并部署 WordPress 和 Spark 应用为例进行说明。

## 安装配置 kubectl 和 Helm CLI {#section_fkv_cwm_vdb .section}

1.  在本地计算机上安装和配置 kubectl。

    参见[通过 kubectl 连接 Kubernetes 集群](cn.zh-CN/用户指南/Kubernetes 集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)。

    若要查看 Kubernetes 目标集群的信息，键入命令 `kubectl cluster-info`。

2.  在本地计算机上安装 Helm。

    安装方法，参见 [Install Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md?spm=5176.100239.blogcont159601.27.k76Hec&file=install.md)。

3.  配置 Helm 的 Repository。这里我们使用了阿里云容器服务提供的 Charts 存储库。

    ```
    helm init --client-only --stable-repo-url https://aliacs-app-catalog.oss-cn-hangzhou.aliyuncs.com/charts/
    helm repo add incubator https://aliacs-app-catalog.oss-cn-hangzhou.aliyuncs.com/charts-incubator/
    helm repo update
    ```


**Helm 基础操作**

-   若要查看在集群上安装的 Charts 列表，请键入：

    `helm list`

    或者缩写

    `helm ls`

-   若要查看存储库配置，请键入：

    `helm repo list`

-   若要查看或搜索存储库中的 Helm charts，请键入以下任一命令：

    ```
    helm search 
    helm search 存储库名称 #如 stable 或 incubator
    helm search chart名称 #如 wordpress 或 spark
    ```

-   若要更新 charts 列表以获取最新版本，请键入：

    `helm repo update`


有关 Helm 使用的详细信息，请参阅 [Helm项目](https://github.com/kubernetes/helm)。

## 部署 WordPress {#section_kkv_cwm_vdb .section}

下面我们将利用 Helm，来部署一个 WordPress 博客网站。

输入如下命令。

```
helm install --name wordpress-test stable/wordpress
```

**说明：** 阿里云 Kubernetes 服务提供块存储（云盘）的动态存储卷支持，您需要预先创建一个云盘存储卷。

得到如下的结果。

```
NAME:   wordpress-test
LAST DEPLOYED: Mon Nov  20 19:01:55 2017
NAMESPACE: default
STATUS: DEPLOYED
...
```

利用如下命令查看 WordPress 的 release 和 service。

```
helm list
kubectl get svc
```

利用如下命令查看 WordPress 相关的 Pod，并等待其状态变为 Running。

```
kubectl get pod
```

利用如下命令获得 WordPress 的访问地址。

```
echo http://$(kubectl get svc wordpress-test-wordpress -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
```

通过上面的 URL，可以在浏览器上看到熟悉的 WordPress 站点。

也可以根据 Charts 的说明，利用如下命令获得 WordPress 站点的管理员用户和密码。

```
echo Username: user
echo Password: $(kubectl get secret --namespace default wordpress-test-wordpress -o jsonpath="{.data.wordpress-password}" | base64 --decode)
```

如需彻底删除 WordPress 应用，可输入如下命令。

```
helm delete --purge wordpress-test
```

## 通过 Helm 部署 Spark {#section_tkv_cwm_vdb .section}

下面我们将利用 Helm，来部署 Spark 以用于大数据处理。

输入如下命令。

```
helm install --name myspark stable/spark
```

得到如下的结果。

```
NAME:   myspark
LAST DEPLOYED: Mon Nov 20 19:24:22 2017
NAMESPACE: default
STATUS: DEPLOYED
...
```

利用如下命令查看 Spark 的 release 和 service。

```
helm list
kubectl get svc
```

利用如下命令查看 Spark 相关的 Pod，并等待其状态变为 Running。因为 Spark 的相关镜像体积较大，所以拉取镜像需要一定的时间。

```
kubectl get pod
```

利用如下命令获得 Spark Web UI 的访问地址。

```
echo http://$(kubectl get svc myspark-webui -o jsonpath='{.status.loadBalancer.ingress[0].ip}'):8080
```

通过上面的 URL，可以在浏览器上看到 Spark 的 Web UI，上面显示 worker 实例当前为 3 个。

接下来，我们将利用如下命令，使用 Helm 对 Spark 应用做升级，将 worker 实例数量从 3 个变更为 4 个。请注意参数名称是大小写敏感的。

```
helm upgrade myspark --set "Worker.Replicas=4" stable/spark
```

得到如下结果。

```
Release "myspark" has been upgraded. Happy Helming!
LAST DEPLOYED: Mon Nov 20 19:27:29 2017
NAMESPACE: default
STATUS: DEPLOYED
...
```

利用如下命令查看 Spark 新增的 Pod，并等待其状态变为 Running。

```
kubectl get pod
```

在浏览器上刷新 Spark 的 Web UI，可以看到此时 worker 数量已经变为 4 个。

如需彻底删除 Spark 应用，可输入如下命令。

```
helm delete --purge myspark
```

## 使用第三方的 Chart 存储库 {#section_elv_cwm_vdb .section}

您除了可以使用预置的阿里云的 Chart 存储库，也可以使用第三方的 Chart 存储库（前提是网络是可达的）。使用如下命令格式添加第三方 Chart 存储库。

```
helm repo add 存储库名 存储库URL
helm repo update
```

关于 Helm 相关命令的说明，您可以参阅 [Helm 文档](https://docs.helm.sh/helm/#helm-repo-add)

## 参考信息 {#section_glv_cwm_vdb .section}

Helm 催生了社区的发展壮大，越来越多的软件提供商，如 Bitnami 等公司，开始提供高质量的 Charts。您可以在 `https://kubeapps.com/` 中寻找和发现已有的 Charts。

