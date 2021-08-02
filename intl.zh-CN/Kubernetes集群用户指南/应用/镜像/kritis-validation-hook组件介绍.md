---
keyword: [kritis-validation-hook, 验证签名, 容器镜像]
---

# kritis-validation-hook组件介绍

kritis-validation-hook组件是部署可信容器环节中进行容器镜像签名验证的关键组件。通过在部署前对容器镜像进行签名验证可以确保只部署经过可信授权方进行过签名的容器镜像，降低在您的环境中运行意外或恶意代码的风险。本文通过具体示例对kritis-validation-hook组件的工作效果进行介绍。

已创建托管版或专有版集群。具体操作，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)或[创建Kubernetes专有版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes专有版集群.md)。

kritis-validation-hook组件在开源的[kritis](https://github.com/grafeas/kritis)软件的基础上增加了对[阿里云容器镜像服务ACR](https://www.alibabacloud.com/zh/product/container-registry)的深度集成，支持验证通过[阿里云密钥管理服务KMS](https://www.alibabacloud.com/zh/products/kms)签名的容器镜像。kritis-validation-hook组件通过与[云安全中心](https://www.alibabacloud.com/zh/products/threat-detection)、KMS和ACR的深度合作，实现了全自动化的对容器镜像进行加签和验签，协助您构建更安全的集群运行环境。实现自动验证容器镜像签名，请参见[使用kritis-validation-hook组件实现自动验证容器镜像签名](/intl.zh-CN/Kubernetes集群用户指南/应用/镜像/使用kritis-validation-hook组件实现自动验证容器镜像签名.md)。

## 配置资源访问权限

为了正常运行kritis-validation-hook组件，需要确保集群使用的RAM角色拥有以下资源访问权限：

**说明：**

-   若您使用的是托管版集群，需要确保集群使用的Worker RAM角色拥有以下资源访问权限。
-   若您使用的是专有版集群，需要确保集群使用的Master RAM角色和Worker RAM角色拥有以下资源访问权限。

```
cr:ListInstance
cr:ListMetadataOccurrences
```

如果您的集群使用的RAM角色没有上述访问权限，您可以通过以下方法添加组件所需的资源访问权限。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群信息页面的**集群资源**页签找到集群所使用的RAM角色。

    -   对于托管版集群，单击**Worker RAM 角色**名称。
    -   对于专有版集群，先后单击**Master RAM 角色**和**Worker RAM 角色**名称。
5.  在RAM角色管理页面单击权限管理页签，然后单击**权限策略名称**列下的**k8s\*\*\*\*\*\*RolePolicy-\*\*\*\***。

6.  在权限策略管理页面单击策略内容页签，再单击**修改策略内容**。

7.  在修改策略内容面板新增以下Action策略，然后单击**确定**。

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


## 启用镜像签名验证示例

以下通过对当前**default**命名空间启用镜像签名验证为例，展示kritis-validation-hook组件的工作效果。

**说明：** 因为镜像签名不属于kritis-validation-hook组件的工作范畴，所以示例略过签名的步骤。本示例中涉及的签名后的信息如下：

-   使用KMS签名的容器镜像：kritis-demo-registry.cn-hangzhou.cr.aliyuncs.com/kritis-demo/alpine@sha256:ddba4d27a7ffc3f86dd6c2f92041af252a1f23a8e742c90e6e1297bfa1bc0c45。
-   使用的KMS key对应的公钥信息存储在publickey.txt中。
-   使用的KMS `key-id：4a2ef103-5aa3-4220-****`。

1.  配置AttestationAuthority，声明相应的可信授权方。

    1.  使用以下内容创建AttestationAuthority.yaml文件。

        ```
        apiVersion: kritis.grafeas.io/v1beta1
        kind: AttestationAuthority
        metadata:
          name: demo-aa
        spec:
          noteReference: namespaces/demo-aa
          publicKeyData: $(cat publickey.txt | base64 | tr -d '\n')
          publicKeyId: 4a2ef103-5aa3-4220-****
        ```

    2.  执行以下命令配置KMS公钥信息。

        ```
        kubectl apply -f AttestationAuthority.yaml
        ```

2.  配置GenericAttestationPolicy，声明验证签名策略及使用可信授权方的信息验证签名。

    1.  使用以下内容创建GenericAttestationPolicy.yaml文件。

        ```
        apiVersion: kritis.grafeas.io/v1beta1
        kind: GenericAttestationPolicy
        metadata:
          name: demo-gap
        spec:
          attestationAuthorityNames:
          - demo-aa
        ```

    2.  执行以下命令配置验证签名策略。

        ```
        kubectl apply -f GenericAttestationPolicy.yaml
        ```

3.  测试镜像签名验证功能，部署未经信任授权方签名的镜像。

    -   执行以下命令测试镜像签名验证功能。

        ```
        kubectl create deployment test-denied --image=alpine:3.11
        ```

        预期输出：

        ```
        Error from server: admission webhook "kritis-validation-hook-deployments.grafeas.io" denied the request: image alpine:3.11 is not attested
        ```

    -   执行以下命令测试镜像签名验证功能。

        ```
        kubectl create deployment test-denied --image=kritis-demo-registry.cn-hangzhou.cr.aliyuncs.com/kritis-demo/alpine:3.11
        ```

        预期输出：

        ```
        Error from server: admission webhook "kritis-validation-hook-deployments.grafeas.io" denied the request: image kritis-demo-registry.cn-hangzhou.cr.aliyuncs.com/kritis-demo/alpine:3.11 is not attested
        ```

    从预期输出可得，镜像签名验证功能可拒绝部署未经信任授权方签名的镜像。

4.  测试镜像签名验证功能，部署经信任授权方签名的镜像。

    执行以下命令测试镜像签名验证功能。

    ```
    kubectl create deployment test-allow --image=kritis-demo-registry.cn-hangzhou.cr.aliyuncs.com/kritis-demo/alpine@sha256:ddba4d27a7ffc3f86dd6c2f92041af252a1f23a8e742c90e6e1297bfa1bc0c45
    ```

    预期输出：

    ```
    deployment.apps/test-allow created
    ```

    从预期输出可得，镜像签名验证功能可部署经信任授权方签名的镜像。


## 配置镜像验签白名单

在中间件或服务网格场景下，kritis-validation-hook组件支持通过配置镜像验签白名单的方式，解决第三方组件自动注入的Sidecar容器的镜像无法通过镜像验签，导致无法部署Pod的问题。组件不会对白名单内的镜像进行签名验证，只会验证未在白名单内的镜像。

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

若您需要把ACK的系统镜像加入白名单，可以通过以下操作配置。

1.  使用以下内容创建`kritis-admission-allowlist-acs.yaml`文件配置白名单。

    ```
    apiVersion: kritis.grafeas.io/v1beta1
    kind: AdmissionAllowlist
    metadata:
      name: allow-acs-images
    spec:
      patterns:
      - namePattern: 'registry*.*.aliyuncs.com/acs/*'
    ```

    namePattern配置项值支持完整匹配，且支持通过星号（\*）字符实现简单的通配符匹配，具体说明如下：

    -   当配置项值不包含星号（\*）字符时，将按配置的值进行完整匹配。例如，`nginx:v0.1.0`只会匹配`nginx:v0.1.0`。
    -   当通过星号（\*）字符实现通配符匹配时，包含以下限制：
        -   星号（\*）位于末尾时，匹配除正斜线（/）外的任意字符。例如，`a.com/nginx*`匹配`a.com/nginx:v0.1.0`，但是不匹配`a.com/nginx/test:v0.1.0` 。
        -   星号（\*）不位于末尾时，匹配字母、数字、短横线\(-\) 以及下划线（\_） 。例如，`registry-vpc.cn-*.aliyuncs.com/acs/pause:3.2`既匹配`registry-vpc.cn-hangzhou.aliyuncs.com/acs/pause:3.2`，也匹配`registry-vpc.cn-beijing.aliyuncs.com/acs/pause:3.2`。
    常见的白名单如下，您可以根据实际需求添加。

    ```
    # 容器服务ACK使用的镜像
    - namePattern: 'registry*.*.aliyuncs.com/acs/*'
    
    # 容器服务ACK使用的镜像（限定中国地域）
    - namePattern: 'registry*.cn-*.aliyuncs.com/acs/*'
    
    # ARMS使用的镜像
    - namePattern: 'registry*.*.aliyuncs.com/arms-docker-repo/*'
    
    # ARMS使用的镜像（限定中国地域）
    - namePattern: 'registry*.cn-*.aliyuncs.com/arms-docker-repo/*'
    ```

2.  执行以下命令配置白名单。

    ```
    kubectl apply -f kritis-admission-allowlist-acs.yaml 
    ```

    预期输出：

    ```
    admissionallowlist.kritis.grafeas.io/allow-acs-images created
    ```

3.  执行以下命令验证白名单配置是否成功。

    ```
    kubectl get admissionallowlists.kritis.grafeas.io
    ```

    预期输出：

    ```
    NAME               AGE
    allow-acs-images   2m22s
    ```


**相关文档**  


[kritis-validation-hook](/intl.zh-CN/产品发布记录/组件介绍与变更记录/安全/kritis-validation-hook.md)

[使用kritis-validation-hook组件实现自动验证容器镜像签名](/intl.zh-CN/Kubernetes集群用户指南/应用/镜像/使用kritis-validation-hook组件实现自动验证容器镜像签名.md)

