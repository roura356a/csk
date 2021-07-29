---
keyword: [创建Notebook, 使用Notebook]
---

# 创建并使用Notebook

数据科学家使用云原生AI控制台进行机器学习（例如：数据检查、数据预处理、模型搭建、模型分析、模型检验和模型发布等）。Notebook是一个机器学习流程的开发标准，阿里云容器服务团队将Notebook作为您在AI控制台中的操作界面与集成开发环境，并给予您相关的授权机制。本文介绍如何在开发控制台创建和使用Notebook。

-   [创建ACK Pro版集群](/intl.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建ACK Pro版集群.md)。
-   ACK Pro版集群已安装云原生AI套件的**开发控制台**和**调度组件**，且集群Kubernetes版本不低于1.18。
-   集群管理员已为开发者创建了RAM子账号（即RAM用户），并分配和关联配额组。具体操作，请参见[创建RAM用户](/intl.zh-CN/用户管理/基本操作/创建RAM用户.md)和[步骤一：为目标用户分配和关联配额组](/intl.zh-CN/云原生AI用户指南/AI控制台使用指南/开发/访问AI开发控制台.md)。
-   配置训练数据，具体操作，请参见[配置训练数据和代码](/intl.zh-CN/云原生AI用户指南/AI控制台使用指南/开发/配置训练数据和代码.md)。
-   创建存储卷（PVC），具体操作，请参见[通过控制台的方式使用NAS静态存储卷](/intl.zh-CN/Kubernetes集群用户指南/存储-CSI/NAS存储卷/使用NAS静态存储卷.md)或[通过控制台的方式使用OSS静态存储卷](/intl.zh-CN/Kubernetes集群用户指南/存储-CSI/OSS存储卷/使用OSS静态存储卷.md)。

    **说明：** 模型训练数据一般被存放在OSS、NAS等存储卷上。


## Notebook功能介绍

-   提供一个云平台集成的机器学习实验环境，该环境可以作为机器学习中的本地开发角色。
-   提供一个拷贝或者处理数据集的工具，将相关的数据集处理完直接提交至持久化存储，可以结合大数据工具进行数据集的处理。
-   提供机器学习的测试、预处理等工作的环境，在该环境您可以编写机器学习主要代码，且将其打包成`docker Image`推送至集群本地或者远程的Docker仓库。

## 步骤一：创建Notebook

1.  登录开发控制台。具体操作，请参见[访问AI开发控制台](/intl.zh-CN/云原生AI用户指南/AI控制台使用指南/开发/访问AI开发控制台.md)。

2.  在开发控制台左侧导航栏中，单击**Notebook**。

3.  在**Notebook**页面，单击**创建Notebook**。

4.  在**Notebook基本信息**和**Notebook资源配置**区域，配置相关信息后，单击**创建Notebook**。

    ![2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5787257261/p298739.png)

    1.  输入**Notebook名称**和**命名空间**，选择**Notebook镜像**和**数据配置**，其中命名空间只能提交到集群管理员为开发分配的命名空间中，其他选项可根据需要配置。

    2.  打开**Workspace PVC**开关，在**Workspace持久化的目标PVC**下拉列表，选择Workspace挂载的PVC。

    3.  在**Notebook资源配置**区域，输入**CPU（核数）**和**GPU卡数**，选择**内存（GPU）**。

5.  任务创建完成后，您可以在**Notebook**页面，单击刚创建的Notebook的名称，即能查看Notebook的运行状况等信息。


## 步骤二：使用Notebook

1.  在**Notebook**页面，单击任意一个**状态**为**Running**的Notebook。

2.  单击**Notebook**区域下的**Python3**。

    **说明：** 在创建Notebook页面，阿里云容器服务团队提供的官方镜像只支持Python语言。如果您有其他需求，可以根据制作Notebook自定义镜像的说明内容进行自定义镜像Dockerfile的制作与镜像的打包操作，具体操作，请参见[制作Notebook自定义镜像说明](#section_pvq_304_w7w)。

    ![23](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2610647261/p298900.png)

3.  在Notebook中使用Arena进行任务提交。

    方式一：在命令行工具使用Arena进行任务提交

    ![24](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2610647261/p298902.png)

    方式二：在Python的SDK中使用Arena进行任务提交

    ![25](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1610647261/p298903.png)


## 制作Notebook自定义镜像说明

为了满足不同的用户的不同需求，在创建Notebook页面为您提供了自定义Notebook镜像的入口，如下图所示：

![28](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6787257261/p299392.png)

您可以基于以下流程进行自定义镜像Dockerfile的制作与镜像的打包操作：

```
FROM tensorflow/tensorflow:1.15.5-gpu
USER root
RUN pip install jupyter && \
    pip install ipywidgets && \
    jupyter nbextension enable --py widgetsnbextension && \
    pip install jupyterlab && jupyter serverextension enable --py jupyterlab
# 您可以通过其他途径安装JupyterLab，但是必须暴露服务到8888端口
EXPOSE 8888
USER jovyan
CMD ["sh", "-c", "jupyter-lab --notebook-dir=/home/jovyan --ip=0.0.0.0 --no-browser --allow-root --port=8888 --ServerApp.token='' --ServerApp.password='' --ServerApp.allow_origin='*' --ServerApp.base_url=${NB_PREFIX} --ServerApp.authenticate_prometheus=False"]
USER root
```

参数的相关解释如下表所示：

|参数|描述|
|--|--|
|--notebook-dir|表示启动的工作目录，默认为JupyterLab官方标准值：`/home/jovyan`。|
|--ip|表示Jupyter服务监听的IP地址，默认值为`0.0.0.0`，即外部IP可访问。|
|--no-browser|表示无浏览器环境（仅支持linux server环境）。|
|--port|表示向外监听的端口，JupyterLab官方标准值：`8888`（固定必填）。|
|--ServerApp.token|表示自定义的登录`token`，默认值为空字符串。如果您自定义了该值，则需要告知使用者。|
|--ServerApp.password|表示自定义的登录密码，默认为空字符串。如果您自定义了该值，则需要告知使用者。|
|--ServerApp.base\_url|表示JupyterLab服务启动路径，因为需要根据该路径进行路由，所以固定从环境变量`NB_PREFIX`中获取，默认值为`${NB_PREFIX}`（固定必填）。|
|--ServerApp.allow\_origin|表示允许JupyterLab访问的源IP组，默认设置为`'*'`，表示允许JupyterLab访问所有的源IP组。|
|--ServerApp.authenticate\_prometheus|Jupyter的验证监控，该参数默认是不开启的，即设置为`False`。您也可以根据具体需求场景按需设置。|

