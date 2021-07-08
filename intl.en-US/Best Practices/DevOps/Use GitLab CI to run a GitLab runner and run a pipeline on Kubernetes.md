---
keyword: [GitLab CI, run a GitLab runner, run a pipeline]
---

# Use GitLab CI to run a GitLab runner and run a pipeline on Kubernetes

This topic describes how to install and register GitLab runners in a Kubernetes cluster and add a Kubernetes executor to build an application. The topic also provides step-by-step examples to implement a continuous integration \(CI\)/continuous delivery \(CD\) pipeline that includes stages, such as source code compilation, image build and push, and application deployment.

## Background information

In the following example, a Java project is built and deployed in a Container Service for Kubernetes \(ACK\) cluster. This example shows how to use GitLab CI to run a GitLab runner, set a Kubernetes executor, and execute a CI/CD pipeline.

## Create a GitLab project and upload sample code

1.  Create a GitLab project.

    Address of the created GitLab project in this example:

    ```
    http://xx.xx.xx.xx/demo/gitlab-java-demo.git
    ```

2.  Run the following commands to create a copy of the sample code and upload the copy to GitLab:

    ```
    git clone https://github.com/haoshuwei/gitlab-ci-k8s-demo.git
    git remote add gitlab http://xx.xx.xx.xx/demo/gitlab-java-demo.git
    git push gitlab master
    ```


## Install GitLab Runner in an ACK cluster

1.  Obtain registration information about GitLab runners.
    1.  Obtain registration information about the runners for this project.
        1.  Log on to the [GitLab](https://about.gitlab.com/) console.
        2.  In the top navigation bar of the GitLab console, choose **Projects** \> **Your projects**.
        3.  On the **Your projects** tab, select the project that you want to use.
        4.  In the left-side navigation pane, choose **Settings** \> **CI / CD**.
        5.  Click **Expand** on the right side of **Runners**.

            ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3346858951/p38533.png)

        6.  Copy the URL and registration token.

            ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3346858951/p38535.png)

    2.  Obtain registration information about group runners.
        1.  In the top navigation bar, choose **Groups** \> **Your groups**.
        2.  On the **Your groups** tab, select the required group.
        3.  In the left-side navigation pane, choose **Settings** \> **CI / CD**.
        4.  Click **Expand** on the right side of **Runners**.

            ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4346858951/p38536.png)

        5.  Copy the URL and registration token.

            ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4346858951/p38537.png)

    3.  Obtain registration information about shared runners.

        **Note:** Only the administrator has the permission to perform this step.

        1.  In the top navigation bar, click ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4346858951/p38538.png) to go to the Admin Area page.
        2.  In the left-side navigation pane, choose **Overview** \> **Runners**.
        3.  Copy the URL and registration token.

            ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4346858951/p38539.png)

2.  Run the following command to obtain a copy of the Helm chart of GitLab Runner:

    ```
    git clone https://github.com/haoshuwei/gitlab-runner.git
    ```

    Replace the gitlabUrl and runnerRegistrationToken fields as shown in the following code:

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

3.  Run the following commands to install GitLab Runner:

    -   Package the Helm chart of GitLab Runner.

        ```
        helm package .
        ```

        Expected output:

        ```
        Successfully packaged chart and saved it to: /root/gitlab/gitlab-runner/gitlab-runner-0.1.37.tgz
        ```

    -   Install GitLab Runner.

        ```
        helm install --namespace gitlab gitlab-runner *.tgz
        ```

    Check whether the related Deployment or pods have been started. If the related Deployment or pods have been started, the registered GitLab runners will be displayed in GitLab, as shown in the following figure.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4346858951/p38540.png)


## Cache settings

GitLab Runner has a limited cache capacity. You must mount volumes to store cache data. In the preceding example, the /opt/cache directory is used as the default cache directory after GitLab Runner is installed. You can modify the `runners.cachePath` field in the values.yaml file to change the cache directory.

For example, to create a cache for Apache Maven, add the `MAVEN_OPTS` variable to the `variables` field and specify a local cache directory. The following code is an example:

```
variables:
  KUBECONFIG: /etc/deploy/config
  MAVEN_OPTS: "-Dmaven.repo.local=/opt/cache/.m2/repository"
```

Modify the following fields in the templates/configmap.yaml file to mount docker.sock and the volume to store cache data:

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

## Set global variables

1.  In the top navigation bar, choose **Projects** \> **Your projects**.
2.  On the **Your projects** tab, select the project that you want to use.
3.  In the left-side navigation pane, choose **Settings** \> **CI / CD**.
4.  Click **Expand** on the right side of **Variables**. Add environment variables for GitLab Runner. In this example, add the following variables:

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4346858951/p38541.png)

    -   REGISTRY\_USERNAME: The username for logging on to the image repository.
    -   REGISTRY\_PASSWORD: The password for logging on to the image repository.
    -   kube\_config: The KubeConfig as an encoded string.
    Run the following command to convert KubeConfig into an encoded string:

    ```
    echo $(cat ~/.kube/config | base64) | tr -d " "
    ```


## Edit the .gitlab-ci.yml file

Use the .gitlab-ci.yml file to compile the source code, build an image, push the image, and deploy the application for the Java demo project. For more information, refer to .gitlab-ci.yml.example of the gitlabci-java-demo project.

The following template is an example of the .gitlab-ci.yml file:

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

The .gitlab-ci.yml file defines a pipeline that consists of the following stages:

```
image: docker:stable  # By default, if no image is specified for each stage of the pipeline, the docker:stable image is used.
stages:
  - package                # Package the source code.
  - docker_build         # Build, package, and push the image.
  - deploy_k8s           # Deploy the application.
variables:
  KUBECONFIG: /etc/deploy/config   # Define the global variable KUBECONFIG.
```

-   Package the source code by using Apache Maven.

    ```
    mvn_build_job:     # The job name.
      image: maven:3.6.2-jdk-14  # The image that is used in this stage.
      stage: package      # The name of the stage.
      tags:                     # The tag of the GitLab Runner image.
        - k8s-runner
      script:
        - mvn package -B -DskipTests  # Run the build script.
        - cp target/demo.war /opt/cache  # Save the build output to cache.
    ```

-   Build, package, and push the image.

    ```
    docker_build_job:  # The job name.
      image: docker:latest # The image that is used in this stage.
      stage: docker_build   # The name of the stage.
      tags:                      # The tag of the GitLab Runner image.
        - k8s-runner
      script:
        - docker login -u $REGISTRY_USERNAME -p $REGISTRY_PASSWORD registry.cn-beijing.aliyuncs.com   # Log on to the image repository.
        - mkdir target
        - cp /opt/cache/demo.war target/demo.war
        - docker build -t registry.cn-beijing.aliyuncs.com/haoshuwei24/gitlabci-java-demo:$CI_PIPELINE_ID .     # Package the Docker image. Use the pipeline ID as its tag.
        - docker push registry.cn-beijing.aliyuncs.com/haoshuwei24/gitlabci-java-demo:$CI_PIPELINE_ID      # Push the Docker image.
    ```

-   Deploy the application.

    ```
    deploy_k8s_job:   # The job name.
      image: registry.cn-hangzhou.aliyuncs.com/haoshuwei24/kubectl:1.16.6   # The image that is used in this stage.
      stage: deploy_k8s  # The name of the stage.
      tags:                      # The tag of the GitLab Runner image.
        - k8s-runner
      script:
        - mkdir -p /etc/deploy
        - echo $kube_config |base64 -d > $KUBECONFIG   # Configure the config file that is used to connect to the Kubernetes cluster.
        - sed -i "s/IMAGE_TAG/$CI_PIPELINE_ID/g" deployment.yaml  # Dynamically replace the image tag in the deployment file.
        - cat deployment.yaml
        - kubectl apply -f deployment.yaml
    ```


## Execute the pipeline

After you submit the .gitlab-ci.yml file, the gitlab-java-demo project will automatically detect this file and execute the pipeline, as shown in the following figures.

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4346858951/p38542.png)

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4346858951/p38543.png)

## Access the application

By default, if no namespace is specified in the deployment file, the application is deployed in the GitLab namespace.

```
 kubectl -n gitlab get svc 
```

Expected output:

```
NAME        TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
java-demo   LoadBalancer   172.19.9.252   xx.xx.xx.xx   80:32349/TCP   1m
```

Visit xx.xx.xx.xx/demo by using your browser to access the application.

For more information about ACK, see [Container Service for Kubernetes](https://www.alibabacloud.com/zh/product/container-service).

For more information about GitLab CI, see [GitLab CI](https://docs.gitlab.com/runner/).

