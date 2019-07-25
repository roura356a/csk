# 接入外部Kubernetes集群 {#task_skz_qwk_qfb .task}

您可以通过容器服务控制台接入现有的外部Kubernetes集群，并通过容器服务控制台进行管理。

-   您需要开通容器服务、弹性伸缩（ESS）服务和访问控制（RAM）服务。

    登录 [容器服务管理控制台](https://cs.console.aliyun.com/) 、 [RAM 管理控制台](https://ram.console.aliyun.com/) 和 [弹性伸缩控制台](https://essnew.console.aliyun.com) 开通相应的服务。

-   修改Kubernetes集群仍然必须在原有的集群中进行完成，包括添加与删除节点、升级Kubernetes集群版本以及更改Kubernetes组件参数等。
-   该功能目前只针对白名单客户开放。

1.  创建接入集群。 
2.  将目标集群接入新集群中。 
    1.  在新集群（本例中为**test-external-cluster1**）右侧单击**管理**，进入**基本信息**页面。
    2.  在**集群接入代理配置**区域单击**复制**，将以下yaml文件内容拷贝到agent.yaml文件中，并执行`kubectl apply -f agent.yaml`命令。 

        ![导入配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/156402368548732_zh-CN.png)

    3.  在目标集群中执行`kubectl get all -n ack-system`命令，查看代理运行状况。 

        ``` {#codeblock_57v_51x_t4y}
        NAME                                     READY   STATUS    RESTARTS   AGE
        pod/ack-cluster-agent-655b75c987-dwp6b   1/1     Running   0          9s
        
        NAME                                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
        deployment.apps/ack-cluster-agent   1         1         1            1           26m
        
        NAME                                           DESIRED   CURRENT   READY   AGE
        replicaset.apps/ack-cluster-agent-655b75c987   1         1         1       26m
        ```

        接入成功后，您可以在容器服务管理控制台的 Kubernetes 集群列表页面，看到新集群的状态为运行中。

        ![集群列表2](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/156402368552710_zh-CN.png)


在集群列表页面中，找到新集群**test-external-cluster1**，单击操作列中的**管理**，查看新集群的基本信息和连接信息。

![基本信息](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/156402368552922_zh-CN.png)

您可以[通过 kubectl 连接 Kubernetes 集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)，执行`kubectl get node`查看新集群**test-external-cluster1**的节点信息。此时，您可以使用该kubeconfig连接远程的被接入集群，进行应用负载的部署。

![pod](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/156402368648729_zh-CN.png)

您也可以在该接入的集群中通过使用Helm来发布管理应用。

![helm](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/156402368652712_zh-CN.png)

