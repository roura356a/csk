---
keyword: [编排模板, 镜像密钥, 命令, 创建无状态应用]
---

# 创建无状态工作负载Deployment

您可以通过镜像、编排模板及命令创建一个无状态应用。容器服务Kubernetes集群支持通过Web界面使用镜像密钥。本文以Nginx为例说明如何通过镜像、编排模板及命令来创建无状态应用。

-   已创建Kubernetes集群。具体操作，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   已通过kubectl连接kubernetes集群。具体操作，请参见[通过Kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl工具连接集群.md)。
-   已构建私有镜像仓库，并将镜像上传到该仓库中。本例中以阿里云容器镜像服务为示例。具体操作，请参见[管理镜像](/intl.zh-CN/Kubernetes集群用户指南/应用/镜像/管理镜像.md)。

## 使用镜像创建无状态Deployment应用

**步骤一：配置应用基本信息**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

5.  在**无状态**页面中，单击**使用镜像创建**。

6.  在**应用基本信息**配置向导页面中，设置应用的基本信息。

    在配置Deployment负载前，您需在页面上方设置应用所在的命名空间。本例设置命名空间为**default**。

    |参数|描述|
    |--|--|
    |应用名称

|设置应用的名称。 |
    |副本数量

|即应用包含的Pod数量，默认数量为2。 |
    |类型

|定义资源对象的类型，可选择**无状态**、**有状态**、**任务**、**定时**、**定时任务**、**守护进程集**。 |
    |标签

|为该应用添加一个标签，标识该应用。 |
    |注解

|为该应用添加一个注解（Annotation）。 |
    |时区同步

|容器与节点是否使用相同的时区。 |

    **说明：** 本例中选择**无状态（Deployment）**类型。

7.  单击**下一步**。

    进入**容器配置**配置向导页面。


**步骤二：配置容器**

在**容器配置**配置向导页面中，配置容器的镜像与资源、端口、环境变量、健康检查、生命周期、数据卷和日志。

**说明：** 在**容器1**页签右侧，单击**添加容器**为应用的Pod设置多个容器。

1.  在**基本配置**区域，完成容器的基本配置。

    |参数|描述|
    |--|--|
    |镜像名称

|    -   您可以单击**选择镜像**，选择所需的镜像并单击**确定**。本例中为官方Nginx镜像，可以在**搜索**页签中，选择**Docker镜像**，然后在文本框中输入nginx进行搜索。
        -   容器镜像服务：在**容器镜像服务**页签中，可以选择托管在容器镜像服务ACR中的镜像。需要选择镜像所属地域，以及镜像服务实例。关于ACR的更多信息，请参见[什么是容器镜像服务ACR]()。

**说明：** 您可以在**容器镜像服务**页签，根据名称搜索托管在容器镜像服务ACR中的镜像。

        -   Docker官方镜像：在**Docker官方镜像**页签中，可以选择Docker官方提供的容器镜像。
        -   用户收藏：在**用户收藏**页签中，可以选择您收藏的Docker官方镜像。
        -   搜索：在**搜索**页签中，可以分别在ACR的特定地域和Docker官方镜像中搜索容器镜像。
    -   您还可以填写私有镜像。填写的格式为`domainname/namespace/imagename:tag`。 |
    |镜像Tag

|    -   您可以单击**选择镜像Tag**选择镜像的版本。若不指定，默认为最新版。
    -   ACK支持以下三种镜像拉取策略（imagePullPolicy）：

        -   **优先使用本地镜像（IfNotPresent）**：如果本地有该镜像（之前拉取过该镜像至宿主机中），则使用本地镜像，本地不存在时拉取镜像。
        -   **总是拉取镜像（Always）**：表示每次部署或扩容都会从容器镜像服务重新拉取镜像，而不会从本地拉取镜像。
        -   **仅使用本地镜像（Never）**：仅使用本地镜像。
**说明：** 当您选中**镜像拉取策略**时，默认不设置镜像拉取策略。

    -   单击**设置镜像密钥**，您可以实现免密拉取镜像。具体操作，请参见[使用免密组件拉取容器镜像](/intl.zh-CN/Kubernetes集群用户指南/应用/镜像/使用免密组件拉取容器镜像.md)。 |
    |资源限制

|可指定该应用所能使用的资源上限，包括CPU、内存和ephemeral-storage三种资源，防止占用过多资源。 |
    |所需资源

|即为该应用预留资源额度，包括CPU和内存两种资源，即容器独占该资源，防止因资源不足而被其他服务或进程争夺资源，导致应用不可用。 |
    |容器启动项

|    -   stdin：表示为该容器开启标准输入。
    -   tty：表示为该容器分配一个虚拟终端，以便于向容器发送信号。
通常这两个选项是一起使用的，表示将终端（tty）绑定到容器的标准输入（stdin）上。例如，一个交互式的程序从用户获取标准输入，并显示到终端中。 |
    |特权容器

|    -   选择特权容器，则privileged=true，开启特权模式。
    -   不选择特权容器，则privileged=false，关闭特权模式。 |
    |Init Container

|勾选该项，表示创建一个Init Container。Init Container包含一些实用的工具。具体操作，请参见[Init Containers](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/?spm=a2c4g.11186623.2.13.3fdd30dfnyevPx)。 |

2.  在**端口设置**区域，单击**新增**设置容器的端口。

    |参数|描述|
    |--|--|
    |名称

|设置容器端口名称。 |
    |容器端口

|设置暴露的容器访问端口或端口名，端口号必须介于1~65535。 |
    |协议

|支持TCP和UDP。 |

3.  在**环境变量**区域，单击**新增**设置环境变量。

    支持通过键值对的形式为Pod配置环境变量。用于给Pod添加环境标志或传递配置等，具体操作，请参见[Pod variable](https://kubernetes.io/docs/tasks/inject-data-application/environment-variable-expose-pod-information/?spm=0.0.0.0.8VJbrE)。

    |参数|描述|
    |--|--|
    |类型

|设置环境变量的类型，支持：

    -   **自定义**
    -   **配置项**
    -   **保密字典**
    -   **变量/变量引用**
    -   **资源引用**
配置项、保密字典支持全部文件的引用，以保密字典为例。选择**保密字典**类型，只选择目标保密字典，则默认引用全部文件。

![环境变量](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/9173530261/p130410.png)

对应的YAML，也引用了整个密钥（Secret）。

![yaml](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/3506659951/p130413.jpg) |
    |变量名称

|设置环境变量名称。 |
    |变量/变量引用

|设置变量引用的值。 |

4.  在**健康检查**区域，根据需要开启**存活检查**、**就绪检查**及**启动探测**。

    更多信息，请参见[配置存活、就绪和启动探测器](https://kubernetes.io/zh/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)。

    |参数|请求类型|配置说明|
    |--|----|----|
    |    -   存活检查（Liveness）：用于检测何时重启容器。
    -   就绪检查（Readiness）：确定容器是否已经就绪，且可以接受流量。
    -   启动探测（Startup Probes）：用于检测何时启动容器。

**说明：** 仅Kubernetes集群1.18及之后版本支持启动探测。

|HTTP请求

|即向容器发送一个HTTP Get请求，支持的参数包括：

    -   协议：HTTP/HTTPS。
    -   路径：访问HTTP Server的路径。
    -   端口：容器暴露的访问端口或端口名，端口号必须介于1~65535。
    -   HTTP头：即HTTP Headers，HTTP请求中自定义的请求头，HTTP允许重复的Header。支持键值对的配置方式。
    -   延迟探测时间（秒）：即initialDelaySeconds，容器启动后第一次执行探测时需要等待多少秒，默认为3秒。
    -   执行探测频率（秒）：即periodSeconds，指执行探测的时间间隔，默认为10秒，最小为1秒。
    -   超时时间（秒）：即timeoutSeconds，探测超时时间。默认1秒，最小1秒。
    -   健康阈值：探测失败后，最少连续探测成功多少次才被认定为成功。默认是1，最小值是1。对于存活检查（liveness）必须是1。
    -   不健康阈值：探测成功后，最少连续探测失败多少次才被认定为失败。默认是3，最小值是1。 |
    |TCP连接

|即向容器发送一个TCP Socket，Kubelet将尝试在指定端口上打开容器的套接字。 如果可以建立连接，容器被认为是健康的，如果不能就认为是失败的。支持的参数包括：

    -   端口：容器暴露的访问端口或端口名，端口号必须介于1~65535。
    -   延迟探测时间（秒）：即initialDelaySeconds，容器启动后第一次执行探测时需要等待多少秒，默认为15秒。
    -   执行探测频率（秒）：即periodSeconds，指执行探测的时间间隔，默认为10秒，最小为1秒。
    -   超时时间（秒）：即timeoutSeconds，探测超时时间。默认1秒，最小1秒。
    -   健康阈值：探测失败后，最少连续探测成功多少次才被认定为成功。默认是1，最小值是1。对于存活检查（liveness）必须是1。
    -   不健康阈值：探测成功后，最少连续探测失败多少次才被认定为失败。默认是3，最小值是1。 |
    |命令行

|通过在容器中执行探针检测命令，来检测容器的健康情况。支持的参数包括：

    -   命令行：用于检测容器健康情况的探测命令。
    -   延迟探测时间（秒）：即initialDelaySeconds，容器启动后第一次执行探测时需要等待多少秒，默认为5秒。
    -   执行探测频率（秒）：即periodSeconds，指执行探测的时间间隔，默认为10秒，最小为1秒。
    -   超时时间（秒）：即timeoutSeconds，探测超时时间。默认1秒，最小1秒。
    -   健康阈值：探测失败后，最少连续探测成功多少次才被认定为成功。默认是1，最小值是1。对于存活检查（liveness）必须是1。
    -   不健康阈值：探测成功后，最少连续探测失败多少次才被认定为失败。默认是3，最小值是1。 |

5.  在**生命周期**区域，设置容器的生命周期。

    您可以为容器的生命周期配置启动执行、启动后处理和停止前处理。具体操作，请参见[配置生命周期](https://kubernetes.io/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/)。

    ![生命周期](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/7975659951/p134220.png)

    |参数|描述|
    |--|--|
    |启动执行

|为容器设置预启动命令和参数。 |
    |启动后处理

|为容器设置启动后的命令。 |
    |停止前处理

|为容器设置预结束命令。 |

6.  在**数据卷**区域，增加本地存储或云存储声明PVC（Persistent Volume Claim）。

    |参数|描述|
    |--|--|
    |增加本地存储

|本地存储：支持主机目录（HostPath）、配置项（ConfigMap）、保密字典（Secret）和临时目录，将对应的挂载源挂载到容器路径中。更多信息，请参见[volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE)。 |
    |增加云存储声明（PersistentVolumeClaim）

|支持通过PVC挂载云存储卷。在选择目标挂载源前，您需要创建云存储声明。具体操作，请参见[创建持久化存储卷声明](/intl.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/创建持久化存储卷声明.md)。|

    本例中配置了一个云存储类型的数据卷声明disk-ssd，将其挂载到容器的/tmp路径下。

    ![配置数据卷](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/4331557261/p12307.png)

7.  在**日志配置**区域，进行采集配置和自定义Tag设置。

    **说明：** 请确保已部署Kubernetes集群，并且在此集群上已安装日志插件。

    |配置项|描述|
    |---|--|
    |采集配置

|日志库：即在日志服务中生成一个对应的Logstore，用于存储采集到的日志。 |
    |容器内日志路径：支持Stdout和文本日志。

    -   Stdout：表示采集容器的标准输出日志。
    -   文本日志：表示收集容器内指定路径的日志，本例中表示收集/var/log/nginx下所有的文本日志，也支持通配符的方式。 |
    |自定义Tag

|您还可以设置自定义Tag，设置Tag后，会将该Tag一起采集到容器的日志输出中。自定义Tag可帮助您给容器日志打上Tag，方便进行日志统计和过滤等分析操作。 |

8.  单击**下一步**。

    进入**高级配置**配置向导页面。


**步骤三：完成高级配置**

在**高级配置**配置向导页面中设置访问、伸缩、调度和标签注解。

1.  在**访问设置**区域，设置暴露后端Pod的方式。

    **说明：**

    针对应用的通信需求，您可灵活进行访问设置：

    -   内部应用：对于只在集群内部工作的应用，您可以在创建服务时，根据需要选择**虚拟集群IP**或**节点端口**类型的服务，来进行内部通信。
    -   外部应用：对于需要暴露到公网的应用，您可以采用两种方式进行访问设置。
        -   创建负载均衡类型的服务：您可以在创建服务时，选择**负载均衡**类型的服务。通过阿里云提供的负载均衡服务SLB（Server Load Balancer），使得该服务提供公网访问能力。
        -   创建路由（Ingress）：通过创建路由（Ingress）提供公网访问能力。更多信息，请参见[Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/?spm=a2c4g.11186623.2.23.3fdd30dfnyevPx)。
    您可以设置暴露后端Pod的方式。本例中选择虚拟集群IP和路由（Ingress），构建一个公网可访问的Nginx应用。

    -   配置服务（Service）：在**服务（Service）**右侧，单击**创建**设置创建服务配置项。

        |配置项|描述|
        |---|--|
        |名称

|输入服务的名称。

本例为nginx-svc。|
        |类型

|选择服务类型，即服务访问的方式。

        -   **虚拟集群IP**：即ClusterIP，指通过集群的内部IP暴露服务，选择该值，服务只能够在集群内部可以访问，这也是默认的ServiceType。

**说明：** 您的服务类型为**虚拟集群IP**时，才能设置**实例间发现服务（Headless Service）**。

        -   **节点端口**：即NodePort，通过每个Node上的IP和静态端口（NodePort）暴露服务。NodePort服务会路由到ClusterIP服务，该ClusterIP服务会自动创建。通过请求`<NodeIP>:<NodePort>`，可以从集群的外部访问一个NodePort服务。
        -   **负载均衡**：即LoadBalancer，指阿里云提供的负载均衡服务（SLB），可选择公网访问或私网访问。阿里云负载均衡服务可以路由到NodePort服务和ClusterIP服务。

            -   新建SLB：您可以通过单击**修改**，修改SLB规格。
            -   使用已有SLB：您可以在已有的列表中选择SLB规格。
**说明：** 负载均衡类型支持新建SLB和使用已有SLB，且多个Kubernetes Service可以复用同一个SLB，但是存在以下限制：

            -   使用已有的负载均衡实例会强制覆盖已有监听。
            -   Kubernetes通过Service创建的SLB不能复用（会导致SLB被意外删除）。只能复用您手动在控制台（或调用OpenAPI）创建的SLB。
            -   复用同一个SLB的多个Service不能有相同的前端监听端口，否则会造成端口冲突。
            -   复用SLB时，监听的名字以及虚拟服务器组的名字被Kubernetes作为唯一标识符。请勿修改监听和虚拟服务器组的名字。
            -   不支持跨集群复用SLB。
本例中选择**虚拟集群IP**。|
        |端口映射

|添加服务端口和容器端口。容器端口需要与后端的Pod中暴露的容器端口一致。 |
        |外部流量策略

|        -   Local：流量只发给本机的Pod。
        -   Cluster：流量可以转发到其他节点上的Pod。
**说明：** 您的服务类型为**节点端口**或**负载均衡**时，才能设置**外部流量策略**。 |
        |注解

|为该服务添加一个注解（Annotation），配置负载均衡的参数。例如设置`service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth:20`表示将该服务的带宽峰值设置为20Mbit/s，从而控制服务的流量。更多参数，请参见[通过Annotation配置负载均衡](/intl.zh-CN/Kubernetes集群用户指南/网络/Service管理/通过Annotation配置负载均衡.md)。 |
        |标签

|为该服务添加一个标签，标识该服务。 |

    -   配置路由（Ingress）：在**路由（Ingress）**右侧，单击**创建**设置后端Pod的路由规则。

        关于路由配置信息，请参见[配置路由信息](/intl.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/创建Ingress路由.md)。

        **说明：** 通过镜像创建应用时，您仅能为一个服务创建路由（Ingress）。本例中使用一个虚拟主机名称作为测试域名，您需要在hosts文件中添加一条域名映射（Ingress外部端点 + Ingress域名）。在实际工作场景中，请使用备案域名。

        ```
        101.37.XX.XX   foo.bar.com    #即Ingress的IP。
        ```

        |配置项|描述|
        |---|--|
        |名称

|输入路由的名称。

本例为nginx-ingress。|
        |规则

|路由规则是指授权入站到达集群服务的规则。更多信息，请参见[配置路由信息](/intl.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/创建Ingress路由.md)。

        -   **域名**：输入Ingress域名。
        -   **路径**：指定服务访问的URL路径，默认为根路径/，本例中不做配置。每个路径 （path）都关联一个backend（服务），在阿里云SLB将流量转发到backend之前，所有的入站请求都要先匹配域名和路径。
        -   **服务**：选择服务的名称和对应端口。
        -   **开启TLS**：配置安全的路由服务。具体操作，请参见[Ingress高级用法](/intl.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/Ingress高级用法.md)。
本例中使用测试域名`foo.bar.com`；设置服务为nginx-svc。 |
        |服务权重

|设置该路径下多个服务的权重。服务权重采用相对值计算方式，默认值为100。 |
        |灰度发布策略

|设置灰度规则后，请求头中满足灰度发布匹配规则的请求才能被路由到新版本服务中。如果该服务设置了100%以下的权重比例，满足灰度规则的请求会继续依据权重比例路由到对应服务。容器服务支持多种流量切分方式，适用于灰度发布以及AB测试场景。

        -   基于Request Header的流量切分
        -   基于Cookie的流量切分
        -   基于Query Param的流量切分
**说明：** 目前阿里云容器服务Kubernetes Ingress Controller需要0.12.0-5及其以上版本才支持流量切分特性。

配置参数如下所示：

        -   **服务**：路由规则配置的服务。
        -   **类型**：支持Header（请求头）、Cookie和Query（请求参数）的匹配规则。
        -   **名称**和**匹配值**：您自定义的请求字段，名称和匹配值为键值对。
        -   **匹配规则**：支持正则匹配和完全匹配。 |
        |注解

|        -   单击**重定向注解**，可为路由添加一条典型的重定向注解。即`nginx.ingress.kubernetes.io/rewrite-target：/`，表示将/path路径重定向到后端服务能够识别的根路径/上面。
        -   您也可以单击**添加**按钮，输入注解名称和值，即Ingress的Annotation键值对，关于Ingress的注解，请参见[Annotations](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/)。 |
        |标签

|为Ingress添加对应的标签，标示该Ingress的特点。 |

    在**访问设置**区域，您可以看到创建完毕的服务和路由，您可单击**变更**和**删除**进行二次配置。

2.  在**伸缩配置**区域，配置是否开启**指标伸缩**和**定时伸缩**，从而满足应用在不同负载下的需求。

    -   容器服务支持容器组的指标伸缩，即根据容器CPU和内存资源占用情况自动调整容器组数量。

        **说明：** 若要启用自动伸缩，您必须为容器设置所需资源，否则容器自动伸缩无法生效。

        |参数|描述|
        |--|--|
        |指标

|支持CPU和内存，需要和设置的所需资源类型相同。 |
        |触发条件

|资源使用率的百分比，超过该使用量，容器开始扩容。 |
        |最大副本数量

|该负载类型可扩容的容器数量上限。 |
        |最小副本数量

|该负载类型可缩容的容器数量下限。 |

    -   容器服务支持容器组的定时伸缩，即定时地对容器服务Kubernetes集群进行扩缩容。开启定时伸缩前，需先安装ack-kubernetes-cronhpa-controller组件。关于定时伸缩的详细说明，请参见[创建定时伸缩CronHPA任务](/intl.zh-CN/Kubernetes集群用户指南/弹性伸缩/容器定时伸缩（CronHPA）.md)。
3.  在**调度设置**区域，设置**升级方式**、**节点亲和性**、**应用亲和性**和**应用非亲和性**，更多信息，请参见[Affinity and anti-affinity](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/?spm=a2c4g.11186623.2.31.3fdd30dfnyevPx#affinity-and-anti-affinity)。

    **说明：** 亲和性调度依赖节点标签和Pod标签。您可使用内置的标签进行调度；也可预先为节点、Pod配置相关的标签。

    |配置项|描述|
    |---|--|
    |升级方式

|升级方式包括滚动升级（rollingupdate）和替换升级（recreate），详细请参见详情请参见[Deployments](https://kubernetes.io/zh/docs/concepts/workloads/controllers/deployment/?spm=a2c4g.11186623.2.32.3fdd30dfnyevPx)。 |
    |节点亲和性

|设置**节点亲和性**，通过Worker节点的Label标签进行设置。

节点调度支持硬约束和软约束（Required/Preferred），以及丰富的匹配表达式（In, NotIn, Exists, DoesNotExist. Gt, and Lt）：

    -   **必须满足**，即硬约束，一定要满足，对应requiredDuringSchedulingIgnoredDuringExecution，效果与`NodeSelector`相同。本例中Pod只能调度到具有对应标签的Worker节点。您可以定义多条硬约束规则，但只需满足其中一条。
    -   **尽量满足**，即软约束，不一定满足，对应preferredDuringSchedulingIgnoredDuringExecution。调度会尽量调度Pod到具有对应标签的Node节点。您还可为软约束规则设定权重，具体调度时，若存在多个符合条件的节点，权重最大的节点会被优先调度。您可定义多条软约束规则，但必须满足全部约束，才会进行调度。 |
    |应用亲和性

|决定应用的Pod可以和哪些Pod部署在同一拓扑域。例如，对于相互通信的服务，可通过应用亲和性调度，将其部署到同一拓扑域（如同一个主机）中，减少它们之间的网络延迟。

根据节点上运行的Pod的标签（Label）来进行调度，支持硬约束和软约束，匹配的表达式有：`In, NotIn, Exists, DoesNotExist`。

    -   **必须满足**，即硬约束，一定要满足，对应requiredDuringSchedulingIgnoredDuringExecution，Pod的亲和性调度必须要满足后续定义的约束条件。
        -   **命名空间**：该策略是依据Pod的Label进行调度，所以会受到命名空间的约束。
        -   **拓扑域**：即topologyKey，指定调度时作用域，这是通过Node节点的标签来实现的，例如指定为`kubernetes.io/hostname`，那就是以Node节点为区分范围；如果指定为`beta.kubernetes.io/os`，则以Node节点的操作系统类型来区分。
        -   **选择器**：单击选择器右侧的加号按钮，您可添加多条硬约束规则。
        -   **查看应用列表**：单击**应用列表**，弹出对话框，您可在此查看各命名空间下的应用，并可将应用的标签导入到亲和性配置页面。
        -   硬约束条件：设置已有应用的标签、操作符和标签值。本例中，表示将待创建的应用调度到该主机上，该主机运行的已有应用具有`app:nginx`标签。
    -   **尽量满足**，即软约束，不一定满足，对应preferredDuringSchedulingIgnoredDuringExecution。Pod的亲和性调度会尽量满足后续定义的约束条件。对于软约束规则，您可配置每条规则的权重，其他配置规则与硬约束规则相同。

**说明：** **权重**：设置一条软约束规则的权重，介于1~100，通过算法计算满足软约束规则的节点的权重，将Pod调度到权重最大的节点上。 |
    |应用非亲和性

|决定应用的Pod不与哪些Pod部署在同一拓扑域。应用非亲和性调度的场景包括：

    -   将一个服务的Pod分散部署到不同的拓扑域（如不同主机）中，提高服务本身的稳定性。
    -   给予Pod一个节点的独占访问权限来保证资源隔离，保证不会有其它Pod来分享节点资源。
    -   把可能会相互影响的服务的Pod分散在不同的主机上。
**说明：** 应用非亲和性调度的设置方式与亲和性调度相同，但是相同的调度规则代表的意思不同，请根据使用场景进行选择。 |
    |调度容忍

|容忍被应用于Pod，允许这个Pod被调度到相对应的污点上。 |
    |调度到虚拟节点

|设置是否调度到虚拟节点。如果您集群中没有虚拟节点，则无法设置该配置项。 |

4.  在**标签和注释**区域，单击**添加**设置容器组的标签和注释。

    |参数|描述|
    |--|--|
    |Pod标签

|为该Pod添加一个标签，标识该应用。 |
    |Pod注解

|为该Pod添加一个注解（Annotation）。 |

5.  单击**创建**。


**步骤四：查看应用**

在**创建完成**配置向导页面中查看应用任务。

1.  在**创建应用任务已提交**面板中单击**查看应用详情**。

    ![查看详情](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/3506659951/p10983.png)

    默认进入新建的nginx-deployment的详情页面。

    **说明：** 您也可以通过以下操作创建路由与服务。如上图所示，在**访问方式**页签。

    -   单击服务右侧的**创建**，也可以创建服务。
    -   单击路由右侧的**创建**，也可以创建路由。
2.  单击左侧导航栏的**路由与负载均衡** \> **路由**，可以看到路由列表下出现一条规则。

    ![路由规则](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/4054404061/p10985.png)

3.  在浏览器中访问路由测试域名，您可访问Nginx欢迎页。

    ![访问nginx](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/3506659951/p10986.png)


**相关操作**

**查看无状态工作负载详情**

在左侧导航栏单击**集群**，单击目标集群名称或目标集群**操作**列下的**详情**，选择**工作负载** \> **无状态**，在无状态页面单击目标应用名称或目标应用**操作**列下的**详情**。

**说明：** 在无状态列表中单击**标签**字段，输入对应应用标签的key与`value`，单击**确定**后可过滤列表中的应用。

在应用详情页面您可以编辑、伸缩、查看YAML、重新部署、及刷新应用。

|操作项|描述|
|---|--|
|**编辑**|在应用详情页面单击**编辑**，您可以修改应用信息。|
|**伸缩**|在应用详情页面单击**伸缩**，您可以修改所需容器组数量。|
|**查看YAML**|在应用详情页面单击**查看Yaml**，您可以**更新**、**下载**、**另存为**YAML文件。|
|**重新部署**|在应用详情页面单击**重新部署**，您可以重新部署应用。|
|**刷新**|在应用详情页面单击**刷新**，您可以刷新应用。|

**设置已创建的无状态工作负载**

在无状态页面的目标应用**操作**列下，单击**更多**后，您可以进行以下操作：

|操作项|描述|
|---|--|
|**查看Yaml**|查看应用的YAML内容。|
|**重新部署**|重新部署应用。|
|**编辑标签**|为应用设置标签。|
|**节点亲和性**|为应用设置节点亲和性。更多信息，请参见[调度配置](#step_csu_4sv_gz8)。|
|**弹性伸缩**|为应用设置弹性伸缩。更多信息，请参见[容器水平伸缩（HPA）](/intl.zh-CN/Kubernetes集群用户指南/弹性伸缩/容器水平伸缩（HPA）.md)和[容器定时伸缩（CronHPA）](/intl.zh-CN/Kubernetes集群用户指南/弹性伸缩/容器定时伸缩（CronHPA）.md)。|
|**调度容忍**|设置应用的容忍策略。更多信息，请参见[调度配置](#step_csu_4sv_gz8)。|
|**升级策略**|设置应用升级的策略：-   **滚动升级**：分批次逐步替换应用Pod实例。
-   **替换升级**：先暂停老版应用Pod，用新版Pod替换。 |
|**复制创建**|复制应用相同的容器设置。|
|**回滚**|回滚至应用之前发布的版本。|
|**日志**|查看应用的日志信息。|
|**删除**|删除应用。|

## 通过编排模板创建Linux应用

在容器服务Kubernetes模板编排中，您需要自己定义一个应用运行所需的资源对象，通过标签选择器等机制，将资源对象组合成一个完整的应用。

本例演示如何通过一个编排模板创建Nginx应用，包含一个Deployment和Service，后端Deployment会创建Pod资源对象，Service会绑定到后端Pod上，形成一个完整的Nginx应用。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

5.  在无状态（Deployment）页面，单击右上角的**使用YAML创建资源**。

6.  对模板进行相关配置，完成配置后单击**创建**。

    -   **命名空间**：在创建页面的顶部选择资源对象所属的命名空间，默认是Default。除了节点、持久化存储卷等底层计算资源以外，大多数资源对象需要作用于命名空间。
    -   **示例模板**：阿里云容器服务提供了多种资源类型的Kubernetes YAML示例模板，让您快速部署资源对象。您可以根据Kubernetes YAML编排的格式要求自主编写，来描述您想定义的资源类型。
    -   **添加工作负载**：您可通过此功能快速定义一个YAML模板。
    -   **使用已有模板**：您可将已有编排模板导入到模板配置页面。
    -   **保存模板**：您可以保存设置好的编排模板。
    下面是一个Nginx应用的示例编排，基于容器服务内置的编排模板。通过该编排模板，即可快速创建一个属于Nginx应用的Deployment。

    **说明：**

    -   容器服务支持Kubernetes YAML编排，支持通过`---`符号将资源对象分隔，从而通过一个模板创建多个资源对象。
    -   在应用中通过Volume方式挂载存储卷时，默认情况下挂载目录下的文件在挂载后将会被覆盖。若要不影响挂载目录下的其他文件，您可以配置`subPath`参数。
    ```
    apiVersion: apps/v1 
    kind: Deployment
    metadata:
        name: nginx-deployment-basic
        labels:
          app: nginx
    spec:
        replicas: 2
        selector:
          matchLabels:
            app: nginx
        template:
          metadata:
            labels:
              app: nginx
          spec:
            containers:
            - name: nginx
              image: nginx:1.7.9 # replace it with your exactly <image_name:tags>
              ports:
              - containerPort: 80
              volumeMounts:
                - name: nginx-config
                  mountPath: /etc/nginx/nginx.conf 
                  subPath: nginx.conf   #设置subPath。    
          volumes:
            - name: nginx-config
              configMap:
                name: nginx-conf
    ---
    
    apiVersion: v1     
    kind: Service
    metadata:
       name: my-service1        #TODO: to specify your service name
       labels:
         app: nginx
    spec:
       selector:
         app: nginx             #TODO: change label selector to match your backend pod
       ports:
       - protocol: TCP
         name: http
         port: 30080            #TODO: choose an unique port on each node to avoid port conflict
         targetPort: 80
       type: LoadBalancer        ##本例中将type从NodePort修改为LoadBalancer。
    ---
    
    #存储卷的配置项
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: nginx-conf
      namespace: default
    data:
      nginx.conf: |-
       user  nginx;
       worker_processes  1;
       error_log  /var/log/nginx/error.log warn;
       pid        /var/run/nginx.pid;
       events {
            worker_connections  1024;
        }
        http {
            include       /etc/nginx/mime.types;
            default_type  application/octet-stream;
            log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                              '$status $body_bytes_sent "$http_referer" '
                              '"$http_user_agent" "$http_x_forwarded_for"';
            access_log  /var/log/nginx/access.log  main;
            sendfile        on;
            #tcp_nopush     on;
            keepalive_timeout  65;
            #gzip  on;
            include /etc/nginx/conf.d/*.conf;
        } 
    ```

7.  单击**创建**后。会提示部署状态信息。


## 通过命令管理应用

您可以通过命令创建应用或者查看应用的容器。

**通过命令创建应用**

1.  执行以下命令启动容器（本示例中为Nginx Web服务器）。

    ```
     kubectl run -it nginx --image=registry.aliyuncs.com/spacexnice/netdia:latest
    ```

2.  执行以下命令，为该容器创建一个服务入口，指定`--type=LoadBalancer`将会为您创建一个阿里云负载均衡路由到该Nginx容器。

    ```
    kubectl expose deployment nginx --port=80 --target-port=80 --type=LoadBalancer
    ```


**通过命令查看容器**

运行`kubectl get pods`命令，列出所有default命名空间里正在运行的容器。

```
NAME                                   READY     STATUS    RESTARTS   AGE
nginx-2721357637-d****                 1/1       Running   1          9h
```

## 使用镜像密钥创建应用

容器服务Kubernetes集群支持通过Web界面使用镜像密钥，包括新建镜像密钥和使用已有镜像密钥。

您在使用私有镜像创建应用时，为了保障镜像安全，通常需要为镜像设置密钥。通过容器服务控制台，您可方便地将私有镜像库的身份认证信息通过docker-registry类型的Secret传入到Kubernetes中。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

5.  在**无状态**页面，设置命名空间，然后单击右上角**使用镜像创建**。

6.  在**应用基本信息**页面设置相关参数。更多信息，请参见[应用基本信息](#table_tf4_nzo_3q5)。

7.  设置容器配置。

    本例仅对容器镜像密钥的配置进行说明，关于容器配置说明，请参见[容器基本配置](#table_fbo_lyv_om2)。

    1.  在**容器配置**页面，首先配置镜像名称，在**镜像名称栏**中输入私有镜像地址，填写格式是`domainname/namespace/imagename`。

        **说明：** 公共镜像无须配置镜像密钥。

    2.  在镜像版本栏中，输入私有镜像地址版本。

        本文示例使用Tomcat私有镜像。

        ![容器配置](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/9085659951/p13583.png)

    3.  单击**设置镜像密钥**，在弹出的对话框中进行设置。

        -   选择**新建密钥**，您需要配置以下参数。

            |参数|描述|
            |--|--|
            |**密钥名称**（regsecret）|指定密钥的键名称，可自行定义。|
            |**仓库域名**（docker-server）|指定Docker仓库地址。若输入阿里云容器服务镜像仓库，会默认补齐。|
            |**用户名**（docker-username）|指定Docker仓库用户名。若使用阿里云容器镜像服务，则为阿里云登录账号（包括阿里云主账号和子账号）。|
            |**密码**（docker-password）|指定Docker仓库登录密码。若使用阿里云容器镜像服务，即容器镜像独立登录密码。|
            |**邮箱**（docker-email）|指定邮件地址。非必选。|

            单击**确定**，您可看到密钥已经成功创建。

            ![设置密钥](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/9085659951/p13585.png)

        -   您也可单击**已有密钥**。您可通过命令行或YAML文件等方式预先创建好容器镜像密钥，具体操作，请参见[如何支持私有镜像]()和[使用私有镜像仓库创建应用](/intl.zh-CN/快速入门/高阶入门/使用私有镜像仓库创建应用.md)。

            ![已有密钥](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/9085659951/p13586.png)

8.  根据页面引导，完成其他配置，最后单击**创建**。

    更多信息，请参见本文的步骤三：完成高级配置。

9.  在控制台左侧导航栏中，单击**集群**。

10. 在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

11. 在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

12. 在无状态页面，查看目标应用运行情况。

    您可看到，Tomcat应用正常运行，成功通过密钥使用Tomcat私有镜像。


## 相关文档

-   [创建有状态工作负载StatefulSet](/intl.zh-CN/Kubernetes集群用户指南/应用/工作负载/创建有状态工作负载StatefulSet.md)
-   [通过Annotation配置负载均衡](/intl.zh-CN/Kubernetes集群用户指南/网络/Service管理/通过Annotation配置负载均衡.md)

