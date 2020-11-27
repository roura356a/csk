---
keyword: [kritis-validation-hook, 验证签名, 容器镜像]
---

# kritis-validation-hook组件介绍

kritis-validation-hook组件是部署可信容器环节中进行容器镜像签名验证的关键组件。通过在部署前对容器镜像进行签名验证可以确保只部署经过可信授权方进行过签名的容器镜像，降低在您的环境中运行意外或恶意代码的风险。本文通过具体示例对kritis-validation-hook组件的工作效果进行介绍。

kritis-validation-hook组件在开源的[kritis](https://github.com/grafeas/kritis)软件的基础上增加了对[阿里云容器镜像服务ACR](https://www.aliyun.com/product/acr)的深度集成，支持验证通过[阿里云密钥管理服务KMS](https://www.aliyun.com/product/kms)进行过签名的容器镜像。kritis-validation-hook组件通过与[云安全中心](https://www.aliyun.com/product/sas)、KMS和ACR的深度合作，实现了全自动化的对容器镜像进行加签和验签，协助您构建更安全的集群运行环境。实现自动验证容器镜像签名，请参见[使用kritis-validation-hook组件实现自动验证容器镜像签名](/cn.zh-CN/Kubernetes集群用户指南/应用管理/使用kritis-validation-hook组件实现自动验证容器镜像签名.md)。

## 权限

如果您使用的是托管版集群，为了正常运行kritis-validation-hook组件，需要确保集群使用的Worker RAM角色拥有以下资源访问权限：

```
cr:ListInstance
cr:ListMetadataOccurrences
```

如果您的集群使用的Worker RAM角色没有上述访问权限，您可以通过以下方法添加组件所需的资源访问权限。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群信息页面的**集群资源**页签找到您集群所使用的Worker RAM角色。

5.  点击Worker RAM角色对应的链接，进入相应的RAM角色管理页面。

6.  点击**权限管理**页签内的**权限策略名称**列下的链接，进入**策略内容**页面。

7.  点击**修改策略内容**，新增以下Action策略，然后点击**确认**保存修改后的策略内容。

    ```
    {
        "Action": [
            "cr:ListInstance",
            "cr:ListMetadataOccurrences"
        ],
        "Resource": [
            "*"
        ],
        "Effect": "Allow"
    }
    ```


## 示例

以下通过对当前default这个namespace启用镜像签名验证为例，展示kritis-validation-hook组件的工作效果。

**说明：** 因为镜像签名不属于kritis-validation-hook组件的工作范畴，所以示例略过签名的步骤。本示例中涉及的签名后的信息如下。

-   使用KMS进行过签名的容器镜像：kritis-demo-registry.cn-hangzhou.cr.aliyuncs.com/kritis-demo/alpine@sha256:ddba4d27a7ffc3f86dd6c2f92041af252a1f23a8e742c90e6e1297bfa1bc0c45。
-   使用的KMS key对应的公钥信息存储在publickey.txt中。
-   使用的KMS key-id：4a2ef103-5aa3-4220-89ee-kms-key-id。

1.  运行以下命令配置AttestationAuthority，声明相应的可信授权方。

    这里配置的是本示例KMS公钥信息。

    ```
    cat <<EOF > AttestationAuthority.yaml
    apiVersion: kritis.grafeas.io/v1beta1
    kind: AttestationAuthority
    metadata:
      name: demo-aa
    spec:
      noteReference: namespaces/demo-aa
      publicKeyData: $(cat publickey.txt | base64 | tr -d '\n')
      publicKeyId: 4a2ef103-5aa3-4220-89ee-kms-key-id
    EOF
    
    kubectl apply -f AttestationAuthority.yaml
    ```

2.  运行以下命令配置GenericAttestationPolicy，声明验证签名策略及使用哪个可信授权方的信息去验证签名。

    ```
    cat <<EOF > GenericAttestationPolicy.yaml
    apiVersion: kritis.grafeas.io/v1beta1
    kind: GenericAttestationPolicy
    metadata:
      name: demo-gap
    spec:
      attestationAuthorityNames:
      - demo-aa
    EOF
    
    kubectl apply -f GenericAttestationPolicy.yaml
    ```

3.  运行以下命令测试镜像签名验证功能，拒绝部署未由信任的授权方进行过签名的镜像。

    ```
    kubectl create deployment test-denied --image=alpine:3.11
    ```

    系统输出类似以下结果：

    ```
    Error from server: admission webhook "kritis-validation-hook-deployments.grafeas.io" denied the request: image alpine:3.11 is not attested
    ```

    输入以下命令：

    ```
    kubectl create deployment test-denied --image=kritis-demo-registry.cn-hangzhou.cr.aliyuncs.com/kritis-demo/alpine:3.11
    ```

    系统输出类似以下结果：

    ```
    Error from server: admission webhook "kritis-validation-hook-deployments.grafeas.io" denied the request: image kritis-demo-registry.cn-hangzhou.cr.aliyuncs.com/kritis-demo/alpine:3.11 is not attested
    ```

4.  测试镜像签名验证功能，部署由信任的授权方进行过签名的镜像。

    输入以下命令：

    ```
    kubectl create deployment test-allow --image=kritis-demo-registry.cn-hangzhou.cr.aliyuncs.com/kritis-demo/alpine@sha256:ddba4d27a7ffc3f86dd6c2f92041af252a1f23a8e742c90e6e1297bfa1bc0c45
    ```

    系统输出类似以下结果：

    ```
    deployment.apps/test-allow created
    ```


## 配置镜像验签白名单

kritis-validation-hook组件支持通过配置镜像验签白名单的方式解决中间件或服务网格场景下第三方组件自动注入的sidecar容器的镜像无法通过镜像验签导致Pod无法部署的问题。组件不会对白名单内的镜像进行签名验证，只会验证未在白名单内的镜像。

可以通过定义`admissionallowlists.kritis.grafeas.io`资源的方法来配置镜像验签白名单。CRD资源定义如下：

```
apiVersion: kritis.grafeas.io/v1beta1   # 默认值，无需修改。
kind: AdmissionAllowlist                # 默认值，无需修改。
metadata:
  name: kritis-allowlist                # 资源名，在集群内唯一。
spec:
  patterns:                             # 白名单配置，可以定义多条白名单。
  - namePattern: 'registry*.*.aliyuncs.com/acs/*' # 想忽略的镜像名称，具体格式详见下方说明。
  - namePattern: 'registry-vpc.cn-beijing.aliyuncs.com/arms-docker-repo/*'
    namespace: 'default'    # [可选]，白名单配置适用于哪个命名空间，未配置时适用于所有命名空间。
```

若您需要把ACK的系统镜像加入白名单，可以通过下面的操作步骤进行配置。

1.  定义以下白名单配置，保存为`kritis-admission-allowlist-acs.yaml`。

    ```
    apiVersion: kritis.grafeas.io/v1beta1
    kind: AdmissionAllowlist
    metadata:
      name: allow-acs-images
    spec:
      patterns:
      - namePattern: 'registry*.*.aliyuncs.com/acs/*'
    ```

    namePattern配置项值支持完整匹配，且支持通过星号（\*）字符实现简单的通配符匹配，具体说明如下。

    -   当配置项值不包含星号（\*）字符时，将按配置的值进行完整匹配。比如`nginx:v0.1.0`只会匹配`nginx:v0.1.0`。
    -   当通过星号（\*）字符实现通配符匹配时，有以下限制。
        -   星号（\*）位于末尾时，匹配除正斜线（/）外的任意字符。比如`a.com/nginx*`匹配`a.com/nginx:v0.1.0`，但是不匹配`a.com/nginx/test:v0.1.0` 。
        -   星号（\*）不位于末尾时，匹配字母、数字、短横线\(-\) 以及下划线（\_） 。比如`registry-vpc.cn-*.aliyuncs.com/acs/pause:3.2`既匹配`registry-vpc.cn-hangzhou.aliyuncs.com/acs/pause:3.2`，也匹配`registry-vpc.cn-beijing.aliyuncs.com/acs/pause:3.2`。
    常见的白名单如下，您可以根据实际需求进行添加。

    ```
    # 容器服务ACK使用的镜像
    - namePattern: 'registry*.*.aliyuncs.com/acs/*'
    
    # 容器服务ACK使用的镜像（限定中国region）
    - namePattern: 'registry*.cn-*.aliyuncs.com/acs/*'
    
    # ARMS使用的镜像
    - namePattern: 'registry*.*.aliyuncs.com/arms-docker-repo/*'
    
    # ARMS使用的镜像（限定中国region）
    - namePattern: 'registry*.cn-*.aliyuncs.com/arms-docker-repo/*'
    ```

2.  执行以下命令，应用定义的白名单配置。

    ```
    kubectl apply -f kritis-admission-allowlist-acs.yaml 
    ```

    系统输出类似以下结果：

    ```
    admissionallowlist.kritis.grafeas.io/allow-acs-images created
    ```

3.  执行`kubectl get admissionallowlists.kritis.grafeas.io`命令，查看配置的白名单配置。

    系统输出类似以下结果：

    ```
    NAME               AGE
    allow-acs-images   2m22s
    ```


## 未来展望

kritis-validation-hook组件在未来可能会同阿里云其他服务一起合作提供不仅限于以下的增强功能。

-   集成不可变镜像tag功能。通过集成不可变镜像tag功能，在对镜像签名进行验证的时候不再需要您必须指定镜像的digest，支持对普通的镜像tag进行验签，提升您的使用体验。（目前最新版本已支持对启用了不可变镜像tag功能的镜像进行签名验证，详见[开启镜像版本不可变]() ）
-   集成镜像漏洞检测功能。不只是验证镜像签名，您同样也可以拒绝部署包含特定级别漏洞的容器镜像，进一步地降低您环境的安全风险。

**相关文档**  


[变更记录](/cn.zh-CN/新功能发布记录/组件介绍与变更记录/kritis-validation-hook/变更记录.md)

[使用kritis-validation-hook组件实现自动验证容器镜像签名](/cn.zh-CN/Kubernetes集群用户指南/应用管理/使用kritis-validation-hook组件实现自动验证容器镜像签名.md)

