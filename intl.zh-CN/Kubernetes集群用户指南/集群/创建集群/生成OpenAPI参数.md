# 生成OpenAPI参数

创建集群时，如果由于请求参数组合不正确导致集群创建失败，您可以通过生成OpenAPI参数，为您创建集群时所请求的参数生成正确的参数组合。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击页面右上角的**集群模板**。

4.  在弹出的选择集群模板中，选择需要创建的集群类型，并单击**创建**。

    默认进入集群配置页面。配置信息，请参见[创建Kubernetes专有版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes专有版集群.md)。

5.  配置集群参数。

    您必须配置了以下参数，才能生成OpenAPI参数。

    -   如果是创建专有版Kubernetes集群，必选参数，请参见[创建Kubernetes专有版集群](/intl.zh-CN/API参考/集群/创建集群/创建Kubernetes专有版集群.md)。
    -   如果是创建托管版Kubernetes集群，必选参数，请参见[创建Kubernetes托管版集群](/intl.zh-CN/API参考/集群/创建集群/创建Kubernetes托管版集群.md)。
6.  配置完成后，单击**生成OpenAPI参数**。

    ![OpenAPI](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/0453686261/p297357.png)

    OpenAPI请求参数对话框中展示您创建集群时所请求的参数的组合。

    ![生成OpenAPI参数](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/9375659951/p64256.png)

7.  单击**复制**，您可以根据参数组合创建集群。

    创建集群时，您可以使用OpenAPI创建集群，请参见[创建Kubernetes专有版集群](/intl.zh-CN/API参考/集群/创建集群/创建Kubernetes专有版集群.md)和[创建Kubernetes托管版集群](/intl.zh-CN/API参考/集群/创建集群/创建Kubernetes托管版集群.md)。

    您也可以通过CLI创建集群，请参见[创建集群](/intl.zh-CN/CLI参考（旧版）/创建集群.md)。


