---
keyword: [Bamboo, Bamboo Agent, 构建任务]
---

# 使用Bamboo部署Bamboo Agent并执行构建任务

本文档以构建一个Java软件项目并部署到阿里云容器服务的Kubernetes集群为例，为您介绍如何使用Bamboo在阿里云Kubernetes服务上运行Remote Agents并在agents上运行Build Plans。

## 前提条件

-   您已经成功创建一个Kubernetes集群，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   您已经创建好一个Bamboo Server。

## 源码项目

本示例中创建的GitHub源码项目地址为如下：

```
https://github.com/AliyunContainerService/jenkins-demo.git
```

分支为`bamboo`。

## 步骤一、在Kubernetes中部署Remote Agent

1.  创建kaniko-docker-cfg secret。

    在Remote Agent上运行构建任务并且使用kaniko推送容器镜像时，kaniko-docker-cfg secret用于配置镜像仓库访问权限。

    1.  在Linux服务器上使用**root**用户执行如下命令，生成/root/.docker/config.json。

        ```
        docker login registry.cn-hangzhou.aliyuncs.com
        ```

    2.  通过[CloudShell](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/在CloudShell上通过kubectl管理Kubernetes集群.md)连接Kubernetes集群，并执行以下命令创建kaniko-docker-cfg secret。

        ```
        kubectl -n bamboo create secret generic kaniko-docker-cfg  --from-file=/root/.docker/config.json
        ```

2.  创建Bamboo Agent应用。

    创建serviceaccount bamboo以及clusterrolebinding，用于kubectl部署应用到kubernetes集群的权限设置。

    1.  创建并复制以下内容到bamboo-agent.yaml，并执行`kubectl -n bamboo apply -f bamboo-agent.yaml`命令，创建Bamboo Agent 应用。

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

    2.  完成后，执行以下命令查看日志。

        ```
        kubectl -n bamboo logs -f <bamboo agent pod name>
        ```

        **说明：** 示例中bamboo agent pod name需要替换成实际的文件名。

    3.  Remote Agent注册成功后，登录[Bamboo](https://confluence.atlassian.com/get-started-with-bitbucket/get-started-with-bitbucket-cloud-856845168.html)。单击右上角![设置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7663659951/p48938.png)，在下拉框中选择**Agent**。

        在Bamboo administration页面，可以看到部署好的Agent。

        ![Bamboo administration](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7663659951/p49070.png)


## 步骤二、配置一个Build plan

完成应用源码拉取、编译打包、容器镜像打包和推送、应用部署的过程。

1.  创建一个Build plan。

    1.  登录[Bamboo](https://confluence.atlassian.com/get-started-with-bitbucket/get-started-with-bitbucket-cloud-856845168.html)，选择**Create** \> **Create plan**，进入Configure plan页面。

    2.  **Project**选择bamboo-ack-demo，填写Plan name、Plan key和Plan description，Repository host选择java-demo，完成后单击**Configure plan**。

        ![Configure plan](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7663659951/p49094.png)

2.  进入Configure Job页面，配置Stages并添加Job。

    1.  源码拉取。

        在创建Build plan时，参数Repository host选择java-demo，即已完成源码拉取，您可以通过如下操作，修改源码拉取位置。

        1.  在**Create tasks**区域，单击**Source Code Checkout**。

            ![Source Code Checkout](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7663659951/p49095.png)

        2.  在右侧的Source Code Checkout configuration页面，修改参数Repository host的取值，单击**Save**。
    2.  mvn打包。

        1.  在**Create tasks**区域，单击**Add task**，在弹出的Task types页面选择**Command**。

            ![Task types](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8663659951/p49096.png)

        2.  在右侧弹出Command configuration页面，填写**Task description**、**Executable**和**Argument**后，单击**Save**。

            ![Task description](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8663659951/p49097.png)

    3.  kaniko打包和推送容器镜像。

        1.  在**Create tasks**区域，单击**Add task**，在弹出的Task types页面选择**Script**。

            ![Script](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8663659951/p49098.png)

        2.  在右侧弹出Script configuration页面，填写**Task description**、**Script location**后（其他参数保持默认值即可），单击**Save**。

            ![Script configuration](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8663659951/p49099.png)

            本示例中，**Script location**如下：

            ```
            kaniko -f `pwd`/Dockerfile -c `pwd` --destination=registry.cn-hangzhou.aliyuncs.com/haoshuwei/bamboo-java-demo:latest
            ```

    4.  使用Kubectl命令部署应用到Kubernetes。

        1.  在**Create tasks**区域，单击**Add task**，在弹出的Task types页面选择**Script**。

            ![Script](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8663659951/p49098.png)

        2.  在右侧弹出Script configuration页面，填写**Task description**、**Script location**后（其他参数保持默认值即可），单击**Save**。

            ![Script configuration](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8663659951/p49100.png)

            本示例中，**Script location**如下：

            ```
            sed -i 's#IMAGE_URL#registry.cn-hangzhou.aliyuncs.com/haoshuwei/bamboo-java-demo:latest#' ./*.yaml
            kubectl apply -f ./
            ```

3.  运行Build plan。

    1.  上述配置完成后，单击**Create**，进入bamboo-ack-demo页面。

        ![bamboo-ack-demo](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8663659951/p49101.png)

    2.  在右上角选择**Run** \> **Run plan**，运行Build plan。

        ![Run plan](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8663659951/p49102.png)

        您可以通过单击**Logs**页签查看运行日志。

        ![Logs](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8663659951/p49103.png)

4.  访问应用服务。

    1.  执行`kubectl -n bamboo get svc`命令，查看应用服务。

        ```
        [root@iZbp12i73koztp1cz75skaZ bamboo]# kubectl -n bamboo get svc
        ```

        预期输出：

        ```
        NAME                TYPE           CLUSTER-IP      EXTERNAL-IP       PORT(S)        AGE
        jenkins-java-demo   LoadBalancer   xx.xx.xx.xx     xx.xx.xx.xx       80:32668/TCP   39m
        ```

    2.  在浏览器中输入http://EXTERNAL-IP，访问应用。

        ![URL](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8663659951/p49104.png)


## 相关参考

查看本示例中镜像registry.cn-hangzhou.aliyuncs.com/haoshuwei/docker-bamboo-agent:v1的制作，请参见[docker-bamboo-agent](https://github.com/haoshuwei/docker-bamboo-agent)。

了解更多Bamboo的相关内容，请参见[Bamboo](https://confluence.atlassian.com/get-started-with-bitbucket/get-started-with-bitbucket-cloud-856845168.html)。

