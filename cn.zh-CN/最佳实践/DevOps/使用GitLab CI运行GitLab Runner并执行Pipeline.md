---
keyword: [GitLab CI, 运行GitLab Runner, 执行Pipeline]
---

# 使用GitLab CI运行GitLab Runner并执行Pipeline

本文主要演示如何在Kubernetes集群中安装、注册GitLab Runner，添加Kubernetes类型的Executor来执行构建，并以此为基础完成一个Java源码示例项目从编译构建、镜像打包到应用部署的CICD过程。

## 背景信息

本文以构建一个Java软件项目并将其部署到阿里云容器服务Kubernetes集群中为例，说明如何使用GitLab CI在阿里云Kubernetes服务上运行GitLab Runner、配置Kubernetes类型的Executor，并执行Pipeline。

## 创建GitLab源码项目并上传示例代码

1.  创建GitLab源码项目。

    本示例中创建的GitLab源码项目地址为：

    ```
    http://xx.xx.xx.xx/demo/gitlab-java-demo.git
    ```

2.  执行以下命令获取示例代码并上传至GitLab。

    ```
    git clone https://github.com/haoshuwei/gitlab-ci-k8s-demo.git
    git remote add gitlab http://xx.xx.xx.xx/demo/gitlab-java-demo.git
    git push gitlab master
    ```


## 在Kubernetes集群中安装GitLab Runner

1.  获取GitLab Runner的注册信息。
    1.  获取项目专用Runner的注册信息。
        1.  登录[GitLab](https://about.gitlab.com/)。
        2.  在顶部导航栏中，选择**Projects** \> **Your projects**。
        3.  在**Your projects**页签下，选择相应的Project。
        4.  在左侧导航栏中，选择**Settings** \> **CI / CD**。
        5.  单击**Runners**右侧的**Expand**。

            ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1663659951/p38533.png)

        6.  获取URL和registration token信息。

            ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2663659951/p38535.png)

    2.  获取Group Runners的注册信息。
        1.  在顶部导航栏中，选择**Groups** \> **Your groups**。
        2.  在**Your groups**页签下，选择相应的group。
        3.  在左侧导航栏中，选择**Settings** \> **CI / CD**。
        4.  单击**Runners**右侧的**Expand**。

            ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2663659951/p38536.png)

        5.  获取URL和registration token信息。

            ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2663659951/p38537.png)

    3.  获取Shared Runners的注册信息。

        **说明：** 只有管理员有权限执行此步操作。

        1.  在顶部导航栏中，单击![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2663659951/p38538.png)进入Admin Area页面。
        2.  在左侧导航栏中，选择**Overview** \> **Runners**。
        3.  获取URL和registration token信息。

            ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2663659951/p38539.png)

2.  执行以下命令获取并修改GitLab Runner的Helm Chart。

    ```
    git clone https://github.com/haoshuwei/gitlab-runner.git
    ```

    替换gitlabUrl和runnerRegistrationToken字段，示例如下：

    ```
    ## GitLab Runner Image
    ##
    image: gitlab/gitlab-runner:alpine-v11.4.0
    
    ## Specify a imagePullPolicy
    ##
    imagePullPolicy: IfNotPresent
    
    ## Default container image to use for initcontainer
    init:
      image: busybox
      tag: latest
    
    ## The GitLab Server URL (with protocol) that want to register the runner against
    ##
    gitlabUrl: http://xx.xx.xx.xx/
    
    ## The Registration Token for adding new Runners to the GitLab Server. This must
    ## be retreived from your GitLab Instance.
    ##
    runnerRegistrationToken: "AMvEWrBTBu-d8czE****"
    ## Unregister all runners before termination
    ##
    unregisterRunners: true
    
    ## Configure the maximum number of concurrent jobs
    ##
    concurrent: 10
    
    ## Defines in seconds how often to check GitLab for a new builds
    ##
    checkInterval: 30
    
    ## For RBAC support:
    ##
    rbac:
      create: true
      clusterWideAccess: false
    
    ## Configure integrated Prometheus metrics exporter
    ##
    metrics:
      enabled: true
    
    ## Configuration for the Pods that that the runner launches for each new job
    ##
    runners:
      ## Default container image to use for builds when none is specified
      ##
      image: ubuntu:16.04
    
      ## Specify the tags associated with the runner. Comma-separated list of tags.
      ##
      tags: "k8s-runner"
    
      ## Run all containers with the privileged flag enabled
      ## This will allow the docker:dind image to run if you need to run Docker
      ## commands. Please read the docs before turning this on:
      ##
      privileged: true
    
      ## Namespace to run Kubernetes jobs in (defaults to the same namespace of this release)
      ##
      namespace: gitlab
    
      cachePath: "/opt/cache"
    
      cache: {}
      builds: {}
      services: {}
      helpers: {}
    
    resources: {}
    ```

3.  执行以下命令安装GitLab Runner。

    -   打包应用

        ```
        helm package .
        ```

        预期输出：

        ```
        Successfully packaged chart and saved it to: /root/gitlab/gitlab-runner/gitlab-runner-0.1.37.tgz
        ```

    -   安装应用

        ```
        helm install --namespace gitlab gitlab-runner *.tgz
        ```

    查看相关的deployment/pod是否成功启动。若成功启动，则可在GitLab上看到注册成功的GitLab Runner，如下图所示。

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2663659951/p38540.png)


## 缓存配置

GitLab Runner对缓存方案的支持有限，所以您需要使用挂载Volume的方式做缓存。在上面的示例中，安装GitLab Runner时默认使用/opt/cache目录作为缓存空间。您也可以通过修改values.yaml文件中的`runners.cachePath`字段修改缓存目录。

例如，如需建立Maven缓存，您可以在`variables`下添加`MAVEN_OPTS`变量并指定本地缓存目录：

```
variables:
  KUBECONFIG: /etc/deploy/config
  MAVEN_OPTS: "-Dmaven.repo.local=/opt/cache/.m2/repository"
```

修改templates/configmap.yaml文件中的以下字段挂载docker.sock和用于cache的volume。

```
cat >>/home/gitlab-runner/.gitlab-runner/config.toml <<EOF
            [[runners.kubernetes.volumes.pvc]]
              name = "gitlab-runner-cache"
              mount_path = "{{ .Values.runners.cachePath }}"
            [[runners.kubernetes.volumes.host_path]]
              name = "docker"
              mount_path = "/var/run/docker.sock"
              read_only = true
              host_path = "/var/run/docker.sock"
    EOF
```

## 设置全局变量

1.  在GitLab的顶部导航栏中，选择**Projects** \> **Your projects**。
2.  在**Your projects**页签下，选择相应的Project。
3.  在左侧导航栏中，选择**Settings** \> **CI / CD**。
4.  单击**Variables**右侧的**Expand**。添加GitLab Runner可用的环境变量。本示例中，添加以下三个变量。

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2663659951/p38541.png)

    -   REGISTRY\_USERNAME：镜像仓库用户名。
    -   REGISTRY\_PASSWORD：镜像仓库密码。
    -   kube\_config：KubeConfig的编码字符串。
    执行以下命令生成KubeConfig的编码字符串：

    ```
    echo $(cat ~/.kube/config | base64) | tr -d " "
    ```


## 编写.gitlab-ci.yml

编写.gitlab-ci.yml文件，完成Java Demo源码项目的编译构建、镜像推送和应用部署（可参考gitlabci-java-demo源码项目中的.gitlab-ci.yml.example）。

.gitlab-ci.yml示例如下。

```
image: docker:stable
stages:
  - package
  - docker_build
  - deploy_k8s
variables:
  KUBECONFIG: /etc/deploy/config
  MAVEN_OPTS: "-Dmaven.repo.local=/opt/cache/.m2/repository"
mvn_build_job:
  image: maven:3.6.2-jdk-14
  stage: package
  tags:
    - k8s-runner
  script:
    - mvn package -B -DskipTests
    - cp target/demo.war /opt/cache
docker_build_job:
  image: docker:latest
  stage: docker_build
  tags:
    - k8s-runner
  script:
    - docker login -u $REGISTRY_USERNAME -p $REGISTRY_PASSWORD registry.cn-beijing.aliyuncs.com
    - mkdir target
    - cp /opt/cache/demo.war target/demo.war
    - docker build -t registry.cn-beijing.aliyuncs.com/haoshuwei24/gitlabci-java-demo:$CI_PIPELINE_ID .
    - docker push registry.cn-beijing.aliyuncs.com/haoshuwei24/gitlabci-java-demo:$CI_PIPELINE_ID
deploy_k8s_job:
  image: registry.cn-hangzhou.aliyuncs.com/haoshuwei24/kubectl:1.16.6
  stage: deploy_k8s
  tags:
    - k8s-runner
  script:
    - mkdir -p /etc/deploy
    - echo $kube_config |base64 -d > $KUBECONFIG
    - sed -i "s/IMAGE_TAG/$CI_PIPELINE_ID/g" deployment.yaml
    - cat deployment.yaml
    - kubectl apply -f deployment.yaml
```

.gitlab-ci.yml定义了一个Pipeline， 分三个阶段步骤执行：

```
image: docker:stable  # Pipeline中各个步骤阶段的构建镜像没有指定时， 默认使用docker:stable镜像
stages:
  - package                # 源码打包阶段
  - docker_build         # 镜像构建和打包推送阶段
  - deploy_k8s           # 应用部署阶段
variables:
  KUBECONFIG: /etc/deploy/config   # 定义全局变量KUBECONFIG
```

-   maven源码打包阶段。

    ```
    mvn_build_job:     # job名称
      image: maven:3.6.2-jdk-14  # 本阶段构建使用的构建镜像
      stage: package      # 关联的阶段名称
      tags:                     # GitLab Runner的tag
        - k8s-runner
      script:
        - mvn package -B -DskipTests  # 执行构建脚本
        - cp target/demo.war /opt/cache   # 构建物保存至缓存区
    ```

-   镜像构建和打包推送阶段。

    ```
    docker_build_job:  # job名称
      image: docker:latest # 本阶段构建使用的构建镜像
      stage: docker_build  # 关联的阶段名称
      tags:                      # GitLab Runner的tag
        - k8s-runner
      script:
        - docker login -u $REGISTRY_USERNAME -p $REGISTRY_PASSWORD registry.cn-beijing.aliyuncs.com   # 登录镜像仓库
        - mkdir target
        - cp /opt/cache/demo.war target/demo.war
        - docker build -t registry.cn-beijing.aliyuncs.com/haoshuwei24/gitlabci-java-demo:$CI_PIPELINE_ID .     # 打包Docker镜像，使用的tag为本次Pipeline的ID
        - docker push registry.cn-beijing.aliyuncs.com/haoshuwei24/gitlabci-java-demo:$CI_PIPELINE_ID      # 推送Docker镜像
    ```

-   应用部署阶段。

    ```
    deploy_k8s_job:   # job名称
      image: registry.cn-hangzhou.aliyuncs.com/haoshuwei24/kubectl:1.16.6   # 本阶段构建使用的构建镜像
      stage: deploy_k8s   # 关联的阶段名称
      tags:                      # GitLab Runner的tag
        - k8s-runner
      script:
        - mkdir -p /etc/deploy
        - echo $kube_config |base64 -d > $KUBECONFIG   # 配置连接Kubernetes集群的config文件
        - sed -i "s/IMAGE_TAG/$CI_PIPELINE_ID/g" deployment.yaml  # 动态替换部署文件中的镜像tag
        - cat deployment.yaml
        - kubectl apply -f deployment.yaml
    ```


## 执行Pipeline

提交.gitlab-ci.yml文件后，Project gitlab-java-demo会自动检测到这个文件并执行Pipeline， 如下图所示。

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2663659951/p38542.png)

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2663659951/p38543.png)

## 访问服务

如果部署文件中没有指定Namespace，则默认会部署到GitLab命名空间下：

```
 kubectl -n gitlab get svc 
```

预期输出：

```
NAME        TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
java-demo   LoadBalancer   172.19.9.252   xx.xx.xx.xx   80:32349/TCP   1m
```

浏览器访问xx.xx.xx.xx/demo进行验证。

了解更多容器服务的相关内容，请参见[容器服务](https://www.aliyun.com/product/containerservice)。

了解更多GitLab CI的相关内容，请参见[GitLab CI](https://docs.gitlab.com/runner/)。

