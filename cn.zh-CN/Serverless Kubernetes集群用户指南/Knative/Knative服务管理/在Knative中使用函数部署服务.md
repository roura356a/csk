---
keyword: [函数部署服务, Knative]
---

# 在Knative中使用函数部署服务

将Knative与函数计算（FC）结合，使用函数部署服务，支持根据代码、OSS文件和镜像部署服务。本文介绍如何在Knative中使用函数部署服务。

-   [创建Serverless Kubernetes集群](/cn.zh-CN/Serverless Kubernetes集群用户指南/快速入门/创建Serverless Kubernetes集群.md)
-   [开启Knative](/cn.zh-CN/Serverless Kubernetes集群用户指南/Knative/Knative组件管理/开启Knative.md)

## 使用代码部署服务

1.  创建coffee.yaml。

    ```
    apiVersion: serving.knative.dev/v1
    kind: Service
    metadata:
      name: coffee
      annotations:
        workload.serving.knative.aliyun.com/class: "fc"
    spec:
      template:
        metadata:
          annotations:
            fc.revision.serving.knative.aliyun.com/code-space: "inline"
        spec:
          containers:
            - image: nodejs8
              command:
              - |
                var getRawBody = require('raw-body')
                var version = 'coffee-default'
                module.exports.handler = function (request, response, context) {
                    var respBody = new Buffer('Hello ' + version + '\n')
                    response.setStatusCode(200)
                    response.setHeader('content-type', 'application/json')
                    response.send(respBody)
                };
    ```

    `image`和`command`下的内容代表使用Nodejs代码部署服务，不同的服务代码内容不同，请根据实际情况替换。

2.  部署服务。

    ```
    kubectl apply -f coffee.yaml
    ```


## 基于函数计算OSS文件部署服务

使用函数计算在对象存储OSS上下载函数代码，部署服务。

1.  创建coffee-oss.yaml。

    ```
    apiVersion: serving.knative.dev/v1
    kind: Service
    metadata:
      name: coffee
      annotations:
        workload.serving.knative.aliyun.com/class: "fc"
    spec:
      template:
        metadata:
          annotations:
            fc.revision.serving.knative.aliyun.com/code-space: "oss"
        spec:
          containers:
            - image: nodejs8
              command:
              - fc-zip
              - knative-demo/node-demo.zip
    ```

    `image`和`command`下的内容表示使用函数计算OSS文件部署服务。以下为`image`和`command`参数介绍：

    -   image：输入runtime环境，例如nodejs8。
    -   command：command数组有两个元素，依次是BucketName和ObjectName。
2.  部署服务。

    ```
    kubectl apply -f coffee-oss.yaml
    ```


## 基于函数计算镜像部署服务

为了简化开发者体验、提升开发和交付效率，函数计算提供了Custom Container Runtime。通过HTTP协议和函数计算系统交互，开发者将容器镜像作为函数的交付物。关于函数计算镜像部署的详细介绍请参见[简介]()。

**使用限制**

|配置项|使用限制|
|---|----|
|镜像大小|-   若您的函数执行内存<1 GB，则解压前镜像大小不能超过256 MB。
-   若您的函数执行内存≥1 GB，则解压前镜像大小不能超过1024 MB。 |
|镜像仓库|目前仅支持阿里云容器镜像服务中的默认实例镜像，未来会支持其他镜像仓库。关于默认实例的详细介绍请参见[容器镜像服务个人版概述]()。|
|镜像访问|目前仅支持同账号同地域下私有镜像仓库读取，未来会支持读取公共镜像。|
|容器内文件读写权限|容器run-as-user UID在10000到10999中随机分配。默认容器可在/tmp目录中具有写入权限，其他目录读写权限由镜像文件系统控制。如果运行的UID没有读写某个文件的权限，则需要在Dockerfile中修改。|
|容器可写层存储空间限制|排除只读镜像层，容器产生的数据最大不能超过512 MB。|
|权限策略|创建目标服务时需要绑定权限策略AliyunContainerRegistryReadOnlyAccess或者AliyunContainerRegistryFullAccess。您需要创建RAM角色，并绑定权限策略AliyunContainerRegistryReadOnlyAccess或者AliyunContainerRegistryFullAccess。然后在RAM角色管理页**基本信息**区域获取ARN。具体操作，请参见[为RAM角色授权](/cn.zh-CN/角色管理/为RAM角色授权.md)和[RAM角色概览](/cn.zh-CN/角色管理/RAM角色概览.md)。|

1.  创建coffee-image.yaml。

    ```
    apiVersion: serving.knative.dev/v1
    kind: Service
    metadata:
      name: coffee
      annotations:
        workload.serving.knative.aliyun.com/class: "fc"
    spec:
      template:
        metadata:
          annotations:
            fc.revision.serving.knative.aliyun.com/code-space: "image"
            fc.revision.serving.knative.aliyun.com/role-arm: "acs:ram::1041208914252405:role/fc-yuanyi-test"
        spec:
          containers:
            - image: registry.cn-shenzhen.aliyuncs.com/aliknative/nodejs-express:v3.0
    ```

    `annotations`和`image`下的内容代表基于函数镜像部署服务，以下为参数解释：

    -   fc.revision.serving.knative.aliyun.com/code-space: image：指定镜像类型。
    -   fc.revision.serving.knative.aliyun.com/role-arm：输入ARN绑定权限策略AliyunContainerRegistryReadOnlyAccess或者AliyunContainerRegistryFullAccess。
    -   image：输入镜像名称。
2.  部署服务。

    ```
    kubectl apply -f coffee-oss.yaml
    ```


## 结果验证

验证部署服务是否成功，以下以使用代码部署的服务为例。

1.  查看服务部署状态。

    ```
    kubectl get ksvc
    ```

    预期输出：

    ```
    NAME     URL                                                                                                                  LATESTCREATED   LATESTREADY    READY   REASON
    coffee   https://198639303048****.cn-beijing.fc.aliyuncs.com/2016-08-15/proxy/kn_default_coffee.http-prd/kn_default_coffee/   coffee-5bqdr    coffee-5bqdr   True
    ```

2.  使用curl命令访问服务。

    ```
    curl https://198639303048****.cn-beijing.fc.aliyuncs.com/2016-08-15/proxy/kn_default_coffee.http-prd/kn_default_coffee/
    ```

    预期输出：

    ```
    Hello coffee-default
    ```

3.  在函数计算控制台查看服务和函数。

    1.  登录[函数计算控制台](https://fc.console.aliyun.com)。

    2.  在顶部菜单栏，选择地域。

    3.  在左侧导航栏中，单击**服务/函数**。在服务/函数页面可以看到kn\_default\_coffee服务。

    4.  单击kn\_default\_coffee，在**函数列表**页签下可以看到kn\_default\_coffee函数。


