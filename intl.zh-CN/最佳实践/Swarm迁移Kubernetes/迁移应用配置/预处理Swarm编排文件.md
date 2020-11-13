# 预处理Swarm编排文件

本文介绍如何预处理Swarm编排文件。

1.  下载Swarm应用编排文件。

    1.  登录[容器服务Swarm控制台](https://cs.console.aliyun.com)，在左侧导航栏单击**应用**，在目标应用右侧单击**变更配置**。

        ![变更配置](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/1073659951/p47976.png)

    2.  拷贝编排文件并保存到本地，文件名后缀为.yaml，本文命名为swarm-piggymetrics.yaml。

2.  预处理Swarm编排文件里面环境变量。

    Swarm编排文件支持配置项替换，是由阿里云Swarm控制台实现的功能，在Kubernetes里面没有与其对应的实体支持，需要在转换前，手动将编排文件里面的变量符*$*替换成该配置项真实值。具体的操作如下：

    1.  登录[容器服务Swarm控制台](https://cs.console.aliyun.com)，单击**配置项**，选择对应的地域，找到目标配置文件。

        ![配置项](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/1073659951/p47977.png)

    2.  参考该配置文件，手动将编排文件swarm-piggymetrics.yaml里面的变量符*$*替换成该配置项真实值。
3.  预处理部分标签。

    您需要预处理部分Swarm标签，否则kompose因格式或不支持等原因，会报错并中断整个转换过程。标签的预处理主要包含以下两个方面，具体针对每个标签的详细操作请参见[配置类标签](/intl.zh-CN/最佳实践/Swarm迁移Kubernetes/附录：标签映射/配置类标签.md)、[发布类标签](/intl.zh-CN/最佳实践/Swarm迁移Kubernetes/附录：标签映射/发布类标签.md)、[网络配置类标签](/intl.zh-CN/最佳实践/Swarm迁移Kubernetes/附录：标签映射/网络配置类标签.md)、[日志配置类标签](/intl.zh-CN/最佳实践/Swarm迁移Kubernetes/附录：标签映射/日志配置类标签.md)。

    -   以下标签的值需要将布尔型修改为字符串类型，如：true/false 修改为`"true"`/`"false"` 。
        -   aliyun.global
        -   aliyun.latest\_image
    -   包含以下标签的Service无法做迁移，需要先手动删除，后续再人工迁移。
        -   external
4.  修改编排文件版本号。

    因kompose工具目前主要支持V2版本的compose文件，所以需要将文件头的版本声明从version: '2.X' 修改为version: '2'，否则转换时会报错。


