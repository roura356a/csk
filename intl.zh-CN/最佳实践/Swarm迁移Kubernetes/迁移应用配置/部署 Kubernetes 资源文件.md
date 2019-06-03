# 部署 Kubernetes 资源文件 {#task_354580 .task}

针对前面kompose 转换成功并预处理后的Kubernetes 资源文件，我们可以通过kubectl工具部署到Kubernetes 集群中；其中，集群票据在[配置kubectl环境](intl.zh-CN/最佳实践/Swarm迁移Kubernetes/迁移应用配置/准备迁移环境.md#step_vtb_99h_e03) 已配置完成。

1.  可通过如下命令，批量部署当前目录下的所有资源文件。 

    ``` {#codeblock_6ii_1cz_ptb}
    kubectl create -f .            #通过文件名或标准输入创建资源，.表示基于当前目录下的资源文件创建
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/288311/155954530347979_zh-CN.png)

2.  登录[容器服务管理控制台](https://cs.console.aliyun.com)，在左侧导航栏选择**应用** \> **无状态**，查看应用部署情况。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/288311/155954530447980_zh-CN.png)

3.  在**路由与负载均衡**下，单击**路由**或者**服务**，查看服务及路由配置情况。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/288311/155954530447981_zh-CN.png)


