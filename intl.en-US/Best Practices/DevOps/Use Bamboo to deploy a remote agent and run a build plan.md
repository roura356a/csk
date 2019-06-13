# Use Bamboo to deploy a remote agent and run a build plan {#concept_592892 .concept}

This topic describes how to use Bamboo to deploy a remote agent in a Kubernetes cluster implemented with Alibaba Cloud Container Service for Kubernetes \(ACK\) and how to use the agent to run a build plan for an application. In this topic, an example application complied in Java is created and deployed to a Kubernetes cluster.

## Prerequisites {#section_1el_o3y_ubr .section}

-   A Kubernetes cluster is created by using ACK. For more information, see [Create a Kubernetes cluster](../../../../intl.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   A Bamboo server is created.

## Source code of the application to be deployed {#section_dyl_992_k20 .section}

The source code of the application created in this topic can be obtained with the following address:

``` {#codeblock_edl_bpz_792}
https://github.com/AliyunContainerService/jenkins-demo.git
```

After you access the corresponding GitHub page, you can find the source code in the `bamboo` branch.

## Deploy a remote agent in a Kubernetes cluster {#section_5xp_sx6_p1q .section}

1.  Create a kaniko-docker-cfg secret.

    **Note:** This secret is used to set the permissions for accessing the target image repository required by building tasks in the remote agent and using kaniko to push a container image.

    1.  Log on to your Linux server by using the **root** account to run the following command to create a `/root/.docker/config.json` file:

        ``` {#codeblock_9lr_emx_37f}
        docker login registry.cn-hangzhou.aliyuncs.com
        ```

    2.  Use [Cloud Shell](../../../../intl.en-US/User Guide/Kubernetes cluster/Cluster management/Use kubectl commands in Cloud Shell to manage a Kubernetes cluster.md#) to connect to the target Kubernetes cluster, and then run the following command to create a kaniko-docker-cfg secret:

        ``` {#codeblock_2rk_ykt_4aq}
        kubectl -n bamboo create secret generic kaniko-docker-cfg  --from-file=/root/.docker/config.json
        ```

2.  Create a Bamboo agent in the target Kubernetes cluster.

    `ServiceAccount` and `ClusterRoleBinding` are created to set the permissions required for kubectl to deploy an application to the target Kubernetes cluster.

    1.  Create a file bamboo-agent.yaml and copy the following code to the file:

        ``` {#codeblock_h5j_lnd_hny}
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
        apiVersion: apps/v1beta2
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

    2.  Run the kubectl -n bamboo apply -f bamboo-agent.yaml command to create the Bamboo agent.
    3.  Run the following command to view logs of the agent:

        ``` {#codeblock_840_h4f_3r9}
        kubectl -n bamboo logs -f <bamboo agent pod name>
        ```

        **Note:** You must replace `<bamboo agent pod name>` with the file name that you use.

    4.  Log on to the Bamboo server you created. Then, in the upper-right corner, click ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/475357/156041079148938_en-US.png), and select **Agent** to view the deployed agent.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/475357/156041079249070_en-US.png)


## Configure a build plan {#section_eh6_dnv_a2g .section}

1.  Create a build plan.
    1.  Log on to the Bamboo server you created, and choose **Create** \> **Create plan**.
    2.  Select bamboo-ack-demo from the **Project** drop-down list, set **Plan name**, **Plan key**, and **Plan description**, select java-demo from the **Repository host** drop-down list, and then click **Configure plan**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/475357/156041079249094_en-US.png)

2.  Configure a job that contains four required tasks for the build plan.
    1.  Confirm and save the setting of the source code repository.

        **Note:** In the preceding step where you create the build plan, your setting for **Repository host** specifies the source code repository. You can retain or modify this setting.

        1.  In the **Create tasks** area, click **Source Code Checkout**.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/475357/156041079249095_en-US.png)

        2.  On the Source Code Checkout configuration page, select a new repository from the **Repository** drop-down list, and then click **Save**.
    2.  Add the command type of mvn to use the mvn tool.
        1.  In the **Create tasks** area, click **Add task**. Then, on the displayed Task types page, click **Command**.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/475357/156041079249096_en-US.png)

        2.  On the Command configuration page, set **Task description**, **Executable**, and **Argument**, and then click **Save**.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/475357/156041079349097_en-US.png)

    3.  Use kaniko to package and push the required container image to the target image repository.
        1.  In the **Create tasks** area, click **Add task**. Then, on the displayed Task types page, click **Script**.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/475357/156041079349098_en-US.png)

        2.  On the Script configuration page, set **Task description**, and **Script location**, then click **Save**.

            **Note:** For the remaining parameters of the script, you can retain the default settings.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/475357/156041079349099_en-US.png)

            In this example, the **Script location** is set as follows:

            ``` {#codeblock_7v2_k23_krm}
            kaniko -f `pwd`/Dockerfile -c `pwd` --destination=registry.cn-hangzhou.aliyuncs.com/haoshuwei/bamboo-java-demo:latest
            ```

    4.  Use kubectl to deploy the application on the target Kubernetes cluster.
        1.  In the **Create tasks** area, click **Add task**. Then, on the displayed Task types page, click **Script**.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/475357/156041079349098_en-US.png)

        2.  On the Script configuration page, set **Task description** and **Script location**, then click **Save**.

            **Note:** For the remaining parameters of the script, you can retain the default settings.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/475357/156041079449100_en-US.png)

            In this example, the **Script location** is set as follows:

            ``` {#codeblock_wvq_pub_p6t}
            sed -i 's#IMAGE_URL#registry.cn-hangzhou.aliyuncs.com/haoshuwei/bamboo-java-demo:latest#' ./*.yaml
            kubectl apply -f ./
            ```

3.  Run the build plan.
    1.  On the Configure Job page, click **Create**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/475357/156041079449101_en-US.png)

    2.  In the upper-right corner of the page, choose **Run** \> **Run plan**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/475357/156041079449102_en-US.png)

        You can click the **Logs** tab to view the logs.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/475357/156041079549103_en-US.png)

4.  Access the deployed application.
    1.  Run the kubectl -n bamboo get svc command to view the Internet IP address of the application.

        ``` {#codeblock_5qo_ubp_ipn}
        [root@iZbp12i73koztp1cz75skaZ bamboo]# kubectl -n bamboo get svc
        NAME                TYPE           CLUSTER-IP      EXTERNAL-IP       PORT(S)        AGE
        jenkins-java-demo   LoadBalancer   xx.xx.xx.xx     xx.xx.xx.xx       80:32668/TCP   39m
        ```

    2.  In your browser, enter http://EXTERNAL-IP to access the application.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/475357/156041079549104_en-US.png)


## Reference {#section_5sh_fsy_h58 .section}

For information about how to create the `registry.cn-hangzhou.aliyuncs.com/haoshuwei/docker-bamboo-agent:v1` image, see [Docker bamboo agent](https://github.com/haoshuwei/docker-bamboo-agent.git).

For more information, see [Bamboo](https://confluence.atlassian.com/get-started-with-bitbucket/get-started-with-bitbucket-cloud-856845168.html).

