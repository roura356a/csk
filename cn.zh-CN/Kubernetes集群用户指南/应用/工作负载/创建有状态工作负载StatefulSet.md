---
keyword: [有状态工作负载, StatefulSet]
---

# 创建有状态工作负载StatefulSet

阿里云容器服务Kubernetes版ACK（Container Service for Kubernetes）集群支持通过界面创建StatefultSet类型的应用，满足您快速创建有状态应用的需求。本例中将创建一个Nginx的有状态应用，并演示StatefulSet应用的特性。

使用镜像创建有状态StatefulSet应用前，请确保您已经进行以下操作：

-   [创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)
-   [创建持久化存储卷声明](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/创建持久化存储卷声明.md)
-   [通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)

StatefulSet包括以下特性：

|场景|说明|
|--|--|
|Pod一致性|包含次序（启动、停止次序）、网络一致性。此一致性与Pod相关，与被调度到哪个Node节点无关。|
|稳定的持久化存储|通过VolumeClaimTemplate为每个Pod创建一个PV。删除、减少副本，不会删除相关的卷。|
|稳定的网络标志|Pod的hostname模式为：`（statefulset名称）−（序号）`。|
|稳定的次序|对于N个副本的StatefulSet，每个Pod都在 \[0，N）的范围内分配一个数字序号，且是唯一的。|

## 操作步骤

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**工作负载** \> **有状态**。

5.  在**有状态**页面中，单击**使用镜像创建**。

6.  在**应用基本信息**页签中，设置应用的基本信息。

    本例中选择**有状态**类型，创建StatefulSet类型的应用。

    |配置项|描述|
    |---|--|
    |应用名称|设置应用的名称。|
    |命名空间|设置应用部署所处的命名空间，默认使用default命名空间。|
    |副本数量|应用包含的Pod数量。|
    |类型|定义资源对象的类型，可选择**无状态**、**有状态**、**任务**、**定时**、**定时任务**、**守护进程集**。|
    |标签|为该应用添加一个标签，标识该应用。|
    |注解|为该应用添加一个注解（annotation）。|
    |时区同步|容器与节点是否使用相同的时区。|

7.  单击**下一步**进入**容器配置**页签。

8.  设置容器相关的配置。

    **说明：** 在**容器配置**页签上方，单击**添加容器**为应用的Pod设置多个容器。

    容器配置的配置项及描述如下。

    -   基本配置

        |配置项|描述|
        |---|--|
        |镜像名称|        -   您可以单击**选择镜像**，选择所需的镜像并单击**确定**。本例中为官方Nginx镜像，可以在**搜索**页签中，选择**Docker镜像**，然后在文本框中输入nginx进行搜索。
            -   容器镜像服务：在**容器镜像服务**页签中，可以选择托管在容器镜像服务ACR中的镜像。需要选择镜像所属地域，以及镜像服务实例。关于ACR的更多信息，请参见[容器镜像服务企业版概述]()。

**说明：** 您可以在**容器镜像服务**页签，根据名称搜索托管在容器镜像服务ACR中的镜像。

            -   Docker官方镜像：在**Docker官方镜像**页签中，可以选择Docker官方提供的容器镜像。
            -   用户收藏：在**用户收藏**页签中，可以选择您收藏的Docker官方镜像。
            -   搜索：在**搜索**页签中，可以分别在ACR的特定地域和Docker官方镜像中搜索容器镜像。
        -   您还可以填写私有镜像。填写的格式为`domainname/namespace/imagename:tag`。 |
        |镜像Tag

|        -   您可以单击**选择镜像Tag**选择镜像的版本。若不指定，默认为最新版。
        -   ACK支持以下三种镜像拉取策略（imagePullPolicy）：

            -   **优先使用本地镜像（ifNotPresent）**：如果本地有该镜像，则使用本地镜像，本地不存在时拉取镜像。
            -   **总是拉取镜像（Always）**：表示每次部署或扩容都会从容器镜像服务重新拉取镜像，而不会从本地拉取镜像。
            -   **仅使用本地镜像（Never）**：仅使用本地镜像。
**说明：** 当您选中**镜像拉取策略**时，默认不设置镜像拉取策略。

        -   单击**设置镜像密钥**，您可以实现免密拉取镜像。具体操作，请参见[使用免密组件拉取容器镜像](/cn.zh-CN/Kubernetes集群用户指南/应用/镜像/使用免密组件拉取容器镜像.md)。 |
        |资源限制|可指定该应用所能使用的资源上限，包括CPU、内存和ephemeral-storage三种资源，防止占用过多资源。其中，CPU资源的单位为millicores，即一个核的千分之一；内存的单位为GiB；ephemeral-storage的单位为GiB。|
        |所需资源|即为该应用预留资源额度，包括CPU和内存两种资源，即容器独占该资源，防止因资源不足而被其他服务或进程争夺资源，导致应用不可用。|
        |容器启动项|        -   stdin：将控制台输入发送到容器。
        -   tty：将标准输入控制台作为容器的控制台输入。 |
        |特权容器|        -   选择特权容器，则privileged=true，开启特权模式。
        -   不选择特权容器，则privileged=false，关闭特权模式。 |
        |Init Container|选中该项，表示创建一个Init Container。Init Container包含一些实用的工具，详情请参见[Init Containers](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/?spm=a2c4g.11186623.2.13.3fdd30dfnyevPx)。|

    -   端口设置

        设置容器的端口。

        -   名称：设置容器端口名称。
        -   容器端口：设置暴露的容器访问端口或端口名，端口号必须介于1~65535。
        -   协议：支持TCP和UDP。
    -   环境变量

        支持通过键值对的形式为Pod配置环境变量。用于给Pod添加环境标志或传递配置等，具体请参见[Pod variable](https://kubernetes.io/docs/tasks/inject-data-application/environment-variable-expose-pod-information/?spm=0.0.0.0.8VJbrE)。

        -   类型：设置环境变量的类型，支持**自定义**、**配置项**、**密钥**和**变量/变量引用**。配置项、密钥支持全部文件的引用，以密钥为例。

            选择**密钥**，选择**变量**，默认全部文件引用。

            ![环境变量](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3506659951/p130410.png)

            对应的yaml，则引用了整个Secret。

            ![yaml](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3506659951/p130413.jpg)

        -   变量名称：设置环境变量名称。
        -   变量/变量引用：设置变量引用的值。
    -   健康检查

        支持存活检查（liveness）、就绪检查（Readiness）和启动探测（Startup Probes）。存活检查用于检测何时重启容器；就绪检查确定容器是否已经就绪，且可以接受流量；启动探测用于检测何时启动容器。

        **说明：** 仅Kubernetes集群1.18及之后版本支持启动探测。

        关于健康检查的更多信息，请参见[配置存活、就绪和启动探测器](https://kubernetes.io/zh/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)。

        |请求类型|配置说明|
        |----|----|
        |HTTP请求|即向容器发送一个HTTPget请求，支持的参数包括：        -   协议：HTTP/HTTPS。
        -   路径：访问HTTP server的路径。
        -   端口：容器暴露的访问端口或端口名，端口号必须介于1~65535。
        -   HTTP头：即HTTPHeaders，HTTP请求中自定义的请求头，HTTP允许重复的header。支持键值对的配置方式。
        -   延迟探测时间（秒）：即initialDelaySeconds，容器启动后第一次执行探测时需要等待多少秒，默认为3秒。
        -   执行探测频率（秒）：即periodSeconds，指执行探测的时间间隔，默认为10秒，最小为1秒。
        -   超时时间（秒）：即timeoutSeconds，探测超时时间。默认1秒，最小1秒。
        -   健康阈值：探测失败后，最少连续探测成功多少次才被认定为成功。默认是1，最小值是1。对于存活检查（liveness）必须是1。
        -   不健康阈值：探测成功后，最少连续探测失败多少次才被认定为失败。默认是3，最小值是1。 |
        |TCP连接|即向容器发送一个TCP Socket，kubelet将尝试在指定端口上打开容器的套接字。 如果可以建立连接，容器被认为是健康的，如果不能就认为是失败的。支持的参数包括：        -   端口：容器暴露的访问端口或端口名，端口号必须介于1~65535。
        -   延迟探测时间（秒）：即initialDelaySeconds，容器启动后第一次执行探测时需要等待多少秒，默认为15秒。
        -   执行探测频率（秒）：即periodSeconds，指执行探测的时间间隔，默认为10秒，最小为1秒。
        -   超时时间（秒）：即timeoutSeconds，探测超时时间。默认1秒，最小1秒。
        -   健康阈值：探测失败后，最少连续探测成功多少次才被认定为成功。默认是1，最小值是1。对于存活检查（liveness）必须是1。
        -   不健康阈值：探测成功后，最少连续探测失败多少次才被认定为失败。默认是3，最小值是1。 |
        |命令行|通过在容器中执行探针检测命令，来检测容器的健康情况。支持的参数包括：        -   命令行：用于检测容器健康情况的探测命令。
        -   延迟探测时间（秒）：即initialDelaySeconds，容器启动后第一次执行探测时需要等待多少秒，默认为5秒。
        -   执行探测频率（秒）：即periodSeconds，指执行探测的时间间隔，默认为10秒，最小为1秒。
        -   超时时间（秒）：即timeoutSeconds，探测超时时间。默认1秒，最小1秒。
        -   健康阈值：探测失败后，最少连续探测成功多少次才被认定为成功。默认是1，最小值是1。对于存活检查（liveness）必须是1。
        -   不健康阈值：探测成功后，最少连续探测失败多少次才被认定为失败。默认是3，最小值是1。 |

    -   生命周期

        您可以为容器的生命周期配置启动执行、启动后处理和停止前处理。具体参见[配置生命周期](https://kubernetes.io/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/)。

        -   **启动执行**：为容器设置预启动命令和参数。
        -   **启动后处理**：为容器设置启动后的命令。
        -   **停止前处理**：为容器设置预结束命令。
        ![生命周期](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3506659951/p134222.png)

    -   数据卷

        支持增加本地存储和云存储声明（PVC）。

        -   **本地存储**：支持主机目录（HostPath）、配置项（ConfigMap）、保密字典（Secret）和临时目录，将对应的挂载源挂载到容器路径中。更多信息参见[volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE)。
        -   **云存储声明（PVC）**：支持云存储。
        本例中配置了一个云存储类型的数据卷声明disk-ssd，将其挂载到容器的/tmp路径下。

        ![配置数据卷](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3506659951/p12307.png)

    -   日志配置

        配置**日志服务**，您可进行采集配置和自定义Tag设置。

        **说明：** 请确保已部署Kubernetes集群，并且在此集群上已安装日志插件。

        |配置项|描述|
        |---|--|
        |采集配置|日志库：即在日志服务中生成一个对应的Logstore，用于存储采集到的日志。|
        |容器内日志路径：支持stdout和文本日志。

        -   stdout：表示采集容器的标准输出日志。
        -   文本日志：表示收集容器内指定路径的日志，本例中表示收集/var/log/nginx下所有的文本日志，也支持通配符的方式。 |
        |自定义Tag|您还可设置自定义Tag，设置Tag后，会将该Tag一起采集到容器的日志输出中。自定义Tag可帮助您给容器日志打上Tag，方便进行日志统计和过滤等分析操作。|

9.  完成容器配置后，单击**下一步**。

10. 进行高级配置。

    -   访问设置

        **说明：**

        针对应用的通信需求，您可灵活进行访问设置：

        -   内部应用：对于只在集群内部工作的应用，您可根据需要创建ClusterIP或NodePort类型的服务，来进行内部通信。
        -   外部应用：对于需要暴露到公网的应用，您可以采用两种方式进行访问设置。
            -   创建LoadBalancer类型的服务：使用阿里云提供的负载均衡服务SLB（Server Load Balancer），该服务提供公网访问能力。
            -   创建路由（Ingress）：通过路由（Ingress）提供公网访问能力，详情请参见[Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/?spm=a2c4g.11186623.2.23.3fdd30dfnyevPx)。
        您可以设置暴露后端Pod的方式。本例中选择ClusterIP服务和路由（Ingress），构建一个公网可访问的Nginx应用。

        |配置项|描述|
        |---|--|
        |服务（Service）|在**服务（Service）**右侧，单击**创建**设置**创建服务**的配置项。配置项的详情，请参见[管理服务](/cn.zh-CN/Kubernetes集群用户指南/网络/Service管理/管理服务.md)。本例中服务类型选择**ClusterIP**。|
        |路由（Ingress）|在**路由（Ingress）**右侧，单击**创建**。在弹出的对话框中，为后端Pod配置路由规则。详细的路由配置信息，请参见[创建Ingress](/cn.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/Ingress基本操作.md)。**说明：** 通过镜像创建应用时，您仅能为一个服务创建路由（Ingress）。本例中使用一个虚拟主机名称作为测试域名，您需要在Hosts中添加一条记录。在实际工作场景中，请使用备案域名。

        ```
101.37.224.146   foo.bar.com    #即ingress的IP
        ``` |

        在**访问设置**区域中，您可看到创建完毕的服务和路由，您可单击**变更**和**删除**进行二次配置。

    -   伸缩配置

        在**伸缩配置**区域，配置是否开启**指标伸缩**和**定时伸缩**，从而满足应用在不同负载下的需求。

        -   容器服务支持容器组的指标伸缩，即根据容器CPU和内存资源占用情况自动调整容器组数量。

            **说明：** 若要启用自动伸缩，您必须为容器设置所需资源，否则容器自动伸缩无法生效。

            |配置项|描述|
            |---|--|
            |指标|支持CPU和内存，需要和设置的所需资源类型相同。|
            |触发条件|资源使用率的百分比，超过该使用量，容器开始扩容。|
            |最大副本数量|该应用可扩容的容器数量上限。|
            |最小副本数量|该应用可缩容的容器数量下限。|

        -   容器服务支持容器组的定时伸缩，即定时地对容器服务Kubernetes集群进行扩缩容。开启定时伸缩前，您需要先安装ack-kubernetes-cronhpa-controller。关于定时伸缩的详细说明，请参见[创建定时伸缩CronHPA任务](/cn.zh-CN/Kubernetes集群用户指南/弹性伸缩/容器定时伸缩（CronHPA）.md)。
    -   调度设置

        您可设置升级方式、节点亲和性、应用亲和性和应用非亲和性，详情请参见[Affinity and anti-affinity](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/?spm=a2c4g.11186623.2.31.3fdd30dfnyevPx#affinity-and-anti-affinity)。

        **说明：** 亲和性调度依赖节点标签和Pod标签。您可使用内置的标签进行调度；也可预先为节点、Pod配置相关的标签。

        |配置项|描述|
        |---|--|
        |升级方式|升级方式包括滚动升级（Rollingupdate）和替换升级（Recreate），详细请参见[Deployments](https://kubernetes.io/zh/docs/concepts/workloads/controllers/deployment/?spm=a2c4g.11186623.2.32.3fdd30dfnyevPx)。 |
        |节点亲和性|设置**节点亲和性**，通过Worker节点的Label标签进行设置。节点调度支持硬约束和软约束（Required/Preferred），以及丰富的匹配表达式（In, NotIn, Exists, DoesNotExist. Gt, and Lt）：

        -   **必须满足**，即硬约束，一定要满足，对应requiredDuringSchedulingIgnoredDuringExecution，效果与`NodeSelector`相同。本例中Pod只能调度到具有对应标签的Worker节点。您可以定义多条硬约束规则，但只需满足其中一条。
        -   **尽量满足**，即软约束，不一定满足，对应preferredDuringSchedulingIgnoredDuringExecution。本例中，调度会尽量不调度Pod到具有对应标签的Node节点。您还可为软约束规则设定权重，具体调度时，若存在多个符合条件的节点，权重最大的节点会被优先调度。您可定义多条软约束规则，但必须满足全部约束，才会进行调度。 |
        |应用亲和性|决定应用的Pod可以和哪些Pod部署在同一拓扑域。例如，对于相互通信的服务，可通过应用亲和性调度，将其部署到同一拓扑域（如同一个主机）中，减少它们之间的网络延迟。根据节点上运行的Pod的标签（Label）来进行调度，支持硬约束和软约束，匹配的表达式有：`In, NotIn, Exists, DoesNotExist`。

        -   **必须满足**，即硬约束，一定要满足，对应requiredDuringSchedulingIgnoredDuringExecution，Pod的亲和性调度必须要满足后续定义的约束条件。
            -   **命名空间**：该策略是依据Pod的Label进行调度，所以会受到命名空间的约束。
            -   **拓扑域**：即TopologyKey，指定调度时作用域，这是通过Node节点的标签来实现的，例如指定为`kubernetes.io/hostname`，那就是以Node节点为区分范围；如果指定为`beta.kubernetes.io/os`，则以Node节点的操作系统类型来区分。
            -   **选择器**：单击选择器右侧的加号按钮，您可添加多条硬约束规则。
            -   **查看应用列表**：单击**应用列表**，弹出对话框，您可在此查看各命名空间下的应用，并可将应用的标签导入到亲和性配置页面。
            -   硬约束条件：设置已有应用的标签、操作符和标签值。本例中，表示将待创建的应用调度到该主机上，该主机运行的已有应用具有`app:nginx`标签。
        -   **尽量满足**，即软约束，不一定满足，对应preferredDuringSchedulingIgnoredDuringExecution。Pod的亲和性调度会尽量满足后续定义的约束条件。对于软约束规则，您可配置每条规则的权重，其他配置规则与硬约束规则相同。

**说明：** **权重**：设置一条软约束规则的权重，介于1~100，通过算法计算满足软约束规则的节点的权重，将Pod调度到权重最大的节点上。 |
        |应用非亲和性|决定应用的Pod不与哪些Pod部署在同一拓扑域。应用非亲和性调度的场景包括：

        -   将一个服务的Pod分散部署到不同的拓扑域（如不同主机）中，提高服务本身的稳定性。
        -   给予Pod一个节点的独占访问权限来保证资源隔离，保证不会有其它Pod来分享节点资源。
        -   把可能会相互影响的服务的Pod分散在不同的主机上。
**说明：** 应用非亲和性调度的设置方式与亲和性调度相同，但是相同的调度规则代表的意思不同，请根据使用场景进行选择。 |
        |调度容忍|容忍被应用于Pod，允许这个Pod被调度到相对应的污点上。|
        |调度到虚拟节点|设置是否调度到虚拟节点。如果您集群中没有虚拟节点，则无法设置该配置项。|

    -   标签和注释
        -   Pod标签：为该Pod添加一个标签，标识该应用。
        -   Pod注解：为该Pod添加一个注解（annotation）。
11. 单击**创建**。

12. 创建成功后，默认进入创建完成页面，会列出应用包含的对象，您可以单击**查看应用详情**进行查看。

    默认进入有状态副本集详情页面。

13. 然后单击左上角**返回列表**，进入有状态副本集列表页面，查看创建的StatefulSet应用。

14. 选择所需的Nginx应用，单击右侧**伸缩**，验证服务伸缩性。

    1.  在弹出的对话框中，将容器组数量设置为3，您可发现扩容时，扩容容器组的排序依次递增；反之，进行缩容时，先按Pod次序从高到低进行缩容。这体现StatefulSet中Pod的次序稳定性。

        ![验证服务伸缩](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5975659951/p12438.png)

    2.  单击左侧导航栏中的**存储卷** \> **存储声明**，您可发现，随着应用扩容，会随着Pod创建新的云存储卷。缩容后，已创建的PV和PVC不会删除。


## 验证持久化存储特性

连接到Master节点，执行以下命令，验证持久化存储特性。

1.  执行以下命令在云盘中创建临时文件。

    ```
    kubectl exec nginx-1 ls /tmp            #列出该目录下的文件lost+found。
    
    kubectl exec nginx-1 touch /tmp/statefulset         #增加一个临时文件statefulset。
    
    kubectl exec nginx-1 ls /tmp
    ```

    预期输出：

    ```
    lost+found
    statefulset
    ```

2.  执行以下命令删除Pod，验证数据持久性。

    ```
    kubectl delete pod nginx-1
    ```

    预期输出：

    ```
    pod"nginx-1" deleted
    ```

3.  过一段时间，待Pod自动重启后，验证数据持久性，证明StatefulSet应用的高可用性。

    ```
    kubectl exec nginx-1 ls /tmp                         #数据持久化存储lost+found
    ```

    预期输出：

    ```
    statefulset
    ```


更多信息，请参见[Kubernetes有状态服务-StatefulSet使用最佳实践](https://yq.aliyun.com/articles/629007?spm=a2c4e.11163080.searchblog.9.54472ec1NC0KbK)。

## 相关操作

在左侧导航栏单击**集群**，单击目标集群名称或目标集群**操作**列下的**应用管理**，在集群管理页左侧导航栏中选择**工作负载** \> **有状态**，在有状态页面单击目标应用名称或目标应用**操作**列下的**详情**，在应用详情页面您可以**编辑**、**伸缩**、**查看Yaml**、**重新部署**、**刷新**应用。

-   编辑：在应用详情页面单击**编辑**，您可以修改应用信息。
-   伸缩：在应用详情页面单击**伸缩**，您可以修改所需容器组数量。
-   查看Yaml：在应用详情页面单击**查看Yaml**，您可以**更新**、**下载**、**另存为**Yaml文件。
-   重新部署：在应用详情页面单击**重新部署**，您可以重新部署应用。
-   刷新：在应用详情页面单击**刷新**，您可以刷新应用。

