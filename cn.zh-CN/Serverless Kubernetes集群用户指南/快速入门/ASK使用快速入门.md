---
keyword: [Serverless集群, 创建Serverless, ASK, Serverless Kubernetes]
---

# ASK使用快速入门

本文介绍如何在容器服务管理控制台快速创建阿里云Serverless Kubernetes（ASK）集群、使用镜像创建应用和查看容器。

-   开通容器服务ACK，且授权默认角色和开通相关云产品。具体操作，请参见[首次使用容器服务Kubernetes版](/cn.zh-CN/快速入门/首次使用容器服务Kubernetes版.md)。
-   登录[弹性容器实例](https://eci.console.aliyun.com)控制台，开通ECI服务。




## 步骤一：创建ASK集群

**说明：** 本文仅介绍创建ASK集群涉及的关键参数的设置和相关内容，在实际工作场景中，请您根据具体需求设置参数和安装相应组件。更多信息，请参见[创建Serverless Kubernetes集群](/cn.zh-CN/Serverless Kubernetes集群用户指南/集群/创建Serverless Kubernetes集群.md)。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击页面右上角的**创建集群**。

4.  单击**ASK集群**页签，然后完成集群配置。

    |配置项|描述|
    |---|--|
    |**集群名称**|填写集群的名称，本文集群名称为ask-hangzhou。**说明：** 集群名称应包含1~63个字符，可包含数字、汉字、英文字符或连字符（-）。 |
    |**地域**|选择集群所在的地域，本文选择为**华东1（杭州）**。|
    |**专有网络**|设置集群的网络。Kubernetes集群仅支持专有网络。支持**自动创建**和**使用已有**的VPC。     -   **自动创建**：集群会自动新建一个VPC，并在VPC中自动创建NAT网关以及配置SNAT规则。
    -   **使用已有**：您可以在已有VPC列表中选择所需的VPC和交换机。如需访问公网，例如下载容器镜像，要配置NAT网关，建议将容器镜像上传到集群所在区域的阿里云镜像服务，并通过内网VPC地址拉取镜像。
详情请参见[使用专有网络](/cn.zh-CN/专有网络和交换机/使用专有网络.md)。

本文选择使用已有专有网络。 |
    |**配置SNAT**|设置是否为专有网络创建NAT网关并配置SNAT规则。 仅当**专有网络**选择为**自动创建**时，需要设置该选项。

**说明：** 若您选择**自动创建**VPC，可选择是否自动配置SNAT。若选择不自动配置SNAT，您可自行配置NAT网关实现VPC安全访问公网环境，并且手动配置SNAT转发策略，否则VPC内实例将不能正常访问公网。

详情请参见[创建NAT网关实例](/cn.zh-CN/基本功能操作/创建NAT网关实例.md)。

本文选中**为专有网络配置SNAT**。 |
    |**Service CIDR**|设置**Service CIDR**。您需要指定**Service CIDR**，网段不能与VPC及VPC内已有Kubernetes集群使用的网段重复，创建成功后不能修改。而且Service地址段也不能和Pod地址段重复，有关Kubernetes网络地址段规划的信息，请参见[Kubernetes集群网络规划](/cn.zh-CN/Kubernetes集群用户指南/网络/Kubernetes集群网络规划.md)。

本文设置为**172.21.0.0/20** |
    |**API Server访问**|ASK默认为API Server创建一个内网SLB实例，您可修改SLB实例规格。更多信息，请参见[实例规格](/cn.zh-CN/传统型负载均衡CLB/CLB用户指南/实例/CLB实例概述.md)。

**说明：** 删除默认创建的SLB实例将会导致无法访问API Server。

您可设置是否开放**使用EIP暴露API Server**。API Server提供了各类资源对象（Pod，Service等）的增删改查及Watch等HTTP Rest接口。

    -   如果选择开放，ASK会创建一个EIP，并挂载到SLB上。此时，Kubernetes API服务（即API Server）会通过EIP的6443端口暴露出来，您可以在外网通过kubeconfig连接并操作集群。
    -   如果选择不开放，则不会创建EIP，您只能在VPC内部用kubeconfig连接并操作集群。
更多信息，请参见[控制集群API Server的公网访问能力](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/集群访问控制/控制集群API Server的公网访问能力.md)。

本文选中**使用EIP暴露API Server**。 |
    |**服务协议**|创建集群前，需阅读并选中**《无服务器Kubernetes服务协议》**。|

5.  在**ASK集群**页面右侧，单击**创建集群**，在弹出的当前配置确认对话框中，单击**确定**。


**后续步骤**

-   集群创建成功后，您可以在容器服务管理控制台的Kubernetes集群列表页面查看所创建的ASK集群。

![创建集群](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/3645388261/p70347.png)

-   在集群列表页面中，找到刚创建的集群，单击操作列中的**详情**，单击**基本信息**和**连接信息**页签，查看集群的基本信息和连接信息。

    ![基本信息](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/3645388261/p135811.png)


## 步骤二：使用镜像创建应用

**说明：** 本文仅介绍使用镜像创建应用涉及的关键参数设置，更多信息，请参见[使用镜像创建应用](/cn.zh-CN/Serverless Kubernetes集群用户指南/应用/使用镜像创建应用.md)。

**第一步：配置应用基本信息**

1.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

2.  在**无状态**页面中，单击**使用镜像创建**。

3.  在应用基本信息配置向导页面，设置应用的基本信息。

    |配置项|描述|
    |---|--|
    |应用名称|设置应用的名称，本文为serverless-app-deployment。|
    |类型|定义资源对象的类型，可选择**无状态（Deployment）**、**有状态（StatefulSet）**等。本文选择**无状态（Deployment）**。|

4.  单击**下一步**。


**第二步：配置容器**

1.  在**容器配置**配置向导页面的**基本配置**区域，完成容器的基本配置。

    |配置项|描述|
    |---|--|
    |镜像名称|您可以单击**选择镜像**，在弹出的对话框中选择所需的镜像并单击**确定。**您还可以填写私有镜像。填写的格式为`domainname/namespace/imagename:tag`。

本文选择使用个人版实例镜像，所属地域为**华东1（杭州）** |
    |镜像Tag|您可以单击**选择镜像Tag** 选择镜像的版本。若不指定，默认为最新版。本文默认不指定镜像Tag。 |

2.  在**端口设置**区域，单击**新增**设置容器的端口。

    -   名称：设置容器端口名称。本文容器端口名称为nginx。
    -   容器端口：设置暴露的容器访问端口或端口名，端口号必须介于1~65535。本文容器端口设置为80。
    -   协议：支持TCP和UDP。
3.  单击**下一步**。


**第三步：完成高级配置**

1.  在**高级配置**向导页面的**访问设置**区域，设置暴露后端Pod的方式。

    配置服务（Service）：在**服务（Service）**右侧，单击**创建**设置创建服务配置项。

    |配置项|描述|
    |---|--|
    |名称|输入服务的名称，本文为**serverless-app-svc**。|
    |端口映射|添加服务端口和容器端口。容器端口需要与后端的Pod中暴露的容器端口一致。本文服务端口和容器端口分别配置为8080和80。 |

    在**访问设置**区域，您可以看到创建完毕的服务，您可单击**变更**和**删除**进行二次配置。

2.  单击**创建**。


**第四步：访问nginx应用**

1.  在**创建完成**页签中单击**查看应用详情**。您可以看到新建的**serverless-app-deployment**出现在无状态列表下。

    ![部署列表](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/6663488261/p10257.png)

2.  访问nginx应用。

    1.  在集群管理页左侧导航栏中，选择**网络** \> **服务**，可以看到新建的服务**serverless-app-svc**出现在服务列表下。

        ![服务列表](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/6663488261/p10258.png)

    2.  单击新建服务的**外部端点**链接，即可在浏览器中访问外部端点，本示例为Nginx。

        ![nginx](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/7548649951/p146837.png)

        **说明：** 关于如何通过创建Ingress路由访问应用，请参见[通过ALB Ingress访问服务](/cn.zh-CN/Serverless Kubernetes集群用户指南/网络/ALB Ingress管理/通过ALB Ingress访问服务.md)。


## 步骤三：查看容器

1.  在集群管理页左侧导航栏中，选择**工作负载** \> **容器组**。

2.  选择目标容器组，单击右侧**操作**列下的**详情**。

    **说明：** 您可对容器组进行更新和删除操作。对于通过模板部署创建的容器组，建议您通过模板进行管理，不要直接删除或修改Pod。

3.  进入容器组的详情页，您可查看该容器组的详情信息。

    ![2](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/9074058261/p302646.png)


