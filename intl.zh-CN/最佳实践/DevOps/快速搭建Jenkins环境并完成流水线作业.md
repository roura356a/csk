---
keyword: [搭建Jenkins环境, 流水线作业]
---

# 快速搭建Jenkins环境并完成流水线作业

本文主要演示如何在阿里云Kubernetes集群上快速搭建Jenkins持续集成环境，并基于提供的示例快速完成应用源码编译、应用镜像构建和推送以及流水线的应用部署。

-   已创建Kubernetes集群。具体操作，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   已通过kubectl连接到Kubernetes集群。具体操作，请参见[通过kubectl管理Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl管理Kubernetes集群.md)。

## 部署Jenkins

建议先按照以下步骤安装部署ack-jenkins应用，成功运行构建任务示例**demo-pipeline**， 再依照此构建任务示例改造您的构建任务配置。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  在**阿里云应用**页签中，单击**ack-jenkins**。

4.  在应用目录 - ack-jenkins页面配置相关参数。

    1.  单击**参数**页签。

    2.  选择Kubernetes**集群**以及**命名空间**，填写**发布名称**。

        ![命名空间](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6594413261/p38449.png)

        **说明：** 建议选择自定义命名空间或**default**命名空间。本示例中选择自定义命名空间**ci**。

    3.  修改`AdminPassword`字段，并单击**创建**。

        如果您未设置登录密码，即未修改`AdminPassword`字段，Jenkins部署成功后系统会自动生成密码，默认密码为`admin`。

5.  访问并登录Jenkins。

    1.  在控制台左侧导航栏中，单击**集群**。

    2.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

    3.  在集群管理页左侧导航栏中，选择**网络** \> **服务**。

    4.  选择上个步骤设置的命名空间，然后单击**ack-jenkins-default**服务的**外部端点**，登录并访问Jenkins系统。

6.  配置Jenkins的新手入门。

    1.  在新手入门配置页面，单击**安装推荐的插件**。

    2.  插件安装完成后，在新手入门的**实例配置**页面，单击**保存并完成**。

    3.  实例配置保存完成后，单击**开始使用Jenkins**。

7.  构建**demo-pipeline**并访问应用服务。

    1.  在Jenkins首页，单击**demo-pipeline**的![构建](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8913491261/p276903.png)图标。

        ![demo-pipeline](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8913491261/p38467.png)

    2.  根据您的镜像仓库信息修改构建参数。本示例中源码仓库分支为`master`，镜像为`registry.cn-beijing.aliyuncs.com/ack-cicd/ack-jenkins-demo:latest`。

        ![构建参数](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9913491261/p38468.png)

    3.  单击**开始构建**。

        测试Kubernetes集群动态分配的Jenkins Slave Pod与Jenkins Master是否连接正常。

        执行构建后，Jenkins从Kubernetes集群动态创建一个Slave Pod运行本次构建任务。关于示例应用代码，请参见[jenkins-demo-GitHub](https://github.com/AliyunContainerService/jenkins-demo)或[jenkins-demo-haoshuwei](https://code.aliyun.com/haoshuwei/jenkins-demo.git)。

    4.  查看**状态**，若构建成功则表示Jenkins on Kubernetes运行正常。

        ![Build History](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9913491261/p38469.png)


## Kubernetes Cloud的配置说明

Jenkins使用Kubernetes Plugin连接Kubernetes集群并动态生成和释放Slave Pod。

1.  在左侧导航栏选择**返回工作台**。
2.  在Jenkins系统左侧导航栏选择**系统管理**。
3.  在管理Jenkins页面的**系统配置**下单击**节点管理**。
4.  在节点列表页面左侧导航栏选择**Configure Clouds**。
5.  在配置集群页面单击**Kubernetes Cloud details...**。

    在部署ack-jenkins时已自动化配置Kubernetes Cloud，以下为各个参数的具体说明：

    |参数|说明|
    |--|--|
    |名称|默认为`kubernetes`。![构建pod](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7347413261/p38460.png) |
    |Kubernetes地址|默认为`https://kubernetes.default.svc.cluster.local:443`，Jenkins系统安装在当前集群中，可以使用内部服务端点访问集群的API Server。|
    |Kubernetes命名空间|默认为`default`，动态Slave Pod会在命名空间`default`下生成和销毁。|
    |Jenkins地址|默认为`http://ack-jenkins-default:8080`，Slave Pod连接Jenkins Master使用的服务端点。|
    |Jenkins通道|默认为`ack-jenkins-default-agent:50000`，Slave Pod使用Jnlp连接Jenkins Master。|

6.  在配置集群页面单击**Pod Templates...**，再单击**Pod Templates details...**。

    在部署ack-jenkins时已自动化配置Pod Templates，以下为各个参数的具体说明：

    |参数|说明|
    |--|--|
    |名称|默认为`slave-pipeline`，Slave Pod的名称。![Container jnlp](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4055414161/p38461.png) |
    |命名空间|默认为`jenkins`。|
    |标签列表|默认为`slave-pipeline`，Jenkins构建任务通过此标签选择模板生成Slave Pod。|
    |容器列表|`jnlp`用于连接Jenkins Master。|
    |`kaniko`用于构建和推送容器镜像。![Container kaniko](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0663659951/p38462.png) |
    |`maven`用于构建和打包应用。![Container maven](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0663659951/p38465.png) |
    |`kubectl`用于部署应用。![Container kubectl](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0663659951/p38463.png) |


## 为Slave Pod配置maven缓存

由于Slave Pod是在Kubernetes集群中动态生成的，可能运行于集群的任何一个Worker节点，所以要保证每次动态生成Slave Pod时都能使用到**maven**缓存，就必须使用共享存储持久化存储卷。

1.  创建NAS共享存储持久化存储卷。具体操作，请参见[使用NAS动态存储卷](/intl.zh-CN/Kubernetes集群用户指南/存储-CSI/NAS存储卷/使用NAS动态存储卷.md)。

    执行以下命令查看在**jenkins**命名空间下创建的NAS持久化存储卷。

    ```
    kubectl -n jenkins get pvc
    ```

    预期输出：

    从预期输出可得：存储卷`ack-jenkins-default`是Jenkins Master的/var/jenkins\_home目录的持久化存储，`nas-csi-pvc`则是为配置maven缓存所共享的NAS持久化存储卷。

    ```
    NAME                  STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS               AGE
    ack-jenkins-default   Bound    d-2ze8xhgzuw1t278j****                     20Gi       RWO            alicloud-disk-efficiency   3h16m
    nas-csi-pvc           Bound    nas-c6c3e703-58a9-484e-8ce5-630486ea****   20Gi       RWX            alicloud-nas-subpath       4m17s
    ```

2.  在Jenkins系统的Pod Template中添加Config Map Volume类型挂载。

    若需要为Jenkins Slave Pod挂载自定义Settings文件，则可以先创建Config Map Volume，再配置到Pod Template上。

    1.  执行以下命令创建自定义Settings文件。

        ```
        kubectl -n jenkins create configmap maven-config --from-file=settings.xml 
        ```

    2.  在Jenkins系统左侧导航栏选择**系统管理**，在管理Jenkins页面的**系统配置**下单击**节点管理**，在节点列表页面左侧导航栏选择**Configure Clouds**。

    3.  在配置集群页面单击**Pod Template**，再单击**Pod Template details**。

    4.  在**卷**下单击**添加卷**，选择**Config Map Volume**类型卷。

        ![添加卷](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7594413261/p281884.png)

    5.  单击**Save**，保存配置。

3.  在Jenkins系统的Pod Template中添加Persistent Volume Claim类型挂载卷。具体操作，请参见[步骤2](#step_ym0_8j1_oj4)。

    ![添加卷PVC](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7594413261/p281899.png)

    **结果验证**

    1.  在Jenkins首页，单击**demo-pipeline**名称。
    2.  在Jenkins系统控制台的左侧导航栏选择**配置**。
    3.  单击流水线页签。
    4.  在**脚本路径**输入Jekinsfile.maven。
    5.  单击**保存**。
    6.  在Jenkins首页，单击**demo-pipeline**的![构建](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8913491261/p276903.png)图标，单击**开始构建**。

        首次进行**maven**构建时，需要花费一段时间下载所有依赖包。

        ![首次构建](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2193542261/p278605.png)

        再次进行**maven**构建，则会引用缓存的依赖包，快速完成源码打包。

        ![再次构建](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2193542261/p278624.png)


## 使用kaniko构建和推送容器镜像

使用**kaniko**推送镜像时，需要设置镜像仓库的访问权限。

在本示例中，需要在Linux环境下（请注意不要在MacOS下）生成访问镜像仓库的config.json文件。例如，需要构建和推送一个镜像`registry.cn-hangzhou.aliyuncs.com/haoshuwei24/jenkins-demo:20200428`。

1.  执行以下命令登录镜像仓库。

    登录镜像仓库的同时会生成config.json文件。

    ```
    docker login -u <username> -p <password> registry.cn-hangzhou.aliyuncs.com
    ```

    预期输出：

    ```
    Login Succeeded
    ```

2.  在**jenkins**命名空间下使用生成的config.json文件创建名为jenkins-docker-cfg的Secret。

    ```
    kubectl create secret generic jenkins-docker-cfg -n jenkins --from-file=/root/.docker/config.json
    ```

3.  在Jenkins系统的Pod Template中配置挂载卷及环境变量。

    1.  在Jenkins系统左侧导航栏选择**系统管理**，在管理Jenkins页面的**系统配置**下单击**节点管理**，在节点列表页面左侧导航栏选择**Configure Clouds**。

    2.  在配置集群页面单击**Pod Template**，再单击**Pod Template details**。

    3.  在**环境变量**下单击**添加环境变量**，选择**Environment Variable**类型卷。

        ![环境变量及Secret卷](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0210503261/p280887.png)

    4.  在**卷**下单击**添加卷**，选择**Secret Volume**类型卷。

    5.  单击**Save**，保存配置。

    **结果验证**

    1.  在Jenkins首页，单击**demo-pipeline**名称。
    2.  在Jenkins系统控制台的左侧导航栏选择**配置**。
    3.  单击流水线页签。
    4.  在**脚本路径**输入Jekinsfile.kaniko。
    5.  单击**保存**。
    6.  在Jenkins首页，单击**demo-pipeline**的![构建](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8913491261/p276903.png)图标，请将构建参数修改为您实际的镜像仓库相关信息，然后单击**开始构建**。

        查看**kaniko**构建日志。

        ![kaniko构建日志](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0210503261/p280900.png)

    7.  查看容器镜像仓库是否已经推送成功。
        1.  登录[容器镜像服务控制台](https://cr.console.aliyun.com)。
        2.  在左侧导航栏，选择**实例列表**。
        3.  在个人版实例管理页面选择**仓库管理** \> **镜像仓库**。
        4.  在**镜像仓库**页面，单击目标仓库右侧操作列的**管理**。
        5.  在左侧导航栏，选择**镜像版本**，查看镜像是否已推送成功。

            ![镜像版本查看](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0210503261/p280924.png)


## 升级Jenkins

若您的Jenkins环境需要更换jenkins-master镜像，由于新版本Jenkins Master的自动化脚本会向/var/jenkins\_home路径写数据，为了防止覆盖已有数据，请先为/var/jenkins\_home目录对应的云盘存储卷做快照备份。具体操作，请参见[使用云盘存储快照](/intl.zh-CN/Kubernetes集群用户指南/存储-CSI/云盘存储卷/使用云盘存储快照.md)。

-   [源码仓库](https://github.com/AliyunContainerService/jenkins-demo.git)
-   [容器服务ACK](https://www.alibabacloud.com/product/kubernetes)
-   [kaniko](https://github.com/GoogleContainerTools/kaniko)

  


