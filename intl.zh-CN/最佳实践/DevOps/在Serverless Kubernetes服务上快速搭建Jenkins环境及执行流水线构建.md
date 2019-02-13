# 在Serverless Kubernetes服务上快速搭建Jenkins环境及执行流水线构建 {#concept_fmw_tjf_qgb .concept}

本文主要演示如何在阿里云Serverless Kubernetes服务上快速搭建Jenkins持续集成环境，并基于提供的示例应用快速完成应用源码编译、镜像构建和推送以及应用部署的流水线。

## 前提条件 {#section_byn_glf_qgb .section}

您已经成功创建了一个Serverless Kubernetes集群。有关详细信息，请参见[创建 Serverless Kubernetes 集群](../../../../../intl.zh-CN/用户指南/Serverless Kubernetes 集群/集群管理/创建 Serverless Kubernetes 集群.md#)。

## 部署Jenkins {#section_z1g_mlf_qgb .section}

1.  执行以下命令下载部署文件：

    ```
    $ git clone https://github.com/AliyunContainerService/jenkins-on-serverless.git
    $ cd jenkins-on-serverless
    ```

2.  完成jenkins\_home持久化配置。

    Serverless Kubernetes目前不支持云盘，如需持久化jenkins\_home，您可以挂载nfs volume, 修改serverless-k8s-jenkins-deploy.yaml文件，注释掉以下字段并配置您的nfs信息：

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
    $ kubectl apply -f serverless-k8s-jenkins-deploy.yaml
    ```

4.  登录Jenkins。
    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
    2.  在左侧导航栏中，选择**路由与负载均衡** \> **服务**。
    3.  单击Jenkins服务的外部端点登录Jenkins。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122940/155004960838515_zh-CN.png)

    4.  在Jenkins登录页面，输入用户名和密码。默认用户名和密码均为admin，请于登录后进行修改。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122940/155004960938516_zh-CN.png)


## 创建集群证书和镜像仓库证书，构建和部署示例应用 {#section_xbb_t1g_qgb .section}

1.  配置Kubernetes Cloud，动态创建slave pod。
    1.  在左侧导航栏中，选择**系统管理**。
    2.  在右侧的Manage Jenkins页面，单击**系统设置**。
    3.  在**Cloud**区域中，填写KubeConfig中的API server URL作为**Kubernetes URL**。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122940/155004960938521_zh-CN.png)

    4.  单击**Credentials**右侧的**Add**。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122940/155004960938523_zh-CN.png)

        在添加凭据前，先在Serverless Kubernetes集群的基本信息页面，找到**配置集群凭据**中提供的**KubeConfig**。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122940/155004960938524_zh-CN.png)

        在弹出的添加凭据对话框中，完成以下配置：

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122940/155004960938527_zh-CN.png)

        -   **类型**：选择**Docker Host Certificate Authentication**。
        -   **Client Key**: 填写KubeConfig中client-key-data对应的内容。
        -   **Client Certificate**: 填写KubeConfig中client-certificate-data对应的内容。
        -   **ID**：填写证书ID。本示例中填写k8sCertAuth。
        -   **描述**：填写描述。
    5.  单击**添加**。
    6.  测试连通性。

        从**Credentials**下拉框中选择上一步添加的凭据，单击**Test Connection**。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122940/155004960938529_zh-CN.png)

    7.  填写jenkins服务的外部端点作为**Jenkins URL**，jenkins-agent的外部端点作为**Jenkins tunnel**。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122940/155004960938531_zh-CN.png)

    8.  单击**Save**保存配置。
2.  通过kubectl在Serverless Kubernetes集群中创建jenkins-docker-cfg secret，用于设置镜像仓库权限。

    本示例中使用阿里云镜像服务提供的北京区域镜像仓库：

    ```
    $ docker login -u xxx -p xxx registry.cn-beijing.aliyuncs.com
    Login Succeeded
    $ kubectl create secret generic jenkins-docker-cfg  --from-file=/root/.docker/config.json
    ```

3.  构建demo-pipeline并访问应用服务。
    1.  在Jenkins首页，单击**demo-pipeline**。
    2.  在左侧导航栏中，选择**Build with Parameters**。
    3.  根据自己的镜像仓库信息修改构建参数，并填写KubeConfig中的API server URL作为**api\_server\_url**。本示例中源码仓库分支为master，镜像为registry.cn-beijing.aliyuncs.com/haoshuwei:stable。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122940/155004960938622_zh-CN.png)

    4.  单击**Build**。
    5.  查看Build History。下图表示构建成功。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122940/155004960938532_zh-CN.png)

    6.  构建成功后，登录[容器服务管理控制台](https://cs.console.aliyun.com)，查看应用的服务地址。

点击[这里](https://github.com/AliyunContainerService/jenkins-demo.git)获取示例项目中使用的源码仓库。

