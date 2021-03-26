配置ACR企业版免密 
===============================

使用阿里云镜像服务ACR来拉取镜像时，可以配置免密来简化配置，加速镜像拉取。本文将为您介绍如何配置免密拉取ACR企业版镜像。

前提条件 
-------------------------

请确保您已完成以下操作：

* 已开通弹性容器实例服务、容器镜像服务ACR服务，并完成了相关的RAM角色授权。

  

* 已创建ASK集群。具体操作，请参见[创建Serverless Kubernetes集群](/intl.zh-CN/Serverless Kubernetes集群用户指南/集群/创建Serverless Kubernetes集群.md)。

  

* 已创建ACR企业版实例并完成镜像仓库相关配置。具体操作，请参见[使用企业版实例推送拉取镜像]()。

  




背景信息 
-------------------------

阿里云容器镜像服务ACR分为默认版和企业版。其中，企业版是企业级云原生应用制品管理平台，提供容器镜像、Helm Chart ，以及符合OCI规范制品的生命周期管理，能够与容器服务Kubernetes版无缝集成，适用于业务大规模部署场景，帮助企业降低交付复杂度。更多信息，请参见[ACR企业版概述]()。

目前容器镜像服务ACR的镜像使用有如下几种情况：

* 与ECI同一账号下的ACR镜像默认免密，无需上传密码。

  

* 非ACR镜像（Docker镜像）不支持免密，使用OpenAPI创建ECI实例时，您可以通过ImageRegistryCredential参数来上传密码。

  




配置ACR企业版免密访问 
---------------------------------

在[容器镜像服务控制台](https://cr.console.aliyun.com/cn-hangzhou/instances)打开要配置的ACR实例，配置网络访问控制如下：

* 公网访问

  开启公网访问入口后，可以直接通过公网域名地址来访问ACR企业版实例的镜像（可跨域）。具体操作，请参见[配置公网的访问控制]()。

  ![ACR2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7252693161/p240003.png)
  

* 专有网络访问

  使用专有网络VPC访问，需要开启相关授权。具体操作，请参见[配置专有网络的访问控制]()。

  ![ACR1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7252693161/p240004.png)
  




配置完成后，您可以记录实例ID、名称、域名等信息，以便后续配置使用。

使用ACR企业版免密拉取镜像（Kubernetes方式） 
-------------------------------------------------

您可以通过添加Annotation来指定ACR实例，从对应的ACR实例中拉取镜像。
**说明**

Kubernetes方式仅支持指定单个ACR实例。如果您有多个ACR实例，且实例中存在多种不同的镜像，建议您将镜像直接归属到同一个ACR实例。如果您有配置多个ACR实例的需求，您可以使用OpenAPI方式。

示例如下：

1. 准备yaml文件。

   示例test_cri.yaml的内容如下：

       apiVersion: v1
       kind: Pod
       metadata:
         annotations:
           k8s.aliyun.com/acr-instance-id: cri-j36zhodptmyq****      #指定ACR企业版实例ID
         name: cri-test
       spec:
         containers:
         - image: test****-registry.cn-beijing.cr.aliyuncs.com/eci_test/nginx:1.0   #使用公网拉取镜像
           imagePullPolicy: Always
           name: nginx
         restartPolicy: Never

   

2. 创建Pod。

       kubectl apply -f test_cri.yaml

   




使用ACR企业版免密拉取镜像（OpenAPI方式） 
----------------------------------------------

调用CreateContainerGroup接口创建ECI实例时，您可以设置AcrRegistryInfo相关参数来配置免密。相关参数说明如下表所示。更多信息，请参见[CreateContainerGroup]()。


|               名称               |     类型     | 是否必选 |                       示例值                        |                      描述                      |
|--------------------------------|------------|------|--------------------------------------------------|----------------------------------------------|
| AcrRegistryInfo.N.RegionId     | String     | 否    | cn-beijing                                       | ACR企业版实例所属地域。                                |
| AcrRegistryInfo.N.InstanceId   | String     | 否    | cri-nwj395hgf6f3\*\*\*\*                         | ACR企业版实例ID。                                  |
| AcrRegistryInfo.N.Domain.N     | RepeatList | 否    | test\*\*\*\*-registry.cn-beijing.cr.aliyuncs.com | ACR企业版实例的域名。默认为相应实例的所有域名。支持指定个别域名，多个以半角逗号分隔。 |
| AcrRegistryInfo.N.InstanceName | String     | 否    | test\*\*\*\*                                     | ACR企业版实例的名称。                                 |



您可以使用以下几种方式传入AcrRegistryInfo相关参数进行配置：

* 示例一：指定地域、ACR实例ID、名称和域名。

      'Container.1.Image': 'test****-registry.cn-beijing.cr.aliyuncs.com/eci_test/nginx:1.0',
      'Container.1.Name': 'c1',
      'Container.2.Image': 'test****-registry-vpc.cn-beijing.cr.aliyuncs.com/eci_test/nginx:1.0',
      'Container.2.Name': 'c2',
      
      #AcrRegistryInfo
      'AcrRegistryInfo.1.RegionId':'cn-beijing',
      'AcrRegistryInfo.1.InstanceId': 'cri-nwj395hg********',
      'AcrRegistryInfo.1.Domain.1': 'test****-registry-vpc.cn-beijing.cr.aliyuncs.com',
      'AcrRegistryInfo.1.Domain.2': 'test****-registry.cn-beijing.cr.aliyuncs.com'

  

* 示例二：指定ACR实例ID和名称

      'Container.1.Image': 'test****-registry.cn-beijing.cr.aliyuncs.com/eci_test/nginx:1.0',
      'Container.1.Name': 'c1',
      'Container.2.Image': 'test****-registry-vpc.cn-beijing.cr.aliyuncs.com/eci_test/nginx:1.0',
      'Container.2.Name': 'c2',
      
      #AcrRegistryInfo
      'AcrRegistryInfo.1.InstanceId': 'cri-nwj395hg********',
      'AcrRegistryInfo.1.InstanceName': 'test****'

  

* 示例三：仅指定ACR实例ID

      'Container.1.Image': 'test****-registry.cn-beijing.cr.aliyuncs.com/eci_test/nginx:1.0',
      'Container.1.Name': 'c1',
      'Container.2.Image': 'test****-registry-vpc.cn-beijing.cr.aliyuncs.com/eci_test/nginx:1.0',
      'Container.2.Name': 'c2',
      
      #AcrRegistryInfo
      'AcrRegistryInfo.1.InstanceId': 'cri-nwj395hg********'

  




您也可以通过SDK方式进行配置。以下为Python示例：

    #!/usr/bin/env python
    #coding=utf-8
    
    from aliyunsdkcore.client import AcsClient
    from aliyunsdkcore.acs_exception.exceptions import ClientException
    from aliyunsdkcore.acs_exception.exceptions import ServerException
    from aliyunsdkeci.request.v20180808.CreateContainerGroupRequest import CreateContainerGroupRequest
    
    client = AcsClient('<accessKeyId>', '<accessSecret>', 'cn-beijing')
    
    request = CreateContainerGroupRequest()
    request.set_accept_format('json')
    
    request.set_SecurityGroupId("sg-2zeh4cev9y7ulbr*****")
    request.set_VSwitchId("vsw-2zejlv7xjnw61w6z*****")
    request.set_ContainerGroupName("test-cri")
    request.set_Containers([
      {
        "Image": "test****-registry.cn-beijing.cr.aliyuncs.com/eci_test/nginx:1.0",
        "Name": "nginx"
      },
      {
        "Image": "test****-registry-vpc.cn-beijing.cr.aliyuncs.com/eci_test/nginx:1.0",
        "Name": "nginx2"
      }
    ])
    request.set_AcrRegistryInfos([
      {
        "RegionId": "cn-beijing",
        "InstanceId": "cri-nwj395hgf6f*****",
        "Domains": [
          "test****-registry-vpc.cn-beijing.cr.aliyuncs.com",
          "test****-registry.cn-beijing.cr.aliyuncs.com"
        ]
      }
    ])
    
    response = client.do_action_with_exception(request)
    # python2:  print(response) 
    print(str(response, encoding='utf-8'))


