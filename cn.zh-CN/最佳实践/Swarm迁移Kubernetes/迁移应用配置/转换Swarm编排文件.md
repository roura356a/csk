# 转换Swarm编排文件

本文介绍如何转换Swarm编排文件。

1.  使用kompose工具转换文件。

    在对Swarm编排文件预处理之后，接下来可以用kompose工具做转换，操作命令如下。

    ```
    kompose-linux-amd64 convert -f source/swarm-piggymetrics.yaml --volumes PersistentVolumeClaimOrHostPath
    ```

    ![kompose](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1073659951/p47978.png)

    **说明：** 正常情况下，会生成对应Kubernetes资源文件，针对不支持自动转换的标签，会有WARN提示。这部分标签请按后面的转换异常进行处理，其一般会有以下3种处理方式：

    -   手动调整原Swarm编排文件，然后重新通过kompose转换。
    -   手动修改kompose工具转换Kubernetes资源文件。
    -   暂时忽略，先部署转换好的Kubernetes资源文件，再通过容器服务控制台手动迁移配置。
2.  手动修复转换问题。

    kompose工具支持转换大部分标签，但少量Swarm标签无法支持自动转换（后续我们会进一步增强），需要我们根据kompose工具输出的警告信息，手动调整Swarm编排文件并重新用kompose工具转换；具体各类异常信息及对应解决方案参见[应用配置迁移异常解决方案](/cn.zh-CN/最佳实践/Swarm迁移Kubernetes/迁移应用配置/应用配置迁移异常解决方案.md)。

3.  手动完善Kubernetes资源文件。

    在通过kompose工具转换之后，部分kompose无法支持的标签，需要我们手动编写或完善Kubernetes资源文件。主要包括以下几类标签：

    -   aliyun.routing.port\_
    -   aliyun.global
    -   external
    -   environment: constraint
    -   extra\_hosts
    -   net
    -   dns
    针对每个标签的详细操作请参见[配置类标签](/cn.zh-CN/最佳实践/Swarm迁移Kubernetes/附录：标签映射/配置类标签.md)、[发布类标签](/cn.zh-CN/最佳实践/Swarm迁移Kubernetes/附录：标签映射/发布类标签.md)、[网络配置类标签](/cn.zh-CN/最佳实践/Swarm迁移Kubernetes/附录：标签映射/网络配置类标签.md)、[日志配置类标签](/cn.zh-CN/最佳实践/Swarm迁移Kubernetes/附录：标签映射/日志配置类标签.md)。


