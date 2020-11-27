# Use Bamboo to deploy a Bamboo agent in an ACK cluster and run a build plan

This topic describes how to use Bamboo to deploy a remote agent in a cluster of Container Service for Kubernetes \(ACK\) and run a build plan for an application. In this topic, a sample application compiled in Java is created and deployed in an ACK cluster.

## Prerequisites

-   An ACK cluster is created. For more information, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).
-   A Bamboo server is created.

## Source code

In this example, the address of the GitHub project is:

```
https://github.com/AliyunContainerService/jenkins-demo.git
```

The source code under the `bamboo` branch is used.

## Step 1: Deploy a remote agent in an ACK cluster

1.  Create a Secret named kaniko-docker-cfg.

    The kaniko-docker-cfg Secret is used to authenticate access to an image registry when you run a build plan and push an image to the image registry.

    1.  Log on to your Linux server as a **root** user and run the following command to generate the /root/.docker/config.json file:

        ```
        docker login registry.cn-hangzhou.aliyuncs.com
        ```

    2.  Use [Cloud Shell](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl on Cloud Shell to manage ACK clusters.md) to connect to the created ACK cluster. Run the following command to create the kaniko-docker-cfg Secret:

        ```
        kubectl -n bamboo create secret generic kaniko-docker-cfg  --from-file=/root/.docker/config.json
        ```

2.  Deploy the Bamboo agent.

    Create a service account and ClusterRoleBinding to configure permissions of the application in the ACK cluster.

    1.  Create a YAML file named bamboo-agent.yaml and paste the following content to the file. Run the `kubectl -n bamboo apply -f bamboo-agent.yaml` command to deploy the Bamboo agent.

        ```
        apiVersion: v1
        kind: ServiceAccount
        metadata:
          namespace: bamboo
          name: bamboo
        
        ---
        apiVersion: rbac.authorization.k8s.io/v1
        kind: ClusterRoleBinding
        metadata:
          name: bamboo-cluster-admin
        subjects:
          - kind: ServiceAccount
            name: bamboo
            namespace: bamboo
        roleRef:
          kind: ClusterRole
          name: cluster-admin
          apiGroup: rbac.authorization.k8s.io
        
        ---
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: bamboo-agent
        spec:
          replicas: 1
          selector:
            matchLabels:
              app: bamboo-agent
          template:
            metadata:
              labels:
                app: bamboo-agent
            spec:
              serviceAccountName: bamboo
              containers:
              - name: bamboo-agent
                env:
                - name: BAMBOO_SERVER_URL
                  value: http://xx.xx.xx.xx:8085
                image: registry.cn-hangzhou.aliyuncs.com/haoshuwei/docker-bamboo-agent:v1
                imagePullPolicy: Always
                volumeMounts:
                  - mountPath: /root/.docker/
                    name: kaniko-docker-cfg
              volumes:
                - name: kaniko-docker-cfg
                  secret:
                    secretName: kaniko-docker-cfg
        ```

    2.  After the Bamboo agent is deployed, run the following command to query log data of the application:

        ```
        kubectl -n bamboo logs -f <bamboo agent pod name>
        ```

        **Note:** You must replace bamboo agent pod name with the actual pod name.

    3.  After the remote agent is registered, log on to the [Bamboo](https://confluence.atlassian.com/get-started-with-bitbucket/get-started-with-bitbucket-cloud-856845168.html) console. In the upper-right corner of the console, click ![Settings](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6346858951/p48938.png) and select **Agent** from the drop-down list.

        On the Bamboo administration page, you can find the deployed agent.

        ![Bamboo administration](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6346858951/p49070.png)


## Step 2: Configure a build plan

You can configure a build plan to pull the source code, compile and package the source code, package and push the container image, and deploy the application.

1.  Create a build plan.

    1.  Log on to the [Bamboo](https://confluence.atlassian.com/get-started-with-bitbucket/get-started-with-bitbucket-cloud-856845168.html) console, choose **Create** \> **Create plan**. The Configure plan page appears.

    2.  Select bamboo-ack-demo from the **Project** drop-down list. Set Plan name, Plan key, and Plan description. Select java-demo from the Repository host drop-down list. Then, click **Configure plan**.

        ![Configure plan](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7346858951/p49094.png)

2.  The Configure Job page appears. On the Configure Job page, configure the stages and add jobs.

    1.  Pull the source code.

        When you create the build plan, you have selected java-demo from the Repository host drop-down list. This means that you have configured where to pull the source code. If you want to change the code repository, perform the following steps.

        1.  In the **Create tasks** section, click **Source Code Checkout**.

            ![Source Code Checkout](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7346858951/p49095.png)

        2.  On the Source Code Checkout configuration panel, modify Repository host based on your requirements and click **Save**.
    2.  Use Maven to package the source code.

        1.  In the **Create tasks** section, click **Add task**. In the Task types dialog box, select **Command**.

            ![Task types](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7346858951/p49096.png)

        2.  On the Command configuration panel, set **Task description**, **Executable**, and **Argument**. Click **Save**.

            ![Task description](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7346858951/p49097.png)

    3.  Use kaniko to package and push the container image to the image registry.

        1.  In the **Create tasks** section, click **Add task**. In the Task types dialog box, select **Script**.

            ![Script](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7346858951/p49098.png)

        2.  On the Script configuration panel, specify **Task description** and **Script location**. You can keep the default settings for the other parameters. Click **Save**.

            ![Script configuration](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7346858951/p49099.png)

            In this example, **Script location** is specified as:

            ```
            kaniko -f `pwd`/Dockerfile -c `pwd` --destination=registry.cn-hangzhou.aliyuncs.com/haoshuwei/bamboo-java-demo:latest
            ```

    4.  Use kubectl to deploy the application in the ACK cluster.

        1.  In the **Create tasks** section, click **Add task**. In the Task types dialog box, select **Script**.

            ![Script](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7346858951/p49098.png)

        2.  On the Script configuration panel, set **Task description** and **Script location**. You can keep the default settings for the other parameters. Click **Save**.

            ![Script configuration](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7346858951/p49100.png)

            In this example, **Script location** is specified as:

            ```
            sed -i 's#IMAGE_URL#registry.cn-hangzhou.aliyuncs.com/haoshuwei/bamboo-java-demo:latest#' . /*.yaml
            kubectl apply -f . /
            ```

3.  Run the build plan.

    1.  After you have completed the preceding steps, click **Create**. The bamboo-ack-demo page appears.

        ![bamboo-ack-demo](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7346858951/p49101.png)

    2.  In the upper-right corner of the bamboo-ack-demo page, choose **Run** \> **Run plan** to run the newly created build plan.

        ![Run plan](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8346858951/p49102.png)

        You can also click the **Logs** tab to view the log of the build plan.

        ![Logs](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8346858951/p49103.png)

4.  Access the deployed application.

    1.  Run the `kubectl -n bamboo get svc` command to query information about the Service for the deployed application.

        ```
        [root@iZbp12i73koztp1cz75skaZ bamboo]# kubectl -n bamboo get svc
        NAME                TYPE           CLUSTER-IP      EXTERNAL-IP       PORT(S)        AGE
        jenkins-java-demo   LoadBalancer   xx.xx.xx.xx     xx.xx.xx.xx       80:32668/TCP   39m
        ```

    2.  You can enter http://EXTERNAL-IP into the address bar of your browser and press Enter to access the deployed application.

        ![URL](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8346858951/p49104.png)


## Related topic

For more information about how to build registry.cn-hangzhou.aliyuncs.com/haoshuwei/docker-bamboo-agent:v1, see [docker-bamboo-agent](https://github.com/haoshuwei/docker-bamboo-agent).

For more information about Bamboo, see [Bamboo](https://confluence.atlassian.com/get-started-with-bitbucket/get-started-with-bitbucket-cloud-856845168.html).

