---
keyword: [容器日志, 日志采集, 日志服务]
---

# 通过日志服务采集Kubernetes容器日志

阿里云容器服务Kubernetes集群集成了日志服务，您可在创建集群时启用日志服务，快速采集Kubernetes集群的容器日志，包括容器的标准输出以及容器内的文本文件。

## 步骤一：启用日志服务组件Logtail

您可以在创建集群时选中**使用日志服务**，启用Logtial组件；也可以为已有集群启用Logtail组件。

**创建集群时启用Logtail**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击页面右上角的**创建集群**。

    以下仅介绍开启日志服务的关键步骤。关于创建集群的具体操作，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)。

4.  在**组件配置**配置项页中，选中**使用日志服务**，表示在新建的Kubernetes集群中安装日志插件。

    当选中**使用日志服务**后，会出现创建项目（Project）的提示。关于日志服务管理日志的组织结构，请参见[项目](/cn.zh-CN/产品简介/基本概念/项目.md)）。有以下两种创建Project方式。

    -   选择一个现有的Project来管理采集的日志。

        ![使用已有Project](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8885659951/p148932.png)

    -   自动创建一个新的Project来管理采集的日志，Project会自动命名为`k8s-log-{ClusterID}`，ClusterID是您新建的Kubernetes集群的唯一标识。

        ![创建新Project](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8885659951/p148933.png)

5.  配置完成后，单击右下角**创建集群**，在弹出的窗口中单击**确定**，完成创建。

    完成创建后，您可在集群列表页面看到开启了Logtail的ACK集群。


**为已有集群启用Logtail**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**运维管理** \> **组件管理**，并在**日志与监控**区域找到**logtail-ds**。

5.  在**logtail-ds**组件右侧，单击**安装**。

6.  在**安装组件**对话框中单击**确认**。


如果您已安装旧版本的日志服务组件，可以在**logtail-ds**组件右侧，单击**升级**。

## 步骤二：创建应用时配置日志服务

您可以在创建应用的同时配置日志服务，从而对容器的日志进行采集。目前支持控制台向导和YAML模板两种方式创建应用。

**通过控制台向导创建应用并配置日志服务**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

5.  在无状态页面上方的**命名空间**下拉框中设置命名空间，然后单击页面右上角的**使用镜像创建**。

6.  在**应用基本信息**页签，设置**应用名称**、**副本数量**和**类型**，单击**下一步**，进入容器配置页面。

    以下仅介绍日志服务相关的配置，其他的应用配置，可参见[使用镜像创建无状态Deployment应用](/cn.zh-CN/Kubernetes集群用户指南/应用管理/使用镜像创建无状态Deployment应用.md)。

7.  进行**日志配置**。单击**+**图标创建新的采集配置，每个采集配置由**Logstore名称**和**日志采集路径**两项构成。

    -   Logstore名称：您可以使用它来指定所采集日志存储于该Logstore，如果该Logstore不存在的话，我们会自动为您在集群关联的日志服务Project下创建相应的Logstore。

        **说明：** 名称中不能包含下划线（\_），可以使用-来代替。

    -   日志采集路径：您可以用它来指定希望采集的日志所在的路径，如使用/usr/local/tomcat/logs/catalina.\*.log来采集tomcat的文本日志。

        **说明：** 如果指定为stdout则表示采集容器的标准输出和标准错误输出。

        每一项采集配置都会被自动创建为对应Logstore的一个采集配置，默认采用极简模式（按行）进行采集，如果您需要更丰富的采集方式，可以前往[日志服务控制台](https://sls.console.aliyun.com/lognext/profile)，进入相应的Project（默认是k8s-log前缀）和Logstore对配置进行修改。

    ![采集配置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9885659951/p148949.png)

8.  设置**自定义Tag**。

    单击**+**图标创建新的自定义Tag，每一个自定义Tag都是一个键值对，会拼接到所采集到的日志中，您可以使用它来为容器的日志数据进行标记，例如版本号。

    ![自定义tag](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9885659951/p9473.png)

9.  当完成所有配置后，可单击右上角的**下一步**进入后续流程。后续操作，可参见[使用镜像创建无状态Deployment应用](/cn.zh-CN/Kubernetes集群用户指南/应用管理/使用镜像创建无状态Deployment应用.md)。


**通过YAML模板创建**

1.  在集群管理页左侧导航栏中，单击**工作负载**。

2.  在无状态页面上方的**命名空间**下拉框中设置命名空间，然后单击页面右上角的**使用模板创建**。

3.  YAML模板的语法同Kubernetes语法，但是为了给容器指定采集配置，需要使用`env`来为容器增加**采集配置**和**自定义Tag**，并根据采集配置，创建对应的volumeMounts和volumes。以下是一个简单的Pod示例：

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: my-demo
    spec:
      containers:
      - name: my-demo-app
        image: 'registry.cn-hangzhou.aliyuncs.com/log-service/docker-log-test:latest'
        env:
        ######### 配置 环境变量 ###########
        - name: aliyun_logs_log-stdout
          value: stdout
        - name: aliyun_logs_log-varlog
          value: /var/log/*.log
        - name: aliyun_logs_mytag1_tags
          value: tag1=v1
        ###############################
        ######### 配置volume mount ###########
        volumeMounts:
        - name: volumn-sls-mydemo
          mountPath: /var/log
      volumes:
      - name: volumn-sls-mydemo
        emptyDir: {}
      ###############################
    ```

    根据您的需求按照以下顺序进行配置。

    **说明：** 如果您有更多的日志采集需求，请参见[步骤三：配置环境变量的高级参数](#section_q8z_yc4_dvn)。

    1.  通过环境变量来创建您的**采集配置**和**自定义Tag**，所有与配置相关的环境变量都采用`aliyun_logs_`作为前缀。
        -   创建采集配置的规则如下：

            ```
            - name: aliyun_logs_{Logstore名称}
              value: {日志采集路径}                            
            ```

            示例中创建了两个采集配置，其中`aliyun_logs_log-stdout`这个env表示创建一个Logstore名字为log-stdout，日志采集路径为stdout的配置，从而将容器的标准输出采集到log-stdout这个Logstore中。

            **说明：** Logstore名称中不能包含下划线（\_），可以使用短划线（-）来代替。

        -   **创建自定义Tag**的规则如下：

            ```
            - name: aliyun_logs_{任意不包含'_'的名称}_tags
              value: {Tag名}={Tag值}                            
            ```

            配置Tag后，当采集到该容器的日志时，会自动附加对应的字段到日志服务。

    2.  如果您的采集配置中指定了非stdout的采集路径，需要在此部分创建相应的volumnMounts。

        示例中采集配置添加了对/var/log/\*.log的采集，因此相应地添加了/var/log的volumeMounts。

4.  当YAML编写完成后，单击**创建**，即可将相应的配置交由Kubernetes集群执行。


## 步骤三：配置环境变量的高级参数

通过容器环境变量配置采集支持多种配置参数，您可根据实际需求设置高级参数来实现日志采集的特殊需求。

**说明：** 通过容器环境变量配置采集日志的方式不适用边缘场景。

|字段|说明|示例|注意事项|
|--|--|--|----|
|aliyun\_logs\_\{key\}|-   必选项。\{key\}只能包含小写字母、数字和-。
-   若不存在aliyun\_logs\_\{key\}\_logstore，则默认创建并采集到名为\{key\}的logstore。
-   当值为stdout表示采集容器的标准输出；其他值为容器内的日志路径。

|-   ```
- name: aliyun_logs_catalina

   stdout
```

-   ```
- name: aliyun_logs_access-log

   /var/log/nginx/access.log
```


|-   默认采集方式为极简模式，如需解析日志内容，建议使用日志服务控制台，并参见[通过DaemonSet-控制台方式采集Kubernetes文件](/cn.zh-CN/数据采集/Logtail采集/采集容器日志/通过DaemonSet-控制台方式采集Kubernetes文件.md)、[通过DaemonSet-控制台方式采集Kubernetes标准输出](/cn.zh-CN/数据采集/Logtail采集/采集容器日志/通过DaemonSet-控制台方式采集Kubernetes标准输出.md)或[通过DaemonSet-CRD方式采集日志](/cn.zh-CN/数据采集/Logtail采集/采集容器日志/通过DaemonSet-CRD方式采集日志.md)进行配置。
-   \{key\}需保持在K8s集群内唯一。 |
|aliyun\_logs\_\{key\}\_tags|可选。值为\{tag-key\}=\{tag-value\}类型，用于对日志进行标识。|```
- name: aliyun_logs_catalina_tags

   app=catalina
```

|-|
|aliyun\_logs\_\{key\}\_project|可选。值为指定的日志服务Project。当不存在该环境变量时为您安装时所选的Project。|```
- name: aliyun_logs_catalina_project

   my-k8s-project
```

|Project需与您的Logtail工作所在Region一致。|
|aliyun\_logs\_\{key\}\_logstore|可选。值为指定的日志服务Logstore。当不存在该环境变量时Logstore和\{key\}一致。|```
- name: aliyun_logs_catalina_tags

   my-logstore
```

|-|
|aliyun\_logs\_\{key\}\_shard|可选。值为创建Logstore时的shard数，有效值为1~10。当不存在该环境变量时值为2。|```
- name: aliyun_logs_catalina_shard

   4
```

|-|
|aliyun\_logs\_\{key\}\_ttl|可选。值为指定的日志保存时间，有效值为1~3650。 -   当取值为3650时，指定日志的保存时间为永久保存。
-   当不存在该环境变量时，默认指定日志的保存时间为90天。

|```
- name: aliyun_logs_catalina_ttl

   3650
```

|-|
|aliyun\_logs\_\{key\}\_machinegroup|可选。值为应用的机器组。当不存在该环境变量时与安装Logtail的默认机器组一致。|```
- name: aliyun_logs_catalina_machinegroup

   my-machine-group
```

|-|

-   **定制需求1：将多个应用数据采集到同一Logstore**

    如果您需要将多个应用数据采集到同一Logstore，可以设置aliyun\_logs\_\{key\}\_logstore参数，例如以下配置将2个应用的stdout采集到stdout-logstore中：

    应用1设置的环境变量为：

    ```
    ######### 配置 环境变量 ###########
        - name: aliyun_logs_app1-stdout
          value: stdout
        - name: aliyun_logs_app1-stdout_logstore
          value: stdout-logstore
    ```

    应用2设置的环境变量为：

    ```
    ######### 配置 环境变量 ###########
        - name: aliyun_logs_app2-stdout
          value: stdout
        - name: aliyun_logs_app2-stdout_logstore
          value: stdout-logstore
    ```

-   **定制需求2：将不同应用数据采集到不同的Project**

    如果您需要将不同应用的数据采集到多个Project中，您需要进行以下操作：

    1.  在每个Project中创建一个机器组，选择自定义标识，标识名为k8s-group-\{cluster-id\}，其中\{cluster-id\}为您的集群ID，机器组名称您可以自定义配置。
    2.  每个应用的环境变量中配置project、logstore、machinegroup信息，其中机器组名称为您在步骤1中创建的机器组名，例如：

        ```
        ######### 配置 环境变量 ###########
            - name: aliyun_logs_app1-stdout
              value: stdout
            - name: aliyun_logs_app1-stdout_project
              value: app1-project
            - name: aliyun_logs_app1-stdout_logstore
              value: app1-logstore
            - name: aliyun_logs_app1-stdout_machinegroup
              value: app1-machine-group
        ```


## 步骤四：查看日志

本例中查看通过控制台向导创建的tomcat应用的日志。完成配置后，tomcat应用的日志已被采集并存储到日志服务中，您可以在日志服务控制台查看容器日志。操作步骤如下：

1.  安装成功后，进入[日志服务控制台](https://sls.console.aliyun.com)。

2.  在进入控制台后，在**Project列表**区域选择Kubernetes集群对应的Project（默认为k8s-log-\{Kubernetes集群ID\}），进入**日志库**列表页签。

3.  在列表中找到相应的Logstore（采集配置中指定），将鼠标悬浮在相应的Logstore名称的右侧，单击![button](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9885659951/p88516.png)图标，并单击**查询分析**。

4.  本例中，在日志查询页面，您可查看tomcat应用的标准输出日志和容器内文本日志，并可发现自定义tag附加到日志字段中。

    ![日志字段](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9885659951/p9541.png)


在为应用接入日志服务后，您也可以在ACK控制台查看容器的日志。操作如下：

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**运维管理** \> **日志中心**。

5.  在**日志中心**页面，单击**应用日志**页签，设置目标应用的类型、所在命名空间、名称后即可查看应用容器的相关日志。


## 更多信息

1.  默认情况下，日志服务会使用极简模式来采集您的数据（按行采集、不解析）。如果您需要更复杂的配置，可以参见以下日志服务文档并前往日志服务控制台进行配置修改。
    -   [通过DaemonSet-控制台方式采集Kubernetes文件](/cn.zh-CN/数据采集/Logtail采集/采集容器日志/通过DaemonSet-控制台方式采集Kubernetes文件.md)
    -   [通过DaemonSet-控制台方式采集Kubernetes标准输出](/cn.zh-CN/数据采集/Logtail采集/采集容器日志/通过DaemonSet-控制台方式采集Kubernetes标准输出.md)
2.  除了通过控制台配置采集以外，您还可以直接通过CRD配置来对Kubernetes集群进行日志采集，具体可参见[通过DaemonSet-CRD方式采集日志](/cn.zh-CN/数据采集/Logtail采集/采集容器日志/通过DaemonSet-CRD方式采集日志.md)。
3.  关于如何进行异常排查，请参见[Logtail排查简介]()。

