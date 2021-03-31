---
keyword: [自建Harbor同步镜像, ACR企业版]
---

# 从自建Harbor同步镜像到ACR企业版

本文将为您介绍将自建Harbor上的镜像同步到ACR（Alibaba Cloud Container Registry）企业版，提供image-syncer的基本使用示例。

-   您需要开通容器镜像服务。

    登录[容器镜像服务控制台](https://cr.console.aliyun.com)开通相应的服务。

-   您已创建了ACR企业版实例，请参见[创建企业版实例]()。

ACR企业版提供企业级容器镜像、Helm Chart安全托管能力，拥有企业级安全独享特性，具备千节点镜像分发、全球多地域同步能力。提供云原生应用交付链，实现一次应用变更，全球化多场景自动交付。主要适用于安全需求高、业务多地域部署、拥有大规模集群节点的企业级客户使用。

## 步骤一：创建命名空间

命名空间可以有效管理该命名空间下的仓库集合，包括仓库权限和仓库属性。如果需要同步的仓库不存在，您可以在命名空间下打开**自动创建仓库**，使用docker push命令自动创建公有或私有仓库。

1.  登录[容器镜像服务控制台](https://cr.console.aliyun.com)。

2.  单击左侧导航栏的**企业版实例** \> **实例列表**。

3.  在目标实例右侧单击**管理**。

4.  单击左侧导航栏的**仓库管理** \> **命名空间**。

5.  单击右上角的**创建命名空间**。

6.  在创建命名空间页面中自定义命名空间，勾选**自动创建仓库**和**默认仓库类型**后，并单击**确定**。

    **说明：** 需要开启**自动创建仓库**按钮。


创建完成后，您可以在命名空间列表中看到新创建的命名空间。同时可以对命名空间进行管理，请参见[命名空间的基本操作]()。

## 步骤二：授权子账号

如果您使用子账号进行后续操作，需要先创建RAM子账号并授予授权。如果您使用主账号进行后续操作，请跳过此步骤。

1.  创建RAM子账号。请参见[创建RAM用户](/intl.zh-CN/用户管理/创建RAM用户.md)。

2.  授予子账号相应的权限。请参见[自定义RAM授权策略](/intl.zh-CN/Kubernetes集群用户指南/授权/自定义RAM授权策略.md)。

    本示例中仅使用到创建、更新镜像仓库相关权限，最小权限设置如下，访问控制的资源粒度为image-syncer命名空间。

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


## 步骤三：配置公网访问控制

企业版实例创建后，默认不允许通过公网访问。因此在配置公网的访问控制策略前，需要先打开公网的访问入口。

1.  登录[容器镜像服务控制台](https://cr.console.aliyun.com)，在控制台页面的左上方，选择所需地域。

2.  在左侧导航栏中，选择**企业版实例** \> **实例列表**。

3.  在**实例列表**页面中，单击所需配置的企业版实例，进入该实例的配置页面。

4.  在左侧导航栏中，选择**仓库管理** \> **访问控制**，进入访问控制的配置页面。

    **说明：** 如果您需要为Helm Chart配置访问控制，请选择**Helm Chart** \> **访问控制**。

5.  在**公网**页签下，单击**添加公网白名单**。

6.  在**添加公网白名单**对话框中，输入允许访问的地址段和备注信息。

7.  单击**确认**。

    完成添加之后，该白名单所在网段的ECS即可正常访问企业版实例。

    **说明：** 如果您希望所有公网下的ECS均可以访问企业版实例，需要保持公网访问入口开启并删除所有公网白名单。请注意完全暴露在公网的企业版实例存在被攻击的风险，请谨慎操作。


## 步骤四：获取访问凭证

在拉取私有镜像或者上传镜像前，需要`docker login`输入您的凭证信息。请按照如下操作获取访问凭证。

1.  单击左侧导航栏的**企业版实例** \> **实例列表**。

2.  在**实例列表**页面中，单击所需配置的企业版实例，进入该实例的配置页面。

3.  单击左侧导航栏的**实例管理** \> **访问凭证**。

4.  单击**设置固定密码**。

5.  在设置固定密码页面填写**密码**和**确认密码**，并单击**确认**。


您可以通过API获取临时密码访问镜像服务实例，请参见[GetAuthorizationToken]()。

## 步骤五：配置image-syncer的同步

使用访问凭证中创建的密码，完成如下image-syncer的同步配置。

与之前同步到默认实例版ACR不同的是，每个ACR企业版实例有自己单独的域名，并且命名空间对于每个不同企业版实例之间来说都是隔离的。

**说明：** 每个ACR企业版实例有自己单独的域名，一个公网可见，一个仅专有网络可见。

-   如果镜像同步工具运行在个人环境上需要使用公网域名。
-   如果要使用仅专有网络可见的域名，则将镜像同步工具运行在阿里云ECS实例上，并且通过配置使域名对该ECS所在的专有网络可见。

本例中，本地搭建的Harbor中的library/nginx仓库同步到ACR企业版实例中image-syncer命名空间下，并且保持仓库名称为nginx，配置文件如下：

```
{
    "auth": {
        "harbor.myk8s.paas.com:32080": {
            "username": "admin",
            "password": "xxxxxxxxx",
            "insecure": true
        },
        "ruohe-test-registry.cn-shanghai.cr.aliyuncs.com": {
            "username": "ruohehhy",
            "password": "xxxxxxxx"
        }
    },
    "images": {
        "harbor.myk8s.paas.com:32080/library/nginx": ""
    }
}
```

-   `harbor.myk8s.paas.com:32080`：本地搭建的Harbor访问地址。需要替换成实际值。
    -   `username`：Harbor的用户名，本例中为admin。
    -   `password`：Harbor的密码。
    -   `insecure`：需要设置为ture。
-   `ruohe-test-registry.cn-shanghai.cr.aliyuncs.com`：本例中使用公网访问域名。
    -   `username`：获取访问凭证的登录用户名。
    -   `password`：获取访问凭证的创建的密码。
-   `"harbor.myk8s.paas.com:32080/library/nginx": ""`： 通过harbor.myk8s.paas.com:32080访问library/nginx仓库。

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


