# Kubernetes与日志服务集成与使用 {#concept_nsj_sxm_t2b .concept}

阿里云容器服务Kubernetees集群集成了日志服务（SLS），您可在创建集群时启用日志服务，随后只要对应用进行简单配置，即可无缝使用阿里云日志服务。

## 前提条件 {#section_of2_3ym_t2b .section}

请确保开通了阿里云[日志服务](https://www.aliyun.com/product/sls?spm=5176.8142029.search.1.e9396d3eHV0OlT)。

## 步骤1 安装日志组件 {#section_ewl_jym_t2b .section}

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏的**集群**，进入集群列表页面，单击右上角**创建Kubernetes集群**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17400/15368106189250_zh-CN.png)

3.  在创建集群页面，选择**Kubernetes**集群模式。勾选**使用SLS**，您可选择已有日志project；或者直接新建一个，默认创建名称为`k8s-log-{ClusterID}的project`。其他集群配置参数，请参见[创建Kubernetes集群](intl.zh-CN/用户指南/Kubernetes 集群/集群管理/创建Kubernetes集群.md#)。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17400/15368106189251_zh-CN.png)

4.  最后单击右上角**创建集群**，启动部署，等集群部署完成。
5.  单击左侧导航栏中**应用** \> **部署**，进入部署列表，选择所需的集群和kube-system命名空间，您可看到alibaba-log-controller组件为运行状态。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17400/15368106189449_zh-CN.png)

6.  进入日志服务管理控制台，您可看到日志project被创建出来。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17400/15368106189451_zh-CN.png)


## 步骤2 在部署页配置采集信息 {#section_rp2_nfl_52b .section}

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在Kubernetes菜单下，单击左侧导航栏中的**应用** \> **部署**，进入部署列表页面，单击右上角**使用镜像创建**。
3.  进入应用基本信息页面，配置应用基本信息，包括应用名称、部署集群和命名空间，最后单击**下一步**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17400/15368106189460_zh-CN.png)

4.  在应用配置页面，首先配置镜像名称、镜像版本、容器数量。本例中创建一个tomcat应用，来测试收集容器标准输出日志，以及容器内文本日志。

    **说明：** tomcat 镜像属于少数同时使用 stdout 和文件日志的容器镜像，适合本例中的演示。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17400/15368106189473_zh-CN.png)

5.  配置数据卷，将容器内的日志目录挂载到临时目录，用于保存文本日志。

    **说明：** 您也可使用其他存储保存容器文本日志，如云盘、NAS等云存储。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17400/15368106189474_zh-CN.png)

6.  配置日志服务，您可进行采集配置和自定义Tag设置。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17400/15368106189541_zh-CN.png)

    您可对日志进行采集配置：

    -   **日志库**：即在日志服务中生成一个对应的logstore，用于存储采集到的日志。
    -   **容器内日志路径**：支持stdout和文本日志。
        -   **stdout**： stdout 表示采集容器的标准输出日志。
        -   **文本日志**：表示收集容器内指定路径的日志，支持通配符的方式，本例中表示收集符合/usr/local/tomcat/logs/catalina.\*.log名称规则的文本日志。
    您还可设置自定义 tag，设置tag后，会将该tag一起采集到容器的日志输出中。自定义 tag 可帮助您给容器日志打上tag，方便进行日志统计和过滤等分析操作。

7.  完成应用配置后，单击**下一步**。
8.  进入访问设置页面，本例中不进行访问设置，单击**创建**，页面提示创建成功。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17400/15368106189564_zh-CN.png)

9.  进入日志服务控制台，在对应的日志project中，您可看到成功创建的logstore。选择所需的logstore，单击**查询**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17400/15368106189567_zh-CN.png)

10. 页面跳转到日志列表页，选择日志查询时间，每条日志都被打上了tag `app=tomcat`。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17400/15368106199575_zh-CN.png)


