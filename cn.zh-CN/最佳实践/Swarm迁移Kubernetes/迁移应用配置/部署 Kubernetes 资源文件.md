# 部署 Kubernetes 资源文件

本文介绍如何部署 Kubernetes 资源文件。

针对前面 kompose 转换成功并预处理后的 Kubernetes 资源文件，我们可以通过 kubectl 工具部署到 Kubernetes 集群中。其中，集群票据在[配置 kubectl 环境](/cn.zh-CN/最佳实践/Swarm迁移Kubernetes/迁移应用配置/准备迁移环境.md)已配置完成。

1.  可通过如下命令，批量部署当前目录下的所有资源文件。

    ```
    kubectl create -f .            #通过文件名或标准输入创建资源，.表示基于当前目录下的资源文件创建
    ```

    ![资源文件](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2073659951/p47979.png)

2.  登录[容器服务管理控制台](https://cs.console.aliyun.com)，在左侧导航栏选择**应用** \> **无状态**，查看应用部署情况。

    ![部署情况](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2073659951/p47980.png)

3.  在**路由与负载均衡**下，单击**路由**或者**服务**，查看服务及路由配置情况。

    ![配置情况](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3073659951/p47981.png)


