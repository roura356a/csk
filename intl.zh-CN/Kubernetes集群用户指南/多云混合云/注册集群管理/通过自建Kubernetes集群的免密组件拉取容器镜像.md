---
keyword: [自建Kubernetes集群, 免密组件, 拉取容器镜像]
---

# 通过自建Kubernetes集群的免密组件拉取容器镜像

通过aliyun-acr-credential-helper组件可以在自建Kubernetes集群中免密拉取ACR个人版或企业版私有镜像。本文列举两个场景介绍如何使用免密组件拉取私有镜像。

-   您需要创建一个注册集群，并将自建Kubernetes集群接入注册集群。具体操作，请参见[创建注册集群并接入本地数据中心集群](/intl.zh-CN/Kubernetes集群用户指南/多云混合云/注册集群管理/创建注册集群并接入本地数据中心集群.md)。
-   通过kubectl连接注册集群。具体操作，请参见[通过kubectl管理Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl管理Kubernetes集群.md)。

## 使用限制

使用免密组件涉及的镜像及集群限制如下：

-   镜像
    -   支持拉取容器镜像服务企业版实例和个人版实例中的私有镜像。
    -   支持拉取集群当前用户容器镜像服务中的私有镜像，通过跨账号授权或AccessKey ID和AccessKey Secret配置可以拉取其他用户的私有镜像。
    -   支持跨地域拉取容器镜像服务中的私有镜像。
-   集群
    -   支持集群多命名空间免密拉取。
    -   集群版本不低于1.11.2。否则，请您手动升级。具体操作，请参见[升级集群](/intl.zh-CN/Kubernetes集群用户指南/集群/升级集群/升级集群.md)。

## 注意事项

-   在Kubernetes资源（例如无状态应用Deployment）模板中配置拉取凭证（imagePullSecret）会导致免密组件失效，如果需使用免密组件，请避免手工配置拉取凭证（imagePullSecret）。
-   如果部署的Kubernetes资源（例如无状态应用Deployment）使用了自定义的ServiceAccount，需先调整免密组件配置文件中Service-Account字段，使其作用于自定义的ServiceAccount，再进行部署资源操作。
-   在集群中创建新的Service Account一段时间后，免密插件根据ACK集群默认权限生成的ACR私有镜像拉取Token才会更新到应用使用到的Service Account中， 使用Service Account的应用才会使用Token去拉取镜像。如果创建完Service Account之后立即创建应用则会出现因鉴权失败无法拉取的情况。
-   免密插件默认覆盖ACK中所有命名空间中默认的ServiceAccount中的imagePullSecret字段。被覆盖的 ServiceAccount会随着对应kube-system命名空间中**acr-configuration**配置项中的service-account字段变动而变动。
-   在修改kube-system命名空间中的**acr-configuration**配置项时，请确认缩进是否与给出的场景的例子相同。建议直接复制对应场景的YAML内容到编辑器中，修改对应的值然后直接应用到集群，以保证YAML格式的正确性。

## 在注册集群中配置免密组件

**步骤一：为免密组件配置RAM权限**

在注册集群中安装组件前，您需要在接入集群中设置AccessKey用来访问云服务的权限。设置AccessKey前，您需要创建RAM用户并为其添加访问相关云资源的权限。

1.  创建RAM用户。具体操作，请参见[创建RAM用户](/intl.zh-CN/用户管理/基本操作/创建RAM用户.md)。

2.  创建权限策略。具体操作，请参见[创建自定义策略](/intl.zh-CN/权限策略管理/自定义策略/创建自定义策略.md)。

    权限策略模板如下所示：

    ```
    {
        "Version": "1",
        "Statement": [
            {
                "Action": [
                    "cr:GetAuthorizationToken",
                    "cr:ListInstanceEndpoint",
                    "cr:PullRepository"
                ],
                "Resource": [
                    "*"
                ],
                "Effect": "Allow"
            }
        ]
    }
    ```

3.  为RAM用户添加权限。具体操作，请参见[为RAM用户授权](/intl.zh-CN/用户管理/授权管理/为RAM用户授权.md)。

4.  为RAM用户创建AccessKey。具体操作，请参见[获取AccessKey]()。

5.  使用AccessKey在注册集群中创建名为alibaba-addon-secret的Secret资源。

    安装免密组件时将自动引用此AccessKey访问对应的云服务资源。

    ```
    kubectl -n kube-system create secret generic alibaba-addon-secret --from-literal='access-key-id=<your access key id>' --from-literal='access-key-secret=<your access key secret>'
    ```

    **说明：** `<your access key id>`及`<your access key secret>`为上一步获取的AccessKey信息。


**步骤二：升级并配置免密组件**

在使用免密组件拉取镜像前，您可能需要升级并配置组件。

1.  升级aliyun-acr-credential-helper组件。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在控制台左侧导航栏中，单击**集群**。

    3.  在**集群列表**页面，单击目标集群操作列下的**更多** \> **系统组件管理**。

    4.  在**安全**区域，找到**aliyun-acr-credential-helper**，单击**升级**。

2.  设置acr-configuration配置项。

    **方式一：通过控制台方式设置acr-configuration配置项。**

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在控制台左侧导航栏中，单击**集群**。

    3.  在**集群列表**页面，单击目标集群下的**详情**。

    4.  在**集群信息**页面左侧导航栏，选择**配置管理** \> **配置项**。

    5.  在**配置项**页面的上方命名空间下拉框中，选择**kube-system**，然后找到配置项**acr-configuration**，然后通过以下两种方式配置acr-configuration。

        -   方式一：单击其右侧的**编辑**，设置**配置项**键和值。

            如果您没有**acr-configuration**配置项，请参见[创建配置项](/intl.zh-CN/Kubernetes集群用户指南/应用/配置项及保密字典/管理配置项.md)。关于如何更新配置项，请参见[修改配置项](/intl.zh-CN/Kubernetes集群用户指南/应用/配置项及保密字典/管理配置项.md)。

        -   方式二：单击其右侧的**YAML编辑**，设置**配置项**键和值。
        acr-configuration配置项的键和值说明如下。

        |配置项键|配置项键说明|配置项值|
        |----|------|----|
        |service-account|使免密组件作用于指定的服务账号。|默认为**Default**。**说明：** 如果要配置多个请以逗号分隔， 如果设置为“\*”， 表示支持所有命名空间下的所有ServiceAccount。 |
        |acr-registry-info|容器镜像的实例信息数组，YAML多行字符串格式，每个实例以三元组方式配置。**说明：** 实例信息三元组：

        -   instanceId：实例ID，企业版实例必须配置此项。
        -   regionId：可选，默认为本地地域。
        -   domains：可选，默认为相应实例的所有域名。若要指定个别域名，多个以逗号分隔。
|默认值为空，表示免密拉取本地地域的默认容器镜像实例仓库。针对企业版容器镜像实例，配置示例如下：

        ```
- instanceId: cri-xxx
  regionId: cn-hangzhou
  domains: xxx.com,yyy.com
        ```

针对个人版容器镜像实例，配置示例如下：

        ```
- instanceId: ""
  regionId: cn-hangzhou
  domains: xxx.com,yyy.com
        ``` |
        |watch-namespace|期望能免密拉取镜像的Namespace。|默认值为**Default**。**说明：** 当取值为ALL时，表示期望所有Namespace都能免密拉取。如果需要配置多个Namespace时，以逗号分隔。 |
        |expiring-threshold|本地Cache Token过期阈值。|默认值为**15m**（建议使用15 min）。|

    **方式二：通过kubectl命令行的方式设置acr-configuration配置项。**

    1.  执行以下命令打开acr-configuration配置项的编辑页。

        ```
        kubectl edit cm acr-configuration -n kube-system
        ```

    2.  根据实际情况设置acr-configuration配置项的值。

        企业版和个人版容器镜像实例下设置acr-configuration配置项的示例如下：

        -   企业版

            ```
            apiVersion: v1
            data:
              acr-api-version: "2018-12-01"
              acr-registry-info: |-
                - instanceId: "cri-xxx"
                  regionId: "cn-hangzhou"
              expiring-threshold: 15m
              service-account: default
              watch-namespace: all
            kind: ConfigMap
            metadata:
             name: acr-configuration
              namespace: kube-system
             selfLink: /api/v1/namespaces/kube-system/configmaps/acr-configuration
            ```

        -   个人版

            ```
            apiVersion: v1
            data:
              acr-api-version: "2018-12-01"
              acr-registry-info: |-
                - instanceId: ""
                    regionId: "cn-hangzhou"
              expiring-threshold: 15m
              service-account: default
              watch-namespace: all
            kind: ConfigMap
            metadata:
             name: acr-configuration
             namespace: kube-system
             selfLink: /api/v1/namespaces/kube-system/configmaps/acr-configuration
            ```


## 场景一：拉取个人版实例和企业实例的私有镜像

注册集群支持同时拉取企业版和个人版的私有镜像，只拉取企业版的私有镜像，只拉取个人版的私有镜像。根据您的使用场景，按照以下方式修改配置项**acr-configuration**中的configMap。具体操作，请参见[配置组件](/intl.zh-CN/Kubernetes集群用户指南/多云混合云/注册集群管理/通过自建Kubernetes集群的免密组件拉取容器镜像.md)。配置内容如下：

-   配置拉取企业版的私有镜像。

    ```
    data:    
        service-account: "default"    
        watch-namespace: "all"    
        expiring-threshold: "15m"    
        notify-email: "cs@aliyuncs.com"    
        acr-registry-info: |      
          - instanceId: "cri-xxx"
            regionId: "cn-hangzhou"         
            domains: "xxx.com","yyy.com"
    ```

-   配置拉取个人版的私有镜像。

    ```
    data:     
        service-account: "default"
        watch-namespace: "all"
        expiring-threshold: "15m"
        notify-email: "cs@aliyuncs.com"
        acr-registry-info: |
          - instanceId: ""
            regionId: "cn-hangzhou"
            domains: "xxx.com","yyy.com"
    ```

-   配置同时拉取个人版和企业版的私有镜像。

    ```
    data:
        service-account: "default"
        watch-namespace: "all"
        expiring-threshold: "15m"
        notify-email: "cs@aliyuncs.com"
        acr-registry-info: |
          - instanceId: ""
          - instanceId: "cri-xxxx"
    ```


## 场景二：配置跨地域拉取镜像权限

如果需要拉取的镜像与当前注册集群不属于同一地域的时候，需要修改配置项**acr-configuration**中的configMap。

例如，个人版的镜像仓库同时拉取北京地域与杭州地域的镜像，配置如下。具体操作，请参见[配置组件](/intl.zh-CN/Kubernetes集群用户指南/多云混合云/注册集群管理/通过自建Kubernetes集群的免密组件拉取容器镜像.md)。

```
data:
    service-account: "default"
    watch-namespace: "all"
    expiring-threshold: "15m"
    notify-email: "cs@aliyuncs.com"
    acr-registry-info: |
      - instanceId: ""
        regionId: cn-beijing
      - instanceId: ""
        regionId: cn-hangzhou            
```

