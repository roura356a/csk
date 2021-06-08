---
keyword: [容器镜像验签, kritis-validation-hook]
---

# 使用kritis-validation-hook组件实现自动验证容器镜像签名

本文介绍如何使用容器镜像服务ACR（Container Registry）、密钥管理服务KMS（Key Management Service）、云安全中心（Security Center）和kritis-validation-hook组件实现自动验证容器镜像签名，确保只部署可信授权方签名的容器镜像，降低在您环境中发生意外或恶意代码的风险。

## 步骤一：安装kritis-validation-hook组件

1.  在左侧导航栏选择**运维管理** \> **组件管理**。

2.  在组件管理页面的**安全**区域，找到**kritis-validation-hook**并单击**安装**。

3.  授予访问kritis-validation-hook组件所需的权限。

    如果您使用的是ACK托管版集群，还需要确保您的集群拥有运行kritis-validation-hook组件所需的访问权限。具体操作，请参见[t1876097.md\#section\_c7v\_xxm\_q88](/cn.zh-CN/Kubernetes集群用户指南/应用/镜像/kritis-validation-hook组件介绍.mdsection_c7v_xxm_q88)。

    **说明：** ACK专有版集群默认开通运行kritis-validation-hook组件所需的访问权限。


## 步骤二：创建用于容器镜像签名的KMS密钥

1.  **说明：** 选择**密钥类型**为**RSA\_2048**；选择**密钥用途**为**SIGN/VERIFY**。


## 步骤三：在云安全中心配置使用KMS密钥的证明者

1.  在**证明者**页签中，单击**创建证明者**。

    创建证明者的配置信息，请参见[t1877364.md\#](/cn.zh-CN/安全运营/容器签名.md)。

2.  单击**确定**。


## 步骤四：在ACR中开启容器镜像自动加签功能

1.  登录[容器镜像服务控制台](https://cr.console.aliyun.com/)。

2.  在实例列表页面，单击目标企业版实例名称或者单击目标企业版实例的**管理**。

3.  在企业版实例管理页左侧导航栏中，选择**仓库管理** \> **命名空间**。

    创建一个命名空间，用于为该命名空间内的所有容器镜像启用镜像加签功能。具体操作，请参见[t1841669.md\#]()。

4.  为创建的命名空间启用容器镜像自动加签功能。

    添加加签规则时选择步骤三中在云安全中心创建的证明者。

    1.  在企业版实例管理页左侧导航栏中，选择**内容可信** \> **镜像加签**。

    2.  在镜像加签页签中，单击**添加加签规则**。

        添加加签规则配置详情，请参见[t1880102.md\#section\_t7n\_hoh\_wrw]()。


## 步骤五：在云安全中心启用容器镜像验签功能

通过在云安全中心添加和启用策略的方式来为Kubernetes集群中某个命名空间启用容器镜像验签功能。

1.  在**安全策略**页签，单击**添加策略**。

    添加策略的配置说明，请参见[创建安全策略](/cn.zh-CN/安全运营/容器签名.md)。

    ![验签](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4185659951/p100026.png)

2.  单击**确定**。


## 步骤六：验证容器镜像自动验签功能

**说明：** 目前只支持使用digest格式的镜像。

执行以下命令验证容器镜像自动验签功能：

-   已开启验签功能的Default命名空间将拒绝部署使用未加签的容器镜像服务。

    使用Tag格式镜像的命令如下：

    ```
    kubectl -n default create deployment not-sign --image=alpine:3.11 -- sleep 10
    ```

    ```
    Error from server: admission webhook "kritis-validation-hook-deployments.grafeas.io" denied the request: image alpine:3.11 is not attested
    ```

    使用Digest格式镜像的命令如下：

    ```
    kubectl -n default create deployment not-sign --image=alpine@sha256:ddba4d27a7ffc3f86dd6c2f92041af252a1f23a8e742c90e6e1297bfa1bc0c45 -- sleep 10
    ```

    ```
    Error from server: admission webhook "kritis-validation-hook-deployments.grafeas.io" denied the request: image alpine@sha256:ddba4d27a7ffc3f86dd6c2f92041af252a1f23a8e742c90e6e1297bfa1bc0c45 is not attested
    ```

-   向已开启自动加签功能的容器镜像服务命名空间中推送一个镜像，然后验证可以使用这个加签的容器镜像部署服务。

    ```
    docker push kritis-demo-registry-vpc.cn-hongkong.cr.aliyuncs.com/kritis-demo/alpine:3.11
    ```

    ```
    The push refers to repository [kritis-demo-registry-vpc.cn-hongkong.cr.aliyuncs.com/kritis-demo/alpine]
    5216338b40a7: Pushed
    3.11: digest: sha256:ddba4d27a7ffc3f86dd6c2f92041af252a1f23a8e742c90e6e1297bfa1bc0c45 size: 528
    ```

    放行已自动加签容器镜像的命令如下：

    ```
    kubectl -n default create deployment is-signed --image=kritis-demo-registry-vpc.cn-hongkong.cr.aliyuncs.com/kritis-demo/alpine@sha256:ddba4d27a7ffc3f86dd6c2f92041af252a1f23a8e742c90e6e1297bfa1bc0c45 -- sleep 10
    ```

    ```
    deployment.apps/is-signed created
    ```


**相关链接**  


kritis-validation-hook组件介绍，请参见[t1876097.md\#](/cn.zh-CN/Kubernetes集群用户指南/应用/镜像/kritis-validation-hook组件介绍.md)。

kritis-validation-hook组件变更记录，请参见[t1876098.md\#](/cn.zh-CN/产品发布记录/组件介绍与变更记录/安全/kritis-validation-hook.md)。

  


