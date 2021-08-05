---
keyword: [应用中心, Git源仓库, 创建应用]
---

# 使用Git源仓库创建应用

通过ACK的应用中心创建应用可以直观地查看所有Kubernetes资源的部署状态与变化。根据数据源的来源不同，创建应用的方式也不同。本文介绍如何使用Git源仓库在应用中心创建应用。

已创建Kubernetes集群。具体操作，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。

## 示例仓库地址

[GitHub仓库地址](https://github.com/AliyunContainerService/appcenter-samples.git)

## Git源仓库中应用编排方式

-   Kubernetes原生的manifests编排

    ```
    .
    ├── deployment.yaml
    ├── ingress.yaml
    └── service.yaml
    ```

-   Helm编排

    ```
    .
    ├── Chart.yaml
    ├── templates
    │   ├── deployment.yaml
    │   ├── _helpers.tpl
    │   ├── ingress.yaml
    │   ├── NOTES.txt
    │   ├── serviceaccount.yaml
    │   ├── service.yaml
    │   └── tests
    │       └── test-connection.yaml
    ├── values-pro.yaml
    └── values.yaml
    ```

-   Kustomize编排

    ```
    .
    ├── base
    │   ├── deployment.yaml
    │   ├── ingress.yaml
    │   ├── kustomization.yaml
    │   └── service.yaml
    └── overlays
        ├── pre
        │   ├── deployment.yaml
        │   └── kustomization.yaml
        └── pro
            ├── deployment.yaml
            └── kustomization.yaml
    ```


## 操作步骤

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**多集群** \> **应用中心**。

3.  在应用中心页面单击**创建应用**。

4.  在**应用来源**配置向导配置参数。

    1.  在**安装应用发布组件**区域选择集群，然后单击**开始安装**。

        **说明：** 仅首次安装应用需要执行此步骤。

    2.  在**应用来源**区域单击**Git仓库**。

    3.  单击**下一步**。

5.  在**应用配置**配置向导中设置参数，然后单击**下一步**。

    |参数|说明|
    |--|--|
    |应用名称|设置应用名称。|
    |应用描述|设置应用描述。|
    |仓库|选择仓库，您可以选择已创建的仓库或者使用新仓库。本文以使用新仓库为例。|
    |仓库地址|设置仓库的地址。|
    |名称|设置仓库的名称。|
    |连接方式|选择连接方式，支持**HTTPS**和**SSH**。以下为不同连接方式需要设置的参数：    -   当设置连接方式为**HTTPS**，需要设置以下参数。
        -   **用户名（可选）**：如果是私有Git仓库，需要设置用户名。
        -   **密码（可选）**：如果是私有Git仓库，需要设置密码。
        -   **TLS客户端证书（可选）**：如果Git服务器使用的是自签发证书，需要设置TLS客户端证书。
        -   **TLS客户端证书密钥（可选）**：如果Git服务器使用的是自签发证书，需要设置TLS客户端证书密钥。
        -   **跳过服务端证书校验**：如果Git服务器使用的是自签发证书，且想要省略TLS客户端证书和TLS客户端证书密钥的配置，则可以选中**跳过服务端证书校验**。
        -   **LFS支持**：是否需要配置Git启用LFS支持。
    -   当设置连接方式为**SSH**，需要设置以下参数。

**SSH私钥**：设置SSH私钥。 |
    |分支|分支名称。|
    |路径|Git源仓库中的应用编排所在的子目录。|

6.  在**发布配置**配置向导页面中设置参数。

    1.  在**发布配置**区域选择发布方式，可选**全量发布**和**灰度发布**。

        **说明：** 第一次发布应用时只能选择**全量发布**，后续对应用进行更新迭代时，可以选择**全量发布**或**灰度发布**。

    2.  在**可选集群**区域单击集群左侧的**添加**后，在**目标集群**区域可以看到选择的集群，然后在**目标集群**区域选择应用要发布的**命名空间**。

        **说明：** 如果没有开通公网访问的集群，需要为API Server绑定EIP，以开启公网访问能力。

    3.  单击**开始发布**。

7.  在**发布确认**对话框单击**确定**。

    在**发布日志**配置页面中可以看到发布应用的进度。


**相关文档**  


[应用中心概述](/intl.zh-CN/Kubernetes集群用户指南/应用中心/应用中心概述.md)

[管理应用](/intl.zh-CN/Kubernetes集群用户指南/应用中心/应用管理/管理应用.md)

[使用编排模板创建应用](/intl.zh-CN/Kubernetes集群用户指南/应用中心/应用管理/使用编排模板创建应用.md)

