---
keyword: [搭建Jenkins环境, 流水线作业]
---

# 快速搭建Jenkins环境并完成流水线作业

本文主要演示如何在阿里云Kubernetes服务上快速搭建Jenkins持续集成环境，并基于提供的示例快速完成应用源码编译、应用镜像构建、推送以及应用部署的流水线。

## 前提条件

-   已创建Kubernetes集群。具体操作步骤，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   已安装[kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)命令行工具。

## 步骤一：部署Jenkins

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在左侧导航栏中，选择**市场** \> **应用目录**。选择**ack-jenkins**。

3.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

4.  在**阿里云应用**页签中，选择**ack-jenkins**。

5.  单击**参数**页签。

6.  选择Kubernetes**集群**以及**命名空间**，填写**发布名称**。

    ![命名空间](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0663659951/p38449.png)

    **说明：** 建议选择自定义命名空间或default命名空间。本示例中选择自定义命名空间ci。

7.  修改`AdminPassword`字段，并单击**创建**。

    **说明：** 为确保密码生效，您需要删除`AdminPassword`字段前的`#`。

    如果您未设置登录密码，即未修改`AdminPassword`字段，Jenkins部署成功后系统会自动生成密码，您可以通过以下命令进行查看：

    ```
    printf $(kubectl get secret --namespace ci jenkins-ci-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo
    ```

8.  在控制台左侧导航栏中，单击**集群**。

9.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

10. 在集群管理页左侧导航栏中，选择**服务与路由** \> **服务**。

11. 选择相应的命名空间，然后单击ack-jenkins-default服务的**外部端点**，访问并登录Jenkins。


## 步骤二：创建集群证书和镜像仓库证书并构建部署示例应用

1.  创建Kubernetes集群证书。

    1.  在Jenkins的左侧导航栏中，选择**Manage Jenkins**。

    2.  在右侧的Manage Jenkins页面，单击**Manage Nodes and Clouds**。

    3.  在左侧的Nodes导航栏，单击**Configure Clouds**。

    4.  在Configure Clouds页面中找到**凭据**，在其右侧选择**Add** \> **Jenkins**。

        ![Credentials](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4055414161/p38458.png)

        在添加凭据前，先在Kubernetes集群的集群信息页面的**连接信息**，找到**配置集群凭据**中提供的**KubeConfig**。有关更多详情，请参见[配置集群凭据](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。

        ![配置集群凭据](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0663659951/p38453.png)

    5.  在弹出的添加凭据对话框中，完成以下配置。

        ![添加凭据](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0663659951/p38526.png)

        -   **Kind**：选择**Docker Host Certificate Authentication**。
        -   **Client Key**：填写KubeConfig中client-key-data对应的内容。
        -   **Client Certificate**：填写KubeConfig中client-certificate-data对应的内容。
        -   **Server CA Certificate**：不用填写。
        -   **ID**：填写证书ID。本示例中填写k8sCertAuth。
        -   **Description**：填写描述。
    6.  单击**Add**。

    7.  从**凭据**下拉框中选择上一步添加的凭据，单击**连接测试**，测试连通性。

    8.  Kubernetes集群动态分配构建Pod，其配置如下图所示。

        ![构建pod](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4055414161/p38460.png)

    9.  配置Kubernetes Pod Template。

        slave-pipeline使用了4个Container分别完成流水线中各个Stage的构建。

        -   Container jnlp的配置如下图所示。

            ![Container jnlp](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4055414161/p38461.png)

            使用jenkins-slave-jnlp作为Docker镜像。jenkins-slave-jnlp用于构建节点jnlp连接master。

            ```
            registry.cn-beijing.aliyuncs.com/acs-sample/jenkins-slave-jnlp:3.14-1
            ```

        -   Container kaniko的配置如下图所示。

            ![Container kaniko](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0663659951/p38462.png)

            使用jenkins-slave-kaniko作为Docker镜像。jenkins-slave-kaniko用于构建和推送应用的镜像。

            ```
            registry.cn-beijing.aliyuncs.com/acs-sample/jenkins-slave-kaniko:0.6.0
            ```

        -   Container kubectl的配置如下图所示。

            ![Container kubectl](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0663659951/p38463.png)

            使用jenkins-slave-kubectl作为Dokcer镜像。jenkins-slave-kubectl用于kubectl部署应用。

            ```
            registry.cn-beijing.aliyuncs.com/acs-sample/jenkins-slave-kubectl:1.11.5
            ```

        -   Container maven的配置如下图所示。

            ![Container maven](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0663659951/p38465.png)

            使用jenkins-slave-maven作为Docker镜像。jenkins-slave-maven用于mvn打包构建。

            ```
            registry.cn-beijing.aliyuncs.com/acs-sample/jenkins-slave-maven:3.3.9-jdk-8-alpine
            ```

    10. 配置kaniko的镜像仓库权限。根据实际情况配置环境变量及Secret卷，示例配置如下图。

        ![kaniko配置镜像仓库权限](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0663659951/p38466.png)

    11. 单击**Save**保存配置。

2.  通过Kubectl在Kubernetes集群中创建Secret，用于设置镜像仓库权限。

    1.  登录镜像仓库。

        本示例中使用阿里云镜像服务提供的北京区域镜像仓库。

        ```
        docker login -u 镜像仓库用户名 -p 密码 registry.cn-beijing.aliyuncs.com
        ```

    2.  执行以下命令创建名为jenkins-docker-cfg的Secret。

        ```
        kubectl create secret generic jenkins-docker-cfg -n ci --from-file=/root/.docker/config.json
        ```

3.  构建demo-pipeline并访问应用服务。

    1.  在Jenkins首页，单击**demo-pipeline**。

        ![demo-pipeline](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0663659951/p38467.png)

    2.  在左侧导航栏中，选择**Build with Parameters**。

    3.  根据自己的镜像仓库信息修改构建参数。本示例中源码仓库分支为master，镜像为registry.cn-beijing.aliyuncs.com/ack-cicd/ack-jenkins-demo:latest。

        ![构建参数](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7337052161/p38468.png)

    4.  单击**Build**。

    5.  查看**Build History**，下图表示构建成功。

        ![Build History](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1663659951/p38469.png)

    6.  构建成功后，登录[容器服务管理控制台](https://cs.console.aliyun.com)，查看应用的服务地址。


## 构建任务示例demo-pipeline的详解

构建`demo-pipeline`任务的流程为：获取源码、构建应用包、打包和推送容器镜像以及部署应用到K8s集群。

整个构建流程按照示例项目中Jenkinsfile声明的内容执行构建。具体示例，可参见[Jenkinsfile示例项目](https://github.com/AliyunContainerService/jenkins-demo/blob/master/Jenkinsfile)。

**demo-pipeline的任务配置**

demo-pipeline设置了4个构建参数，参数说明如下所示：

-   origin\_repo，repo及image\_tag用于构建和推送应用容器镜像的地址。例如，示例应用的容器镜像要推送到registry.cn-beijing.aliyuncs.com/ack-cicd/ack-jenkins-demo:latest，则origin\_repo为registry.cn-beijing.aliyuncs.com/haoshuwei/ack-cicd，repo为ack-cicd，image\_tag为latest。
-   branch用于指定本次构建拉取应用源码仓库的分支。

![镜像仓库信息构建参数](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7016152161/p238055.png)

在Jenkins首页，单击**demo-pipeline**。在左侧导航栏中，选择**Configure**。选择**Pipeline**页签，可配置从GitHub仓库获取源码并指定Jenkinsfile路径。

![配置Pipeline](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7016152161/p238076.png)

**Jenkinsfile解析**

在示例项目源码的Jenkinsfile中定义以下内容：

```
pipeline{
      //定义groovy脚本中使用的环境变量。
      environment{
        // 将构建任务中的构建参数转换为环境变量。
        // 本示例中使用DEPLOY\_TO\_K8S变量来决定把应用部署到哪套容器集群环境中，如“Production Environment”， “Staging001 Environment”等。
        IMAGE_TAG =  sh(returnStdout: true,script: 'echo $image_tag').trim()
        ORIGIN_REPO =  sh(returnStdout: true,script: 'echo $origin_repo').trim()
        REPO =  sh(returnStdout: true,script: 'echo $repo').trim()
        BRANCH =  sh(returnStdout: true,script: 'echo $branch').trim()
      }
      //定义本次构建使用哪个标签的构建环境，本示例中为 “slave-pipeline”。
      agent{
        node{
          label 'slave-pipeline'
        }
      }
      // "stages"定义项目构建的多个模块，可以添加多个 “stage”，可以多个 “stage” 串行或者并行执行。
      stages{
        // 定义第一个stage，完成克隆源码的任务。
        stage('Git'){
          steps{
            git branch: '${BRANCH}', credentialsId: '', url: 'https://github.com/AliyunContainerService/jenkins-demo.git'
          }
        }
        //添加第二个stage，运行源码打包命令。
        stage('Package'){
          steps{
              container("maven") {
                  sh "mvn package -B -DskipTests"
              }
          }
        }
        //添加第三个stage, 运行容器镜像构建和推送命令，用到了environment中定义的groovy环境变量。
        stage('Image Build And Publish'){
          steps{
              container("kaniko") {
                  sh "kaniko -f `pwd`/Dockerfile -c `pwd` --destination=${ORIGIN_REPO}/${REPO}:${IMAGE_TAG} --skip-tls-verify"
              }
          }
        }
        // 添加第四个stage, 部署应用到指定K8s集群。
        stage('Deploy to Kubernetes') {
            parallel {
                    steps {
                        container('kubectl') {
                            step([$class: 'KubernetesDeploy', authMethod: 'certs', apiServerUrl: 'https://kubernetes.default.svc.cluster.local:443', credentialsId:'k8sCertAuth', config: 'deployment.yaml',variableState: 'ORIGIN_REPO,REPO,IMAGE_TAG'])
                        }
                    }
            }
        }
      }
}
```

代码详情，请参见[jenkins-demo/Jenkinsfile](https://github.com/AliyunContainerService/jenkins-demo/blob/master/Jenkinsfile)。

示例项目源码的各个代码块说明如下所示：

|代码块|用途|具体详情|
|:--|:-|----|
|`environment{}`|用于获取构建参数并转换为Jenkinsfile中使用的环境变量。|无。|
|`agent{}`|用于指定本次构建使用的构建节点标签为`slave-pipeline`。|在**Jenkins**左侧导航栏中，选择**Manage Jenkins** \> **Manage Nodes and Clouds** \> **Configure Clouds**，在Configure Clouds页面可以看到Kubernetes集群的配置，用于定义如何动态创建构建节点。

Kubernetes集群动态分配构建Pod，其配置如下图所示：

![Configure Clouds](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7337052161/p238347.png)

在Jenkins命名空间下创建Slave Pod：slave-pipeline。此Slave Pod包含4个Container Template分别完成流水线中各个Stage的构建。具体配置，请参见[系统配置详解](#section_r50_b6c_4cl)。

当使用kaniko容器进行容器镜像构建和推送时，需要获取镜像推送到仓库的权限。该权限是以K8s Secret的方式挂载到slave-pipeline的Pod中，环境变量`DOCKER_CONFIG`则定义了kaniko获取镜像仓库推送权限文件的默认路径。 在任意Linux系统上执行以下命令，创建名为`jenkins-docker-cfg`的Secret：

```
docker login -u <用户名\> -p <密码\> registry.cn-beijing.aliyuncs.com
kubectl create secret generic jenkins-docker-cfg -n ci --from-file=/root/.docker/config.json
``` |
|`stages{}`|定义了多个stage，分别完成持续集成及持续部署过程中的不同步骤。|获取源码。```
stage('Git'){
  steps{
    git branch: '${BRANCH}', credentialsId: '', url: 'https://github.com/AliyunContainerService/jenkins-demo.git'
  }
}
```

**说明：** 源码仓库中一般包含Jenkinsfile、Dockerfile、deployment.yaml等文件。 |
|maven用于编译和打包源码。```
stage('Package'){
  steps{
    container("maven") {
      sh "mvn package -B -DskipTests"
    }
  }
}
```

**说明：** maven编译时，若要使用缓存功能，需要使用NAS共享存储，创建NAS Volume并挂载到Kubernetes云的slave-pipeline模板上。 |
|kaniko用于容器镜像构建和部署。```
stage('Image Build And Publish'){
  steps{
    container("kaniko") {
      sh "kaniko -f `pwd`/Dockerfile -c `pwd` --destination=${ORIGIN_REPO}/${REPO}:${IMAGE_TAG} --skip-tls-verify"
    }
  }
}
```

**说明：** kaniko工具构建容器镜像可以不依赖Docker Daemon进程，在用户态空间完成镜像构建和推送，更安全可靠。 |
|kubectl用于部署K8s应用。**说明：** 在Kubectl部署K8s应用前需要先创建一个名称为k8sCertAuth的集群证书。

```
stage('Deploy to Kubernetes') {
  steps {
    container('kubectl') {
      step([$class: 'KubernetesDeploy', authMethod: 'certs', apiServerUrl: 'https://kubernetes.default.svc.cluster.local:443', credentialsId:'k8sCertAuth', config: 'deployment.yaml',variableState: 'ORIGIN_REPO,REPO,IMAGE_TAG'])
      //variableState字段声明需要替换deployment.yaml文件中的环境变量。
}
  }
}
``` |

## 系统配置详解

**构建环境配置说明**

-   Kubernetes集群动态分配构建Pod的配置如下图所示：

    ![Pod配置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7337052161/p238625.png)

-   slave-pipeline使用了4个Container分别完成流水线中各个stage的构建。
    -   Container jnlp用于Slave Pod连接Master，配置如下图所示：

        ![Container jnlp](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7337052161/p238632.png)

        使用用户自定义镜像作为Docker镜像。

        ```
        jenkinsci/jnlp-slave:3.35-5
        ```

    -   Container kaniko用于容器镜像构建和部署，配置如下图所示：

        ![Container kaniko](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7337052161/p238782.png)

        使用容器服务镜像作为Docker镜像。

        ```
        registry.cn-beijing.aliyuncs.com/acs/kaniko:v0.14.0
        ```

    -   Container kubectl用于部署K8s应用，配置如下图所示：

        ![Container kubectl](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7337052161/p238827.png)

        使用容器服务镜像作为Dokcer镜像。

        ```
        registry.cn-beijing.aliyuncs.com/acs/kubectl:1.14.8
        ```

    -   Container maven用于编译和打包源码，配置如下图所示：

        ![Conainer maven](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8337052161/p238838.png)

        使用用户自定义镜像作为Docker镜像。

        ```
        maven:3.6.2-jdk-14
        ```

-   kaniko配置镜像仓库权限：

    ![配置镜像仓库权限](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8337052161/p239206.png)


**项目实例说明**

-   项目实例中使用的源码仓库。

    ```
    https://github.com/AliyunContainerService/jenkins-demo.git
    ```

-   kaniko构建和推送Docker镜像说明。

    kaniko可以不依赖Docker Daemon并在用户空间执行完成Dockerfile中的每一行命令，最终完成Docker镜像的构建和推送。

    ```
    kaniko -f `pwd`/Dockerfile -c `pwd` --destination=${origin_repo}/${repo}:${image_tag}
    ```

-   部署应用到Kubernetes集群插件说明。

    在Jenkins首页，单击**demo-pipeline**。在左侧导航栏中，选择**Configure**。选择**Build**页签。

    插件配置如下：

    ![部署应用到Kubernetes集群](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8337052161/p238991.png)

    对应的Pipeline语法为：

    ```
    step([$class: 'KubernetesDeploy', authMethod: 'certs', apiServerUrl: 'https://kubernetes.default.svc.cluster.local:443', credentialsId:'k8sCertAuth', config: 'deployment.yaml',variableState: 'ORIGIN_REPO,REPO,IMAGE_TAG'])
    ```

    **说明：** 声明的变量ORIGIN\_REPO,REPO,IMAGE\_TAG可在构建执行时把deployment.yaml文件中对应的变量值替换为实际值。

-   Jenkinsfile说明。

    ```
    pipeline{
          // 定义groovy脚本中使用的环境变量。
          environment{
            // 本示例中使用DEPLOY_TO_K8S变量来决定把应用部署到哪套容器集群环境中，如“Production Environment”， “Staging001 Environment”等。
            IMAGE_TAG =  sh(returnStdout: true,script: 'echo $image_tag').trim()
            ORIGIN_REPO =  sh(returnStdout: true,script: 'echo $origin_repo').trim()
            REPO =  sh(returnStdout: true,script: 'echo $repo').trim()
            BRANCH =  sh(returnStdout: true,script: 'echo $branch').trim()
          }
          // 定义本次构建使用哪个标签的构建环境，本示例中为 “slave-pipeline”。
          agent{
            node{
              label 'slave-pipeline'
            }
          }
          // "stages"定义项目构建的多个模块，可以添加多个 “stage”，可以多个 “stage” 串行或者并行执行。
          stages{
            // 定义第一个stage，完成克隆源码的任务。
            stage('Git'){
              steps{
                git branch: '${BRANCH}', credentialsId: '', url: 'https://github.com/AliyunContainerService/jenkins-demo.git'
              }
            }
            // 添加第二个stage，运行源码打包命令。
            stage('Package'){
              steps{
                  container("maven") {
                      sh "mvn package -B -DskipTests"
                  }
              }
            }
            // 添加第三个stage, 运行容器镜像构建和推送命令，用到了environment中定义的groovy环境变量。
            stage('Image Build And Publish'){
              steps{
                  container("kaniko") {
                      sh "kaniko -f `pwd`/Dockerfile -c `pwd` --destination=${ORIGIN_REPO}/${REPO}:${IMAGE_TAG}"
                  }
              }
            }
             // 添加第四个stage, 部署应用到指定K8s集群。
            stage('Deploy to Kubernetes') {
                parallel {
                    stage('Deploy to Production Environment') {
                        when {
                            expression {
                                "$BRANCH" == "master"
                            }
                        }
                        steps {
                            container('kubectl') {
                                step([$class: 'KubernetesDeploy', authMethod: 'certs', apiServerUrl: 'https://kubernetes.default.svc.cluster.local:443', credentialsId:'k8sCertAuth', config: 'deployment.yaml',variableState: 'ORIGIN_REPO,REPO,IMAGE_TAG'])
                            }
                        }
                    }
                    stage('Deploy to Staging001 Environment') {
                        when {
                            expression {
                                "$BRANCH" == "latest"
                            }
                        }
                        steps {
                            container('kubectl') {
                                step([$class: 'KubernetesDeploy', authMethod: 'certs', apiServerUrl: 'https://kubernetes.default.svc.cluster.local:443', credentialsId:'k8sCertAuth', config: 'deployment.yaml',variableState: 'ORIGIN_REPO,REPO,IMAGE_TAG'])
                            }
                        }
                    }
                }
            }
          }
        }
    ```


-   [源码仓库](https://github.com/AliyunContainerService/jenkins-demo.git)
-   [容器服务ACK](https://www.aliyun.com/product/kubernetes)
-   [kaniko](https://github.com/GoogleContainerTools/kaniko)

  


