---
keyword: [流水线构建, 搭建Jenkins环境]
---

# 在ASK上快速搭建Jenkins环境及执行流水线构建

本文主要演示如何在阿里云Serverless Kubernetes服务（ASK）上快速搭建Jenkins持续集成环境，并基于提供的应用示例快速完成应用源码编译、镜像构建和推送以及应用部署的流水线。

-   已创建ASK集群。具体操作，请参见[创建Serverless Kubernetes集群](/cn.zh-CN/Serverless Kubernetes集群用户指南/快速入门/创建Serverless Kubernetes集群.md)。
-   已通过kubectl连接Kubernetes集群。具体操作，请参见[通过kubectl连接Kubernetes集群](/cn.zh-CN/Serverless Kubernetes集群用户指南/集群/管理和访问集群/通过kubectl连接Kubernetes集群.md)。

## 部署Jenkins

1.  执行以下命令下载部署文件。

    ```
    git clone https://github.com/AliyunContainerService/jenkins-on-serverless.git
    cd jenkins-on-serverless
    ```

2.  完成`jenkins_home`持久化配置。

    Serverless Kubernetes目前不支持云盘，如需持久化`jenkins_home`，您可以挂载`nfs volume`，修改serverless-k8s-jenkins-deploy.yaml文件，取消以下字段注释并配置您的NFS信息：

    ```
    #volumeMounts:
            #  - mountPath: /var/jenkins_home
            #    name: jenkins-home
          .....
          #volumes:
          #  - name: jenkins-home
          #    nfs:
          #      path: /
          #      server:
    ```

3.  执行以下命令部署Jenkins。

    ```
    kubectl apply -f serverless-k8s-jenkins-deploy.yaml
    ```

4.  登录Jenkins。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在控制台左侧导航栏中，单击**集群**。

    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

    4.  在集群管理页左侧导航栏中，选择**网络** \> **服务**。

    5.  单击Jenkins服务的外部端点登录Jenkins。

        ![外部端点](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6748649951/p141997.png)

    6.  在Jenkins登录页面，输入用户名和密码。默认用户名和密码均为**admin**。

        **说明：** 为了保证Jenkins系统安全，请登录后修改密码。

5.  配置Jenkins的新手入门。

    1.  在新手入门配置页面，单击**安装推荐的插件**。

    2.  插件安装完成后，在新手入门的**实例配置**页面，单击**保存并完成**。

    3.  实例配置保存完成后，单击**开始使用Jenkins**。

6.  获取Token的Secret。

    1.  执行以下命令查看Token。

        ```
        kubectl get secret
        ```

        预期输出：

        ```
        NAME                         TYPE                                  DATA   AGE
        ack-jenkins-sa-token-q****   kubernetes.io/service-account-token   3      28m
        default-token-b****          kubernetes.io/service-account-token   3      27h
        ```

    2.  执行以下命令获取**ack-jenkins-sa-token-q\*\*\*\***的Secret。

        ```
        kubectl get secret ack-jenkins-sa-token-q**** -o jsonpath={.data.token} |base64 -d
        ```

        预期输出：

        ```
        sdgdrh****
        ```

7.  创建Secret Text类型凭证。

    1.  在Jenkins系统左侧导航栏，选择**系统管理**。

    2.  在管理Jenkins页面，**系统配置**下单击**节点管理**。

    3.  在节点列表页面左侧导航栏，选择**Configure Clouds**。

    4.  在配置集群页面，单击**Kubernetes Cloud details...**。

    5.  在**凭据**字段，选择**添加** \> **Jenkins**。

    6.  在Jenkins 凭据提供者: Jenkins对话框，添加Secret Text类型的凭证。

        凭证的参数说明如下所示：

        |参数|说明|
        |--|--|
        |Domain|表示凭据的域。默认为**全局凭据（unrestricted）**|
        |类型|表示凭据的类型。本示例选择**Secret Text**。|
        |范围|表示凭据的范围，可选择全局或系统。本示例选择**全局（Jenkins，nodes，items，all child items，etc）**。|
        |Secret|表示凭据的Secret。本示例输入上个步骤获取的Secret。|
        |ID|表示凭据的名称。本示例为**ask-jenkins-token**。|
        |描述|表示凭据的补充说明。|

    7.  单击**添加**。

8.  在配置集群页面配置相关参数。更多信息，请参见[Kubernetes Cloud的配置说明](/cn.zh-CN/最佳实践/DevOps/快速搭建Jenkins环境并完成流水线作业.md)。

    1.  配置**Kubernetes地址**，**凭据**选择为**ask-jenkins-token**，单击**连接测试**验证连接是否正常。

    2.  配置**Jenkins地址**和**Jenkins通道**。

    3.  单击**Save**。

9.  构建**demo-pipeline**并访问应用服务。

    1.  在Jenkins首页，单击**demo-pipeline**的![构建](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8913491261/p276903.png)图标。

        ![demo-pipeline](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8913491261/p38467.png)

    2.  根据您的镜像仓库信息修改构建参数。本示例中源码仓库分支为`master`，镜像为`registry.cn-beijing.aliyuncs.com/ack-cicd/ack-jenkins-demo:latest`。

        ![构建参数](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9913491261/p38468.png)

    3.  单击**开始构建**。

        测试Kubernetes集群动态分配的Jenkins Slave Pod与Jenkins Master是否连接正常。

        执行构建后，Jenkins从Kubernetes集群动态创建一个Slave Pod运行本次构建任务。关于示例应用代码，请参见[jenkins-demo-GitHub](https://github.com/AliyunContainerService/jenkins-demo)或[jenkins-demo-haoshuwei](https://code.aliyun.com/haoshuwei/jenkins-demo.git)。

    4.  查看**状态**，若构建成功则表示Jenkins on Kubernetes运行正常。

        ![Build History](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9913491261/p38469.png)


-   关于如何为Slave Pod配置maven缓存，请参见[为Slave Pod配置maven缓存](/cn.zh-CN/最佳实践/DevOps/快速搭建Jenkins环境并完成流水线作业.md)。
-   关于如何使用kaniko构建和推送容器镜像，请参见[使用kaniko构建和推送容器镜像](/cn.zh-CN/最佳实践/DevOps/快速搭建Jenkins环境并完成流水线作业.md)。

