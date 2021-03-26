---
keyword: [流水线构建, 搭建Jenkins环境]
---

# 在ASK上快速搭建Jenkins环境及执行流水线构建

本文主要演示如何在阿里云Serverless Kubernetes服务（ASK）上快速搭建Jenkins持续集成环境，并基于提供的应用示例快速完成应用源码编译、镜像构建和推送以及应用部署的流水线。

您已完成以下操作：

-   [创建Serverless Kubernetes集群](/cn.zh-CN/Serverless Kubernetes集群用户指南/快速入门/创建Serverless Kubernetes集群.md)
-   [通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)

## 部署Jenkins

1.  执行以下命令下载部署文件：

    ```
    git clone https://github.com/AliyunContainerService/jenkins-on-serverless.git
    cd jenkins-on-serverless
    ```

2.  完成`jenkins_home`持久化配置。

    Serverless Kubernetes目前不支持云盘，如需持久化`jenkins_home`，您可以挂载`nfs volume`，修改serverless-k8s-jenkins-deploy.yaml文件，取消以下字段注释并配置您的nfs信息：

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

    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**应用管理**。

    4.  在左侧导航栏中，选择**服务**。

    5.  单击Jenkins服务的外部端点登录Jenkins。

        ![外部端点](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6748649951/p141997.png)

    6.  在Jenkins登录页面，输入用户名和密码。默认用户名和密码均为**admin**，请于登录后进行修改。

        ![Jenkins登录页面](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6748649951/p38516.png)


## 创建集群证书和镜像仓库证书，构建和部署示例应用

1.  配置Kubernetes Cloud，动态创建Slave Pod。

    1.  在左侧导航栏中，选择**系统管理**。

    2.  在右侧的Manage Jenkins页面，单击**系统设置**。

    3.  在**Cloud**区域中，填写KubeConfig中的API Server URL作为**Kubernetes URL**。

        ![Kubernetes URL](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6748649951/p38521.png)

    4.  单击**凭证**右侧的**Add**。

        ![Add](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6748649951/p38523.png)

        在添加凭据前，先在Serverless Kubernetes集群的基本信息页面，找到**配置集群凭据**中提供的**KubeConfig**。

        ![KubeConfig](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6748649951/p38524.png)

        在弹出的添加凭据对话框中，完成以下配置。

        ![添加凭据](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6748649951/p38527.png)

        -   **类型**：选择**Docker Host Certificate Authentication**。
        -   **Client Key**：填写KubeConfig中client-key-data对应的内容。
        -   **Client Certificate**：填写KubeConfig中client-certificate-data对应的内容。
        -   **ID**：填写证书ID。本示例中填写k8sCertAuth。
        -   **描述**：填写描述。
    5.  单击**添加**。

    6.  测试连通性。

        从**Credentials**下拉框中选择上一步添加的凭据，单击**Test Connection**。

        ![3](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6748649951/p142012.png)

    7.  填写jenkins服务的外部端点作为**Jenkins URL**，jenkins-agent的外部端点作为**Jenkins tunnel**。

        ![Jenkins URL](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6748649951/p38531.png)

    8.  单击**Save**保存配置。

2.  通过kubectl在Serverless Kubernetes集群中创建jenkins-docker-cfg secret，用于设置镜像仓库权限。

    本示例中使用阿里云镜像服务提供的北京区域镜像仓库：

    ```
    docker login -u xxx -p xxx registry.cn-beijing.aliyuncs.com
    #输出：
    Login Succeeded
    
    kubectl create secret generic jenkins-docker-cfg  --from-file=/root/.docker/config.json
    ```

3.  构建demo-pipeline并访问应用服务。

    1.  在Jenkins首页，单击**demo-pipeline**。

    2.  在左侧导航栏中，选择**Build with Parameters**。

    3.  根据自己的镜像仓库信息修改构建参数，并填写KubeConfig中的API Server URL作为**api\_server\_url**。本示例中源码仓库分支为Serverless，镜像为**registry.cn-beijing.aliyuncs.com/haoshuwei/jenkins-java-demo:serverless**， 请根据提示信息进行替换。

        ![api_server_url](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6748649951/p38622.png)

    4.  单击**Build**。

    5.  查看Build History。下图表示构建成功。

        ![Build History](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6748649951/p38532.png)

    6.  构建成功后，登录[容器服务管理控制台](https://cs.console.aliyun.com)，查看应用的服务地址。


## 获取源码仓库

单击[源码仓库](https://github.com/AliyunContainerService/jenkins-demo.git)获取示例项目中使用的源码仓库。

