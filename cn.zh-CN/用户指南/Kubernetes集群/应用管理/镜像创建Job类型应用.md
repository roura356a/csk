# 镜像创建Job类型应用 {#task_o24_h1s_3fb .task}

阿里云容器服务Kubernetes集群支持通过界面创建Job类型的应用。本例中将创建一个Job类型的busybox应用，并演示任务（Job）应用的特性。

您已成功创建一个 Kubernetes 集群。参见[创建Kubernetes集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/创建Kubernetes集群.md#)。

Job负责批量处理短暂的一次性任务 \(short lived one-off tasks\)，即仅执行一次的任务，它保证批处理任务的一个或多个Pod成功结束。

Kubernetes支持以下几种Job：

-   非并行Job：通常创建一个Pod直至其成功结束
-   固定结束次数的Job：设置`.spec.completions`，创建多个Pod，直到`.spec.completions`个Pod成功结束
-   带有工作队列的并行Job：设置`.spec.Parallelism`但不设置`.spec.completions`，当所有Pod结束并且至少一个成功时，Job就认为是成功。
-   固定结束次数的并行Job：同时设置`.spec.completions`和`.spec.Parallelism`，多个Pod同时处理工作队列。

根据`.spec.completions`和`.spec.Parallelism`的设置，可以将Job划分为以下几种模式：

**说明：** 本例中创建的任务属于固定结束次数的并行Job。

|Job类型|使用示例|行为|completions|Parallelism|
|-----|----|--|-----------|-----------|
|一次性Job|数据库迁移|创建一个Pod直至其成功结束|1|1|
|固定结束次数的Job|处理工作队列的Pod|依次创建一个Pod运行直至completions个成功结束|2+|1|
|固定结束次数的并行Job|多个Pod同时处理工作队列|依次创建多个Pod运行直至completions个成功结束|2+|2+|
|并行Job|多个Pod同时处理工作队列|创建一个或多个Pod直至有一个成功结束|1|2+|

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。 
2.  在Kubernetes菜单下，单击左侧导航栏中的**应用** \> **任务**，然后单击页面右上角的**使用镜像创建**。 
3.  在应用基本信息页面进行设置，然后单击**下一步** 进入应用配置页面。 

    -   **应用名称**：设置应用的名称。
    -   **部署集群**：设置应用部署的集群。
    -   **命名空间**：设置应用部署所处的命名空间，默认使用default命名空间。
    -   **类型**：设置类型为**任务**。

        **说明：** 本例中选择**任务**类型，即Job。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23357/155558094913569_zh-CN.png)

4.  设置容器配置。 

    **说明：** 您可为应用的Pod设置多个容器。

    1.  设置容器的基本配置。 

        -   **镜像名称**：您可以单击**选择镜像**，在弹出的对话框中选择所需的镜像并单击**确定**，本例中为 busybox。

            您还可以填写私有 registry。填写的格式为`domainname/namespace/imagename:tag`

        -   **镜像版本**：您可以单击**选择镜像版本** 选择镜像的版本。若不指定，默认为 latest。
        -   **总是拉取镜像**：为了提高效率，容器服务会对镜像进行缓存。部署时，如果发现镜像 Tag 与本地缓存的一致，则会直接复用而不重新拉取。所以，如果您基于上层业务便利性等因素考虑，在做代码和镜像变更时没有同步修改 Tag ，就会导致部署时还是使用本地缓存内旧版本镜像。而勾选该选项后，会忽略缓存，每次部署时重新拉取镜像，确保使用的始终是最新的镜像和代码。
        -   **设置镜像密钥**：若您在使用私有镜像时，您可使用镜像密钥，保障镜像安全。具体配置请参见[使用镜像密钥](intl.zh-CN/用户指南/Kubernetes集群/应用管理/使用镜像密钥.md#)。
        -   **资源限制**：可指定该应用所能使用的资源上限，包括 CPU 和 内存两种资源，防止占用过多资源。其中，CPU 资源的单位为 millicores，即一个核的千分之一；内存的单位为 Bytes，可以为 Gi、Mi 或 Ki。
        -   **所需资源**：即为该应用预留资源额度，包括 CPU 和 内存两种资源，即容器独占该资源，防止因资源不足而被其他服务或进程争抢资源，导致应用不可用。
        -   **Init Container**：勾选该项，表示创建一个Init Container，Init Container包含一些实用的工具，具体参见[https://kubernetes.io/docs/concepts/workloads/pods/init-containers/](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)。
        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23357/155558095013606_zh-CN.png)

    2.  配置**环境变量**。 

        支持通过键值对的形式为 Pod 配置环境变量。用于给 Pod 添加环境标志或传递配置等，具体请参见 [Pod variable](https://kubernetes.io/docs/tasks/inject-data-application/environment-variable-expose-pod-information/?spm=0.0.0.0.8VJbrE)。

    3.  配置**健康检查**。 

        支持存活检查（liveness）和就绪检查（Readiness）。存活检查用于检测何时重启容器；就绪检查确定容器是否已经就绪，且可以接受流量。关于健康检查的更多信息，请参见[https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes)。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/155558095110977_zh-CN.png)

        |请求类型|配置说明|
        |----|----|
        |HTTP请求|即向容器发送一个HTTPget 请求，支持的参数包括：        -   协议：HTTP/HTTPS
        -   路径：访问HTTP server 的路径
        -   端口：容器暴露的访问端口或端口名，端口号必须介于1~65535。
        -   HTTP头：即HTTPHeaders，HTTP请求中自定义的请求头，HTTP允许重复的header。支持键值对的配置方式。
        -   延迟探测时间（秒）：即**initialDelaySeconds**，容器启动后第一次执行探测时需要等待多少秒，默认为3秒。
        -   执行探测频率（秒）：即**periodSeconds**，指执行探测的时间间隔，默认为10s，最低为1s。
        -   超时时间（秒）：即**timeoutSeconds**，探测超时时间。默认1秒，最小1秒。
        -   健康阈值：探测失败后，最少连续探测成功多少次才被认定为成功。默认是1，最小值是1。对于存活检查（liveness）必须是1。
        -   不健康阈值：探测成功后，最少连续探测失败多少次才被认定为失败。默认是3。最小值是1。
|
        |TCP连接|即向容器发送一个TCP Socket，kubelet将尝试在指定端口上打开容器的套接字。 如果可以建立连接，容器被认为是健康的，如果不能就认为是失败的。支持的参数包括：        -   端口：容器暴露的访问端口或端口名，端口号必须介于1~65535。
        -   延迟探测时间（秒）：即**initialDelaySeconds**，容器启动后第一次执行探测时需要等待多少秒，默认为15秒。
        -   执行探测频率（秒）：即**periodSeconds**，指执行探测的时间间隔，默认为10s，最低为1s。
        -   超时时间（秒）：即**timeoutSeconds**，探测超时时间。默认1秒，最小1秒。
        -   健康阈值：探测失败后，最少连续探测成功多少次才被认定为成功。默认是1，最小值是1。对于存活检查（liveness）必须是1。
        -   不健康阈值：探测成功后，最少连续探测失败多少次才被认定为失败。默认是3。最小值是1。
|
        |命令行|通过在容器中执行探针检测命令，来检测容器的健康情况。支持的参数包括：        -   命令行：用于检测容器健康情况的探测命令。
        -   延迟探测时间（秒）：即**initialDelaySeconds**，容器启动后第一次执行探测时需要等待多少秒，默认为5秒。
        -   执行探测频率（秒）：即**periodSeconds**，指执行探测的时间间隔，默认为10s，最低为1s。
        -   超时时间（秒）：即**timeoutSeconds**，探测超时时间。默认1秒，最小1秒。
        -   健康阈值：探测失败后，最少连续探测成功多少次才被认定为成功。默认是1，最小值是1。对于存活检查（liveness）必须是1。
        -   不健康阈值：探测成功后，最少连续探测失败多少次才被认定为失败。默认是3。最小值是1。
 |

    4.  配置生命周期。 

        您可以为容器的生命周期配置容器启动项、启动执行、启动后处理和停止前处理。具体参见[https://kubernetes.io/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/](https://kubernetes.io/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/)。

        -   **容器启动项**：勾选 stdin 表示为该容器开启标准输入；勾选 tty 表示为该容器分配一个虚拟终端，以便于向容器发送信号。通常这两个选项是一起使用的，表示将终端 \(tty\) 绑定到容器的标准输入（stdin）上，比如一个交互式的程序从用户获取标准输入，并显示到终端中。
        -   **启动执行**：为容器设置预启动命令和参数。
        -   **启动后处理**：为容器设置启动后的命令。
        -   **停止前处理**：为容器设置预结束命令。
        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23357/155558095113638_zh-CN.png)

    5.  配置数据卷信息。 

        支持配置本地存储和云存储。

        -   **本地存储**：支持主机目录（hostpath）、配置项（configmap）、保密字典（secret）和临时目录，将对应的挂载源挂载到容器路径中。更多信息参见 [volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE)。
        -   **云存储**：支持云盘/NAS/OSS三种云存储类型。
    6.  配置**日志服务**，您可进行采集配置和自定义Tag设置。 

        **说明：** 请确保已部署Kubernetes集群，并且在此集群上已安装日志插件。

        您可对日志进行采集配置：

        -   **日志库**：即在日志服务中生成一个对应的logstore，用于存储采集到的日志。
        -   **容器内日志路径**：支持stdout和文本日志。
            -   **stdout**： stdout 表示采集容器的标准输出日志。
            -   **文本日志**：您可收集容器内指定路径的文本日志，同时支持通配符的方式。
        您还可设置自定义 tag，设置tag后，会将该tag一起采集到容器的日志输出中。自定义 tag 可帮助您给容器日志打上tag，方便进行日志统计和过滤等分析操作。

5.  完成容器配置后，单击 **下一步**。 
6.  进行高级设置。 

    您可进行**任务配置**。

    |参数|说明|
    |--|--|
    |**成功运行的Pod数**|即completions，指定job需要成功运行Pods的次数。默认值为1|
    |**并行运行的Pod数**|即parallelism，指定job在任一时刻应该并发运行Pod的数量。默认值为1|
    |**超时时间**|即activeDeadlineSeconds，指定job可运行的时间期限，超过时间还未结束，系统将会尝试进行终止。|
    |**重试次数**|即backoffLimit，指定job失败后进行重试的次数。默认是6次，每次失败后重试会有延迟时间，该时间是指数级增长，最长时间是6min。|
    |**重启策略**|仅支持不重启（Never）和失败时（OnFailure）|

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23357/155558095113639_zh-CN.png)

7.  最后单击**创建**。 
8.  创建成功后，默认进入创建完成页面，会列出应用包含的对象。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23357/155558095113640_zh-CN.png)

    您可以单击**查看应用详情**，进入任务详情页面。

    创建过程中，您可在**状态**栏中查看容器组的创建情况。本例中按照任务定义，一次性并行创建2个Pod。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23357/155558095113641_zh-CN.png)

    等待一段时间，所有容器组创建完毕。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23357/155558095113642_zh-CN.png)

9.  单击左上角**返回列表**，进入任务列表页面中，您可看到，该任务已显示完成时间。 

    **说明：** 若任务未创建完毕所有容器组，任务不会显示完成时间。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23357/155558095313643_zh-CN.png)


