Configure password-free access to pull images from a Container Registry Enterprise Edition instance 
========================================================================================================================

To pull images from a Container Registry instance, you can configure password-free access to the instance to simplify configurations and accelerate the process of pulling images. This topic describes how to configure password-free access to pull images from a Container Registry Enterprise Edition instance. 

Prerequisites 
----------------------------------

The following requirements are met:

* Elastic Container Instance and Container Registry are activated. The required Resource Access Management (RAM) roles are authorized.

  

* A serverless Kubernetes (ASK) cluster is created. For more information, see [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Cluster/Create an ASK cluster.md).

  

* A Container Registry Enterprise Edition instance is created and an image repository is configured. For more information, see [Use a Container Registry Enterprise Edition instance to push and pull images]().

  




Background information 
-------------------------------------------

Container Registry provides Container Registry Enterprise Edition instances and default instances. Container Registry Enterprise Edition is an enterprise-grade platform used to manage the lifecycle of cloud native application artifacts. These artifacts include container images, Helm charts, and Open Container Initiative (OCI) artifacts. Container Registry Enterprise Edition can seamlessly integrate with Container Service for Kubernetes (ACK) in large-scale business deployment scenarios to simplify application delivery for enterprises. For more information, see [What is Container Registry Enterprise Edition?]() 

The following situations exist when you use Container Registry images:

* If a Container Registry image belongs to the same account as the elastic container instance, you can pull this image without a password.

  

* If an image is a Docker image instead of a Container Registry image, you cannot pull the image without a password. When you call an API operation to create an elastic container instance, you can use the ImageRegistryCredential parameter to pass in a password.

  




Configure password-free access to a Container Registry Enterprise Edition instance 
-------------------------------------------------------------------------------------------------------

In the [Container Registry Enterprise Edition](https://cr.console.aliyun.com/cn-hangzhou/instances) console, find the instance to which you want to configure password-free access and configure the following network access control settings:

* Settings for access over the Internet

  After you enable Internet access, you can access images in the Container Registry Enterprise Edition instance across regions by using public domain names. For more information, see [Configure access over the Internet](). 

  ![ACR2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3014617161/p240003.png)
  

* Settings for access over virtual private clouds (VPCs)

  You must grant required permissions before you can allow access to the Container Registry Enterprise Edition instance over VPCs. For more information, see [Configure access over VPCs](). 

  ![ACR1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4014617161/p240004.png)
  




After you configure the Container Registry Enterprise Edition instance, you can record the instance information such as the instance ID, instance name, and domain name for subsequent use.

Pull images from Container Registry Enterprise Edition instances without passwords (Kubernetes) 
--------------------------------------------------------------------------------------------------------------------

You can add annotations to specify a Container Registry Enterprise Edition instance from which you want to pull images. 
**Note**

You can specify a single Container Registry Enterprise Edition instance when you use the Kubernetes method. If you have multiple Container Registry Enterprise Edition instances that contain different images, we recommend that you put the images into a single Container Registry Enterprise Edition instance. If you want to configure multiple Container Registry Enterprise Edition instances, we recommend that you call an API operation.

Example:

1. Prepare the YAML file. 

   The following sample code provides an example of test_cri.yaml:

       apiVersion: v1
       kind: Pod
       metadata:
         annotations:
           k8s.aliyun.com/acr-instance-id: cri-j36zhodptmyq****      # Specify the ID of the Container Registry Enterprise Edition instance.
         name: cri-test
       spec:
         containers:
         - image: test****-registry.cn-beijing.cr.aliyuncs.com/eci_test/nginx:1.0   # Pull an image over the Internet.
           imagePullPolicy: Always
           name: nginx
         restartPolicy: Never

   

2. Create a pod. 

       kubectl apply -f test_cri.yaml

   




Pull images from Container Registry Enterprise Edition instances without passwords (API operation) 
-----------------------------------------------------------------------------------------------------------------------

When you call the CreateContainerGroup operation to create an elastic container instance, you can use AcrRegistryInfo parameters to configure password-free access. The following table describes the parameters. For more information, see [CreateContainerGroup](). 
**Note**

When you use AcrRegistryInfo parameters to configure password-free access, you must specify the AcrRegistryInfo.N.InstanceId parameter.


|           Parameter            |    Type    |                     Example                      |                                                                                                Description                                                                                                |
|--------------------------------|------------|--------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| AcrRegistryInfo.N.RegionId     | String     | cn-beijing                                       | The region ID of the Container Registry Enterprise Edition instance.                                                                                                                                      |
| AcrRegistryInfo.N.InstanceId   | String     | cri-nwj395hgf6f3\*\*\*\*                         | The ID of the Container Registry Enterprise Edition instance.                                                                                                                                             |
| AcrRegistryInfo.N.Domain.N     | RepeatList | test\*\*\*\*-registry.cn-beijing.cr.aliyuncs.com | Domain name N of the Container Registry Enterprise Edition instance. The default value is all domain names of the instance. You can specify domain names. Separate multiple domain names with commas (,). |
| AcrRegistryInfo.N.InstanceName | String     | test\*\*\*\*                                     | The name of the Container Registry Enterprise Edition instance.                                                                                                                                           |



The following examples demonstrate how to specify AcrRegistryInfo parameters:

* Example 1: Specify the region ID, ID, name, and domain names of the Container Registry Enterprise Edition instance. 

      'Container.1.Image': 'test****-registry.cn-beijing.cr.aliyuncs.com/eci_test/nginx:1.0',
      'Container.1.Name': 'c1',
      'Container.2.Image': 'test****-registry-vpc.cn-beijing.cr.aliyuncs.com/eci_test/nginx:1.0',
      'Container.2.Name': 'c2',
      
      #AcrRegistryInfo
      'AcrRegistryInfo.1.RegionId':'cn-beijing',
      'AcrRegistryInfo.1.InstanceId': 'cri-nwj395hg********',
      'AcrRegistryInfo.1.Domain.1': 'test****-registry-vpc.cn-beijing.cr.aliyuncs.com',
      'AcrRegistryInfo.1.Domain.2': 'test****-registry.cn-beijing.cr.aliyuncs.com'

  

* Example 2: Specify the ID and name of the Container Registry Enterprise Edition instance.

      'Container.1.Image': 'test****-registry.cn-beijing.cr.aliyuncs.com/eci_test/nginx:1.0',
      'Container.1.Name': 'c1',
      'Container.2.Image': 'test****-registry-vpc.cn-beijing.cr.aliyuncs.com/eci_test/nginx:1.0',
      'Container.2.Name': 'c2',
      
      #AcrRegistryInfo
      'AcrRegistryInfo.1.InstanceId': 'cri-nwj395hg********',
      'AcrRegistryInfo.1.InstanceName': 'test****'

  

* Example 3: Specify only the ID of the Container Registry Enterprise Edition instance.

      'Container.1.Image': 'test****-registry.cn-beijing.cr.aliyuncs.com/eci_test/nginx:1.0',
      'Container.1.Name': 'c1',
      'Container.2.Image': 'test****-registry-vpc.cn-beijing.cr.aliyuncs.com/eci_test/nginx:1.0',
      'Container.2.Name': 'c2',
      
      #AcrRegistryInfo
      'AcrRegistryInfo.1.InstanceId': 'cri-nwj395hg********'

  




You can also use SDKs to specify AcrRegistryInfo parameters. The following sample code provides an example on how to use SDK for Python to specify AcrRegistryInfo parameters:

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


