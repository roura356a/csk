---
keyword: [Harbor同步镜像, ACR默认实例版]
---

# 从自建Harbor同步镜像到ACR默认实例版

ACR（Alibaba Cloud Container Registry）是阿里云提供的容器镜像托管服务，支持全球20个地域的镜像全生命周期管理，联合容器服务等云产品，打造云原生应用的一站式体验。本文将为您介绍将自建Harbor上的镜像同步到ACR默认实例版 ，提供image-synce的基本使用示例。

您需要开通容器镜像服务。

登录[容器镜像服务控制台](https://cr.console.aliyun.com)开通相应的服务。

## 创建命名空间

命名空间可以有效管理其名下的仓库集合，包括仓库权限和仓库属性。如果需要同步的仓库不存在，需要在命名空间下打开**自动创建仓库**，并通过docker push自动创建仓库。

**说明：** 当目标仓库不存在时，docker push可以根据默认仓库类型自动创建的仓库类型是公有的还是私有。

1.  登录[容器镜像服务控制台](https://cr.console.aliyun.com)。

2.  单击左侧导航栏的**默认实例** \> **命名空间**。

3.  在命名空间页面，单击右上角的**创建命名空间**。

4.  在创建命名空间页面中自定义命名空间，并单击**确定**。


创建完成后，您可以在命名空间列表中看到新创建的命名空间。同时可以对命名空间进行管理，请参见[命名空间的基本操作]()。

## 授权子账号

如果您使用子账号进行后续操作，需要先创建RAM子账号并授予授权。如果您使用主账号进行后续操作，请跳过此步骤。

1.  创建RAM子账号。请参见[创建RAM用户](/cn.zh-CN/用户管理/创建RAM用户.md)。

2.  授予子账号相应的权限。请参见[自定义RAM授权策略](/cn.zh-CN/Kubernetes集群用户指南/授权/自定义RAM授权策略.md)。

    本示例中仅使用到创建、更新镜像仓库相关权限，访问控制的资源粒度为image-syncer命名空间，最小权限设置如下。

    ```
    {
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                    "cr:CreateRepository",
                    "cr:UpdateRepository",
                    "cr:PushRepository",
                    "cr:PullRepository"
                ],
                "Resource": [
                    "acs:cr:*:*:repository/image-syncer/*"
                ]
            }
        ],
        "Version": "1"
    }
    ```


## 获取访问凭证

在拉取私有镜像或者上传镜像前，需要`docker login`输入您的凭证信息。请按照以下操作获取访问凭证。

1.  单击左侧导航栏的**默认实例** \> **访问凭证**。

2.  在访问凭证页面单击**设置固定密码**。

3.  在设置固定密码页面填写**密码**和**确认密码**，并单击**确认**。


您可以通过API获取临时密码访问镜像服务实例，请参见[GetAuthorizationToken]()。

## image-syncer的同步配置

本例中，假设将本地搭建的Harbor中的library/nginx仓库同步到华北2的image-syncer命名空间下，并且保持仓库名称为nginx。配置文件示例如下。

```
{
    "auth": {
        "harbor.myk8s.paas.com:32080": {
            "username": "admin",
            "password": "xxxxxxxxx",
            "insecure": true
        },
        "registry.cn-beijing.aliyuncs.com": {
            "username": "acr_pusher@1938562138124787",
            "password": "xxxxxxxx"
        }
    },
    "images": {
        "harbor.myk8s.paas.com:32080/library/nginx": ""
    }
}
```

-   `harbor.myk8s.paas.com:32080`：本地搭建的harbor访问地址。需要替换成实际值。
    -   `username`：Harbor的用户名，本例中为admin。
    -   `password`：Harbor的密码。
    -   `insecure`：需要设置为true。
-   `registry.cn-beijing.aliyuncs.com`：目标仓库访问地址。本例中假设为镜像所在的地域为华北2。
    -   `username`：获取访问凭证的登录用户名。
    -   `password`：获取访问凭证的创建的密码。
-   `"harbor.myk8s.paas.com:32080/library/nginx": ""`：通过harbor.myk8s.paas.com:32080访问library/nginx仓库。

## 通过image-syncer同步镜像

1.  从[image-syncer](https://github.com/AliyunContainerService/image-syncer/releases/tag/v1.0.3)下载最新的安装包。

    **说明：** 目前只支持Linux amd64版本。

2.  安装和配置image-syncer工具。

    有关详细信息，请参见[安装和配置image-syncer](https://github.com/AliyunContainerService/image-syncer?spm=a2c6h.12873639.0.0.66b165a8HrkbnA#compile-manually)。

3.  执行如下命令同步镜像。

    ```
    # 设置默认目标registry为registry.cn-beijing.aliyuncs.com，默认目标namespace为image-syncer
    # 并发数为10，重试次数为10
    # 日志输出到./log文件下，不存在会自动创建，不指定的话默认会将日志打印到Stderr
    # 指定配置文件为harbor-to-acr.json，内容如上所述
    ./image-syncer --proc=10 --config=./harbor-to-acr.json --registry=registry.cn-beijing.aliyuncs.com --namespace=image-syncer --retries=10 --log=./log
    ```


## 执行结果

一次镜像同步会经历三个阶段：生成同步任务、执行同步任务以及重试失败任务。其中，每个同步任务都代表了一个需要同步的镜像（tag），如果配置文件中某条规则没有指定tag，在**生成同步任务**阶段会自动列出源仓库所有tag，并生成对应的同步任务，如果生成同步任务失败，也会在重试阶段进行重试。

-   同步镜像正常结果输出如下所示。

    ![结果正常](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6763659951/p71380.png)

-   同步镜像异常结果（例如，账号或密码错误）输出如下所示。

    ![结果异常](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6763659951/p71384.png)

-   在运行时，image-syncer会打印出如下的日志信息。

    ![日志信息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7763659951/p71386.png)


