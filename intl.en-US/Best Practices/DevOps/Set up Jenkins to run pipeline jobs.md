# Set up Jenkins to run pipeline jobs

This topic describes how to build a continuous integration environment by using Jenkins in a Container Service for Kubernetes \(ACK\) cluster. This topic also provides examples on how to build an application delivery pipeline that consists of source code compilation, image building and pushing, and application deployment.

## Prerequisites

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) is installed.

## Step 1: Deploy Jenkins

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**. Select **ack-jenkins**.

3.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

4.  On the **Alibaba Cloud Apps** tab, select **ack-jenkins**.

5.  Click the **Parameters** tab.

6.  In the Deploy section, select an ACK cluster from the **Cluster** drop-down list and a namespace from the **Namespace** drop-down list. Then, enter a release name in the **Release Name** field.

    ![Namespace](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0346858951/p38449.png)

    **Note:** We recommend that you select a custom namespace or the default namespace. A custom namespace named ci is selected in this example.

7.  On the Parameters tab, modify the value in the `AdminPassword` field. Then, click **Create** in the Deploy section.

    **Note:** To ensure that the password takes effect, delete the number sign \(`#`\) before `AdminPassword`.

    If you do not specify a password by modifying `AdminPassword`, the system automatically generates a password after Jenkins is deployed. You can run the following command to query the password:

    ```
    printf $(kubectl get secret --namespace ci jenkins-ci-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo
    ```

8.  In the left-side navigation pane, click **Clusters**.

9.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

10. In the left-side navigation pane of the details page, choose **Services and Ingresses** \> **Services**.

11. Select the namespace in which Jenkins is deployed. Then, click **External Endpoint** next to the ack-jenkins-default Service to log on to Jenkins.


## Step 2: Create a cluster certificate and an image repository certificate, and build and deploy an application

1.  Create a Kubernetes cluster certificate.

    1.  In the left-side navigation pane of the Jenkins console, click **Manage Jenkins**.

    2.  On the right side of the Manage Jenkins page, click **Manage Nodes and Clouds**.

    3.  In the left-side navigation pane of Nodes, click **Configure Clouds**.

    4.  On the Configure Clouds page, find **Credentials**. On the right side of the Credentials field, choose **Add** \> **Jenkins**.

        ![Credentials](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1315514161/p38458.png)

        Before you add a credential, go to the Cluster Information page. On the **Connection Information** tab, find **KubeConfig** in the **Configure the cluster credentials** field. For more information, see [Configure the cluster credential](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).

        ![Configure a cluster credential](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0346858951/p38453.png)

    5.  In the Add Credentials dialog box, set the following parameters:

        ![Add a credential](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1346858951/p38526.png)

        -   **Kind**: Select **Docker Host Certificate Authentication**.
        -   **Client Key**: Enter the value of client-key-data obtained from KubeConfig.
        -   **Client Certificate**: Enter the value of client-certificate-data obtained from KubeConfig.
        -   **Server CA Certificate**: Ignore this parameter.
        -   **ID**: Enter the certificate ID. k8sCertAuth is entered in this example.
        -   **Description**: Enter a description.
    6.  Click **Add**.

    7.  In the **Credentials** drop-down list, select the credential that you added. Then, click **Test Connection** to verify the connectivity.

    8.  The following figure shows the configurations of the Kubernetes cluster that dynamically schedules build pods.

        ![Build pods](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1315514161/p38460.png)

    9.  Configure a Kubernetes pod template.

        The slave-pipeline uses four containers to build stages in the pipeline.

        -   The following figure shows the configurations of the jnlp container.

            ![Container jnlp](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1346858951/p38461.png)

            Create a Docker container from the jenkins-slave-jnlp image. The jnlp container is used to connect the Jenkins slave node and the master node through Java Network Launching Protocol \(JNLP\).

            ```
            registry.cn-beijing.aliyuncs.com/acs-sample/jenkins-slave-jnlp:3.14-1
            ```

        -   The following figure shows the configurations of the kaniko container.

            ![Container kaniko](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1346858951/p38462.png)

            Create a Docker container from the jenkins-slave-kaniko image. The kaniko container is used to build and push application images.

            ```
            registry.cn-beijing.aliyuncs.com/acs-sample/jenkins-slave-kaniko:0.6.0
            ```

        -   The following figure shows the configurations of the kubectl container.

            ![Container kubectl](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1346858951/p38463.png)

            Create a Docker container from the jenkins-slave-kubectl image. The kubectl container is used to deploy applications in the kubectl container.

            ```
            registry.cn-beijing.aliyuncs.com/acs-sample/jenkins-slave-kubectl:1.11.5
            ```

        -   The following figure shows the configurations of the maven container.

            ![Container maven](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1346858951/p38465.png)

            Create a Docker container from the jenkins-slave-maven image. The maven container is used to package builds by using Maven.

            ```
            registry.cn-beijing.aliyuncs.com/acs-sample/jenkins-slave-maven:3.3.9-jdk-8-alpine
            ```

    10. Specify permissions of the kaniko container on the image repository. Configure the environment variables and Secret volume based on your requirements. The following figure shows an example.

        ![Specify the permissions of the kaniko container on the image repository](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1346858951/p38466.png)

    11. Click **Save**.

2.  Create a Secret in a Kubernetes cluster by using kubectl. The Secret specifies permissions on the image repository.

    1.  Log on to the image repository.

        An image repository provided by Alibaba Cloud Container Registry in the China \(Beijing\) region is used in this example.

        ```
        docker login -u username of the image repository -p password registry.cn-beijing.aliyuncs.com
        ```

    2.  Run the following command to create a Secret named jenkins-docker-cfg:

        ```
        kubectl create secret generic jenkins-docker-cfg -n ci --from-file=/root/.docker/config.json
        ```

3.  Build demo-pipeline and access the application.

    1.  On the Jenkins homepage, click **demo-pipeline**.

        ![demo-pipeline](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1616059161/p38467.png)

    2.  In the left-side navigation pane, click **Build with Parameters**.

    3.  Modify the build parameters based on the information of your image repository. In this example, the branch of the source code repository is master and the image is registry.cn-beijing.aliyuncs.com/ack-cicd/ack-jenkins-demo:latest.

        ![Build parameters](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2315514161/p38468.png)

    4.  Click **Build**.

    5.  View **Build History**. The following figure indicates that demo-pipeline is built.

        ![Build History](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2346858951/p38469.png)


## Details of building demo-pipeline

Building `demo-pipeline` includes the following steps: retrieve the source code, build an application package, package the container and push the container image, and deploy the application to the Kubernetes cluster.

The pipeline is executed based on the content declared in the Jenkinsfile of the sample project. For more information, see [Jenkinsfile sample project](https://github.com/AliyunContainerService/jenkins-demo/blob/master/Jenkinsfile).

**configurations of demo-pipeline**

Four build parameters are set in demo-pipeline. The following content describes the parameters:

-   origin\_repo, repo, and image\_tag are used to specify the path of the container image. For example, if the container image of the sample application is pushed to registry.cn-beijing.aliyuncs.com/ack-cicd/ack-jenkins-demo:latest, set origin\_repo to registry.cn-beijing.aliyuncs.com/haoshuwei/ack-cicd, repo to ack-cicd, and image\_tag to latest.
-   The branch parameter is used to specify the branch of the source code repository.

![Build parameters for the image repository](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2920719161/p238055.png)

On the Jenkins homepage, click **demo-pipeline**. In the left-side navigation pane, click **Configure**. On the **Pipeline** tab, you can retrieve the source code from a GitHub repository and specify the path of Jenkinsfile.

![Configure the pipeline](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2920719161/p238076.png)

**Jenkinsfile parsing**

Add the following content to Jenkinsfile in the source code:

```
pipeline{
      // Define the environment variables used in the groovy script.
      environment{
        // Transform the build parameters in the build to environment variables.
        // In this example, the DEPLOY\_TO\_K8S variable is used to determine the cluster environment where the application is deployed, such as Production Environment, Staging001 Environment.
        IMAGE_TAG =  sh(returnStdout: true,script: 'echo $image_tag').trim()
        ORIGIN_REPO =  sh(returnStdout: true,script: 'echo $origin_repo').trim()
        REPO =  sh(returnStdout: true,script: 'echo $repo').trim()
        BRANCH =  sh(returnStdout: true,script: 'echo $branch').trim()
      }
      // Specify the tag of the build environment. In this example, slave-pipeline is used.
      agent{
        node{
          label 'slave-pipeline'
        }
      }
      // The stages parameter defines multiple modules built by the project. You can add multiple stages and execute the stages in a row or in parallel.
      stages{
        // Add the first stage: copy the source code.
        stage('Git'){
          steps{
            git branch: '${BRANCH}', credentialsId: '', url: 'https://github.com/AliyunContainerService/jenkins-demo.git'
          }
        }
        // Add the second stage: package the source code.
        stage('Package'){
          steps{
              container("maven") {
                  sh "mvn package -B -DskipTests"
              }
          }
        }
        // Add the third stage: build and push the container image. The groovy environment variables defined in the environment are used.
        stage('Image Build And Publish'){
          steps{
              container("kaniko") {
                  sh "kaniko -f `pwd`/Dockerfile -c `pwd` --destination=${ORIGIN_REPO}/${REPO}:${IMAGE_TAG} --skip-tls-verify"
              }
          }
        }
        // Add the fourth stage: deploy the application to the specified Kubernetes cluster.
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

For more information about the code, see [jenkins-demo/Jenkinsfile](https://github.com/AliyunContainerService/jenkins-demo/blob/master/Jenkinsfile).

The following table describes the code blocks in the source code of the sample project.

|Code block|Purpose|Description|
|:---------|:------|-----------|
|`environment{}`|Retrieves the build parameters and transforms the parameters to environment variables used in Jenkinsfile.|N/A|
|`agent{}`|Specifies the node label of the build. The node label is specified as `slave-pipeline`.|In the left-side navigation pane of **Jenkins**, choose **Manage Jenkins** \> **Manage Nodes and Clouds** \> **Configure Clouds**. On the Configure Clouds page, you can view the configurations of the Kubernetes cluster. The configurations define how to dynamically create nodes of the build.

The following figure shows the configurations of the Kubernetes cluster that dynamically schedules build pods.

![Configure Clouds](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2920719161/p238347.png)

Create a slave pod named slave-pipeline in the Jenkins namespace. The slave pod contains four containers. The containers are used to build the stages in the pipeline. For more information, see [Details of the system configurations](#section_r50_b6c_4cl).

The kaniko container must be granted the permission to push the image to the repository before it can be used to build and push the container image. The permission is specified in the K8s Secret that is mounted to the pod of the slave-pipeline. The `DOCKER_CONFIG` environment variable defines the default path of the file which allows the kaniko container to push the image to the repository. Run the following command in Linux to create a Secret named `jenkins-docker-cfg`:

```
docker login -u <username\> -p <password\> registry.cn-beijing.aliyuncs.com
kubectl create secret generic jenkins-docker-cfg -n ci --from-file=/root/.docker/config.json
``` |
|`stages{}`|Defines multiple stages to complete continuous integration and deployment in several steps.|Retrieve the source code. ```
stage('Git'){
  steps{
    git branch: '${BRANCH}', credentialsId: '', url: 'https://github.com/AliyunContainerService/jenkins-demo.git'
  }
}
```

**Note:** The source code repository contains Jenkinsfile, Dockerfile, and deployment.yaml. |
|The maven container is used to compile and package the source code. ```
stage('Package'){
  steps{
    container("maven") {
      sh "mvn package -B -DskipTests"
    }
  }
}
```

**Note:** If the cache feature is required when the maven container compiles the source code, you must create a volume that uses an Apsara File Storage NAS \(NAS\) file system. Then, mount the volume to the template of the slave-pipeline pod in the Kubernetes cluster. |
|The kaniko container is used to build and deploy container images. ```
stage('Image Build And Publish'){
  steps{
    container("kaniko") {
      sh "kaniko -f `pwd`/Dockerfile -c `pwd` --destination=${ORIGIN_REPO}/${REPO}:${IMAGE_TAG} --skip-tls-verify"
    }
  }
}
```

**Note:** The kaniko container is not reliant on a Docker deamon process when it builds a container image. kaniko builds and pushes the container image in the user-space, which is more secure and reliable. |
|The kubectl container is used to deploy applications to the Kubernetes cluster. **Note:** Before kubectl deploys an application to the Kubernetes cluster, you must create a cluster certificate named k8sCertAuth.

```
stage('Deploy to Kubernetes') {
  steps {
    container('kubectl') {
      step([$class: 'KubernetesDeploy', authMethod: 'certs', apiServerUrl: 'https://kubernetes.default.svc.cluster.local:443', credentialsId:'k8sCertAuth', config: 'deployment.yaml',variableState: 'ORIGIN_REPO,REPO,IMAGE_TAG'])
      // The variables declared in the variableState field must be replaced with the environment variables in the deployment.yaml file.
}
  }
}
``` |

## Details of the system configurations

**Configurations of the Jenkins build environment**

-   The following figure shows the configurations of the Kubernetes cluster that dynamically schedules build pods.

    ![Pod configurations](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2920719161/p238625.png)

-   The slave-pipeline pod contains four containers that are used to build stages in the pipeline.
    -   The jnlp container is used to connect the slave pod to the master node. The following figure shows the configurations.

        ![Container jnlp](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2920719161/p238632.png)

        Create a Docker container from a custom image.

        ```
        jenkinsci/jnlp-slave:3.35-5
        ```

    -   The kaniko container is used to build and deploy the container image. The following figure shows the configurations.

        ![Container kaniko](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3920719161/p238782.png)

        Create a Docker container from an image provided by ACK.

        ```
        registry.cn-beijing.aliyuncs.com/acs/kaniko:v0.14.0
        ```

    -   The kubectl container is used to deploy applications to the Kubernetes cluster. The following figure shows the configurations.

        ![Container kubectl](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3920719161/p238827.png)

        Create a Docker container from an image provided by ACK.

        ```
        registry.cn-beijing.aliyuncs.com/acs/kubectl:1.14.8
        ```

    -   The maven container is used to compile and package the source code. The following figure shows the configurations.

        ![Conainer maven](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3920719161/p238838.png)

        Create a Docker container from a custom image.

        ```
        maven:3.6.2-jdk-14
        ```

-   Specify the permissions of the kaniko container on the image repository.

    ![Specify permissions on the image repository](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3920719161/p239206.png)


**Description of the sample project**

-   The source code repository used in the sample project:

    ```
    https://github.com/AliyunContainerService/jenkins-demo.git
    ```

-   Use kaniko to build and push the Docker image.

    kaniko is not reliant on a Docker daemon process and executes every command of the Dockerfile in user-space. After kaniko executes every command of the Dockerfile, it builds the Docker image and pushes the image to the registry.

    ```
    kaniko -f `pwd`/Dockerfile -c `pwd` --destination=${origin_repo}/${repo}:${image_tag}
    ```

-   Plug-ins that are used to deploy applications to the Kubernetes cluster.

    On the Jenkins homepage, click **demo-pipeline**. In the left-side navigation pane, click **Configure**. Click the **Build** tab.

    The following figure shows the configurations of the plug-ins.

    ![Deploy applications to the Kubernetes cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3920719161/p238991.png)

    The pipeline syntax:

    ```
    step([$class: 'KubernetesDeploy', authMethod: 'certs', apiServerUrl: 'https://kubernetes.default.svc.cluster.local:443', credentialsId:'k8sCertAuth', config: 'deployment.yaml',variableState: 'ORIGIN_REPO,REPO,IMAGE_TAG'])
    ```

    **Note:** When the build is executed, the variables declared in the ORIGIN\_REPO,REPO,IMAGE\_TAG field can replace the variables in the deployment.yaml file with the actual values.

-   Description of the Jenkinsfile.

    ```
    pipeline{
          // Define the environment variables used in the groovy script.
          environment{
            // In this example, the DEPLOY_TO_K8S variable is used to determine the cluster environment where the application is deployed, such as Production Environment, Staging001 Environment.
            IMAGE_TAG =  sh(returnStdout: true,script: 'echo $image_tag').trim()
            ORIGIN_REPO =  sh(returnStdout: true,script: 'echo $origin_repo').trim()
            REPO =  sh(returnStdout: true,script: 'echo $repo').trim()
            BRANCH =  sh(returnStdout: true,script: 'echo $branch').trim()
          }
          // Specify the tag of the build environment. In this example, slave-pipeline is used.
          agent{
            node{
              label 'slave-pipeline'
            }
          }
          // The stages parameter defines multiple modules built by the project. You can add multiple stages and execute the stages in a row or in parallel.
          stages{
            // Add the first stage: copy the source code.
            stage('Git'){
              steps{
                git branch: '${BRANCH}', credentialsId: '', url: 'https://github.com/AliyunContainerService/jenkins-demo.git'
              }
            }
            // Add the second stage: package the source code.
            stage('Package'){
              steps{
                  container("maven") {
                      sh "mvn package -B -DskipTests"
                  }
              }
            }
            // Add the third stage: build and push the container image. The groovy environment variable defined in the environment is used.
            stage('Image Build And Publish'){
              steps{
                  container("kaniko") {
                      sh "kaniko -f `pwd`/Dockerfile -c `pwd` --destination=${ORIGIN_REPO}/${REPO}:${IMAGE_TAG}"
                  }
              }
            }
             // Add the fourth stage: deploy the application to the specified Kubernetes cluster.
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


-   [Source code repository](https://github.com/AliyunContainerService/jenkins-demo.git)
-   [ACK](https://www.alibabacloud.com/product/kubernetes)
-   [kaniko](https://github.com/GoogleContainerTools/kaniko)

  


