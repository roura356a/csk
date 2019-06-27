# Use GitLab CI to run a GitLab runner and activate a pipleline in a Kubernetes cluster {#concept_sjb_hmg_qgb .concept}

This topic describes how to install and register a GitLab runner in a Kubernetes cluster and add a Kubernetes executor to build an application. After completing the preceding operations, you can build a Java application source code project. The example in this topic shows a CI/CD pipleline that includes the following actions in order: compiling and building the code, packaging the application image, and deploying the application.

## Background {#section_dx4_qmg_qgb .section}

In the following example, a Java software project is built and deployed in a Kubernetes cluster that runs on Alibaba Cloud Container Service for Kubernetes. The example shows how to use GitLab CI to run a GitLab runner, set a Kubernetes executor, and activate a pipeline in an Alibaba Cloud Kubernetes cluster.

## Create the GigLab source code project and upload the example code {#section_ts4_tmg_qgb .section}

1.  Create the GitLab source code project.

    In this example, the address of the created GitLab source code project is as follows:

    ``` {#codeblock_wbm_383_uqb}
    http://xx.xx.xx.xx/demo/gitlab-java-demo.git
    ```

2.  Run the following commands to obtain the example code and upload it to GitLab:

    ``` {#codeblock_swy_8kh_l6t}
    $ git clone https://code.aliyun.com/CodePipeline/gitlabci-java-demo.git
    $ git remote add gitlab http://xx.xx.xx.xx/demo/gitlab-java-demo.git
    $ git push gitlab master
    ```


## Install the GitLab runner in a Kubernetes cluster {#section_hbd_1ng_qgb .section}

1.  Obtain the GitLab runner registration information.
    1.  Obtain the registration information of the runner specific to the project.
        1.  Log on to GitLab.
        2.  In the top navigation bar, choose **Projects** \> **Your projects**.
        3.  On the **Your projects** tab page, select the target project.
        4.  In the left-side navigation pane, choose **Settings** \> **CI / CD**.
        5.  Click **Expand** on the right of **Runners**.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/123018/156161404038533_en-US.png)

        6.  Obtain the URL and the registration token.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/123018/156161404138535_en-US.png)

    2.  Obtain the group runner registration information.
        1.  In the top navigation bar, choose **Groups** \> **Your groups**.
        2.  On the **Your groups** tab page, select the target group.
        3.  In the left-side navigation pane, choose **Settings** \> **CI / CD**.
        4.  Click **Expand** on the right of **Runners**.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/123018/156161404138536_en-US.png)

        5.  Obtain the URL and the registration token.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/123018/156161404138537_en-US.png)

    3.  Obtain the shared runner registration information.

        **Note:** Only the administrator has permission to perform this action.

        1.  In the top navigation bar, click ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/123018/156161404138538_en-US.png).
        2.  In the left-side navigation pane of the Admin Area page, choose **Overview** \> **Runners**.
        3.  Obtain the URL and the registration token.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/123018/156161404138539_en-US.png)

2.  Run the following command to obtain and modify the Helm Chart of the GitLab runner:

    ``` {#codeblock_xf0_jzy_vm0}
    $ git clone https://code.aliyun.com/CodePipeline/gitlab-runner.git
    ```

    Modify the values.yaml file as follows:

    ``` {#codeblock_ab5_ocr_lfs}
    ## GitLab Runner Image
    ##
    image: gitlab/gitlab-runner:alpine-v11.4.0
    
    ## Specify an imagePullPolicy
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
    ## be retrieved from your GitLab Instance.
    ##
    runnerRegistrationToken: "AMvEWrBTBu-d8czEYyfY"
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

3.  Run the following command to install the GitLab runner.

    ``` {#codeblock_uya_xfh_mry}
    $ helm package .
    Successfully packaged chart and saved it to: /root/gitlab/gitlab-runner/gitlab-runner-0.1.37.tgz
    
    $ helm install --namespace gitlab --name gitlab-runner *.tgz
    ```

    Check whether the related deployment/pod has been started. If the related deployment/pod has been started, the GitLab runner that has been registered in GitLab is displayed.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/123018/156161404238540_en-US.png)


## Set the GtiLab runner cache {#section_nmt_nsp_qgb .section}

GitLab runners have a limited cache capacity. Therefore, you need to mount a volume to your GitLab runner so that the volume functions as the GitLab runner cache. In this example, the /opt/cache directory is used as the GitLab runner cache by default. You can modify the `runners.cachePath` field of the values.yaml file to change the default cache directory.

For example, to create a maven cache, add the `MAVEN_OPTS` variable to `variables` and specify a local cache directory as follows:

``` {#codeblock_jkg_7uw_rwr}
variables:
  KUBECONFIG: /etc/deploy/config
  MAVEN_OPTS: "-Dmaven.repo.local=/opt/cache/.m2/repository"
```

To mount a new volume, modify the following fields in the templates/configmap.yaml file:

``` {#codeblock_1ja_cos_fgu}
cat >>/home/gitlab-runner/.gitlab-runner/config.toml <<EOF
            [[runners.kubernetes.volumes.pvc]]
              name = "gitlab-runner-cache"
              mount_path = "{{ .Values.runners.cachePath }}"
EOF
```

This means that you need to modify the settings of config.toml during the period between the time at which the GigLab runner was registered and the time at which the runner has not yet been started.

## Set the global variables {#section_d4v_ssp_qgb .section}

1.  In the top navigation bar, choose **Projects** \> **Your projects**.
2.  On the **Your projects** tab page, select the target project.
3.  In the left-side navigation pane, choose **Settings** \> **CI / CD**.
4.  Click **Expand** on the right of **Runners** to add available to the GitLab runner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/123018/156161404238541_en-US.png)

    In this example, add the following three variables:

    -   REGISTRY\_USERNAME: indicates a registry username.
    -   REGISTRY\_PASSWORD: indicates a registry password.
    -   kube\_config: indicates a string of KubeConfig code characters.
    Run the following command to generate a string of KubeConfig code characters:

    ``` {#codeblock_wsb_sxm_9uo}
    echo $(cat ~/.kube/config | base64) | tr -d " "
    ```


## Edit the .gitlab-ci.yml file {#section_ubt_vsp_qgb .section}

Edit the .gitlab-ci.yml file to compile and build the Java demo source code project, push the application image, and deploy the application. For more information, see the .gitlab-ci.yml.example of the gitlabci-java-demo source code project. This section first shows an example of an entire YAML file, and then describes key sections of the file in more detail.

The entire .gitlab-ci.yml file is as follows:

``` {#codeblock_m25_9e9_mx2}
image: docker:stable
stages:
  - package
  - docker_build
  - deploy_k8s
variables:
  KUBECONFIG: /etc/deploy/config
mvn_build_job:
  image: registry.cn-beijing.aliyuncs.com/codepipeline/public-blueocean-codepipeline-slave-java:0.1-63b99a20
  stage: package
  tags:
    - k8s-test
  script:
    - mvn package -B -DskipTests
    - cp target/demo.war /opt/cache
docker_build_job:
  image: registry.cn-beijing.aliyuncs.com/codepipeline/public-blueocean-codepipeline-slave-java:0.1-63b99a20
  stage: docker_build
  services:
    - docker:dind
  variables:
    DOCKER_DRIVER: overlay
    DOCKER_HOST: tcp://localhost:2375
  tags:
    - k8s-test
  script:
    - docker login -u $REGISTRY_USERNAME -p $REGISTRY_PASSWORD registry.cn-beijing.aliyuncs.com
    - mkdir target
    - cp /opt/cache/demo.war target/demo.war
    - docker build -t registry.cn-beijing.aliyuncs.com/gitlab-demo/java-demo:$CI_PIPELINE_ID .
    - docker push registry.cn-beijing.aliyuncs.com/gitlab-demo/java-demo:$CI_PIPELINE_ID
deploy_k8s_job:
  image: registry.cn-beijing.aliyuncs.com/codepipeline/public-blueocean-codepipeline-slave-java:0.1-63b99a20
  stage: deploy_k8s
  tags:
    - k8s-test
  script:
    - mkdir -p /etc/deploy
    - echo $kube_config |base64 -d > $KUBECONFIG
    - sed -i "s/IMAGE_TAG/$CI_PIPELINE_ID/g" deployment.yaml
    - cat deployment.yaml
    - kubectl apply -f deployment.yaml
```

The .gitlab-ci.yml file defines a pipeline that is executed in three stages.

``` {#codeblock_zno_dv0_thn}
image: docker:stable  # If no image is specified for each step of the pipeline, the docker:stable image is used by default.
stages:
  - package                # package the soucre code
  - docker_build         # build, package, and push the application image
  - deploy_k8s           # deploy the application
variables:
  KUBECONFIG: /etc/deploy/config   # define the global variable, namely, KUBECONFIG
```

-   Package the maven source code

    ``` {#codeblock_qzz_lck_aq7}
    mvn_build_job:     # the job name
      image: registry.cn-beijing.aliyuncs.com/codepipeline/public-blueocean-codepipeline-slave-java:0.1-63b99a20  # the image used in this stage
      stage: package      # the stage name
      tags:                     # GitLab Runner tag
        - k8s-test
      script:
        - mvn package -B -DskipTests  # the script to execute the build
        - cp target/demo.war /opt/cache   # save the build to the cache
    ```

-   Build, package, and push the application image

    ``` {#codeblock_4rn_qm8_2xl}
    docker_build_job:  # the job name
      image: registry.cn-beijing.aliyuncs.com/codepipeline/public-blueocean-codepipeline-slave-java:0.1-63b99a20 # the image used in this stage
      stage: docker_build  # the stage name
      services:                  # Use the docker:dind service. You must set the runners.privileged in GitLab Runner Helm Chart to true.
        - docker:dind
      variables:
        DOCKER_DRIVER: overlay
        DOCKER_HOST: tcp://localhost:2375   # Connect the Docker Daemon.
      tags:                      # GitLab Runner tag
        - k8s-test
      script:
        - docker login -u REGISTRY_USERNAME -p $REGISTRY_PASSWORD registry.cn-beijing.aliyuncs.com   # log on to the registry
        - mkdir target
        - cp /opt/cache/demo.war target/demo.war
        - docker build -t registry.cn-beijing.aliyuncs.com/gitlab-demo/java-demo:$CI_PIPELINE_ID .     # Package the Docker image. The used tag is the pipeline ID.
        - docker push registry.cn-beijing.aliyuncs.com/gitlab-demo/java-demo:$CI_PIPELINE_ID      # Push the Docker image.
    ```

-   Deploy the application

    ``` {#codeblock_asn_mtr_d7n}
    deploy_k8s_job:   # the job name
      image: registry.cn-beijing.aliyuncs.com/codepipeline/public-blueocean-codepipeline-slave-java:0.1-63b99a20   # the image used in this stage
      stage: deploy_k8s   # the stage name
      tags:                      # GitLab Runner tag
        - k8s-test
      script:
        - mkdir -p /etc/deploy
        - echo $kube_config |base64 -d > $KUBECONFIG   # Set the config file that is used to connect the Kubernetes cluster.
        - sed -i "s/IMAGE_TAG/$CI_PIPELINE_ID/g" deployment.yaml  # Dynamically replace the image tag of the deployment file.
        - kubectl apply -f deployment.yaml
    ```


## Activate the pipeline {#section_y1z_ysp_qgb .section}

After you submit the .gitlab-ci.yml file, the gitlab-java-demo project automatically detects this file and activate the pipeline.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/123018/156161404238542_en-US.png)

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/123018/156161404338543_en-US.png)

## Access the application service {#section_hjr_1tp_qgb .section}

If no namespace is specified in the deployment file, the application is deployed in the GitLab namespace by default.

``` {#codeblock_8t2_cuf_lzt}
$ kubectl -n gitlab get svc 
NAME        TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
java-demo   LoadBalancer   172.19.9.252   xx.xx.xx.xx   80:32349/TCP   1m
```

Visit xx.xx.xx.xx/demo in your browser to check the result.

For more information, see [Container Service](https://www.alibabacloud.com/product/container-service) and [GitLab CI](https://docs.gitlab.com/runner/).

