Configure pod annotations 
==============================================

When you schedule pods of a Kubernetes cluster to Elastic Container Instance through a virtual node, you can add annotations to the pods to use the features of Elastic Container Instance. When you add annotations, make sure that they comply with Kubernetes semantics. This topic describes the `annotations` that are supported by Elastic Container Instance. 


**Note**

Annotations described in this topic are not applicable to 

**pods that are deployed on** 

virtual nodes. Pods scheduled to Elastic Compute Service (ECS) are not affected by the annotations.

Specify the specification of an elastic container instance 
-------------------------------------------------------------------------------

You can add the `k8s.aliyun.com/eci-use-specs annotation` to specify the specification of an elastic container instance on which you want to deploy pods. You can specify instance specifications in a list and separate them with commas (,). When the quota of a type of instance is insufficient, the system attempts to create instances of the next type. 

You can set k8s.aliyun.com/eci-use-specs to a list of specifications. Separate them with commas (,). Format:

1. Specify the vCPU, memory, and GPU specifications.

* `${cpu}-${mem}Gi`

  For example, 2-4Gi specifies an instance that has 

  `2 vCPUs `

  and 4 GiB of memory. This format specifies instances with general specifications. If you want to specify SSDs and GPUs, you must specify the ECS instance type.
  




Example:




    apiVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
    kind: Deployment
    metadata:
      name: vk-cos-use
      labels:
        app: cos
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: cos
      template:
        metadata:
            annotations: 
                "k8s.aliyun.com/eci-use-specs": "2-4Gi,4-8Gi,ecs.c6.xlarge"
            labels:
                app: cos
        spec:
          containers:
          - name: u1
            image: "registry-vpc.cn-beijing.aliyuncs.com/lxx/cos-4g"
          nodeName: virtual-node-eci-0





2. Specify the ECS instance type, for example, ecs.c6.xlarge. For more information about ECS instance types that are supported by Elastic Container Instance, see [Instance families](/intl.en-US/Instance/Instance families.md). 

    apiVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
    kind: Deployment
    metadata:
      name: vk-cos-use
      labels:
        app: cos
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: cos
      template:
        metadata:
            annotations: 
                "k8s.aliyun.com/eci-use-specs": "ecs.c5.xlarge,ecs.c6.xlarge,ecs.c6.4xlarge"
            labels:
                app: cos
        spec:
          containers:
          - name: u1
            image: "registry-vpc.cn-beijing.aliyuncs.com/lxx/cos-4g"
          nodeName: virtual-node-eci-0



ImageCache 
-------------------------------

When you create a pod, you can use a cached image to accelerate the creation process. For more information, see [Use an image cache CRD to accelerate pod creation]().

Specify the ID of the cached image 
-------------------------------------------------------

When you create a Deployment, you can specify the ID of a cached image to accelerate the creation of the Deployment. 

    apiVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
    kind: Deployment
    metadata:
      name: nginx-with-imagecache
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          annotations: 
              "k8s.aliyun.com/eci-image-snapshot-id": "${your_image_cache_id}"
          labels:
              app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:1.7.9
            imagePullPolicy: IfNotPresent
          nodeName: virtual-node-eci-0



Enable automatic image match 
-------------------------------------------------

When you create a Deployment, you can enable automatic image match. The system selects the most suitable image from cached images to accelerate the creation of the Deployment. 

    apiVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
    kind: Deployment
    metadata:
      name: nginx-dynamic-image-cache
      labels:
        app: nginx-dynamic-image-cache
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
            annotations: 
                "k8s.aliyun.com/eci-image-cache": "true"
            labels:
                app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:latest
            imagePullPolicy: IfNotPresent
          nodeName: virtual-node-eci-0



EIP 
------------------------

You can associate an elastic IP address (EIP) with an elastic container instance to enable Internet access for the elastic container instance. For more information, see [What is an EIP?](/intl.en-US/.md)

Enable automatic EIP creation 
--------------------------------------------------

When you create a Deployment, you can enable automatic EIP creation to create an EIP and associate the EIP with the elastic container instance to run the Deployment. 

The settings of an EIP created in this way are:

* Default bandwidth: **5 Mbit/s** . You can use an annotation to change the bandwidth value.

  




<!-- -->

* Billing method: **pay-as-you-go** .

  



**Note**

* You can use the following annotation to specify the bandwidth value of the EIP: k8s.aliyun.com/eip-bandwidth. The default bandwidth value is 5 Mbit/s.

  

* You can also use the following annotation to add the EIP of an ECS instance to an EIP bandwidth plan: k8s.aliyun.com/eip-common-bandwith-package-id.

  




    apiVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
    kind: Deployment
    metadata:
      name: cos-vk-resource-group-id
      labels:
        app: vk
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
            annotations: 
                "k8s.aliyun.com/eci-with-eip": "true"
                "k8s.aliyun.com/eip-bandwidth": "10"
            labels:
                app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:latest
          nodeName: virtual-node-eci-0



Specify an existing EIP 
--------------------------------------------

When you create a Deployment, you can select an existing EIP and associate the EIP with the elastic container instance to run the Deployment. 

    apiVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
    kind: Deployment
    metadata:
      name: cos-vk-resource-group-id
      labels:
        app: vk
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
            annotations: 
                "k8s.aliyun.com/eci-eip-instanceid": "${your_eip_Instance_Id}"
            labels:
                app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:latest
          nodeName: virtual-node-eci-0



Specify one or more NTP servers 
----------------------------------------------------

You can use an annotation to specify one or more NTP servers for an elastic container instance. For more information, see [Set one or more NTP servers for pods](). 

    apiVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
    kind: Deployment
    metadata:
      name: set-ngnix-ntp
      labels:
        app: vk
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
            annotations: 
                "k8s.aliyun.com/eci-ntp-server": 100.100.5.1,100.100.5.2  # Specify the IP addresses of the NTP servers.
            labels:
                app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:latest
          nodeName: virtual-node-eci-0



Specify the resource group to which an ECI belongs 
-----------------------------------------------------------------------

You can use an annotation to specify the resource group to which an ECI belongs. 

    apiVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
    kind: Deployment
    metadata:
      name: cos-vk-resource-group-id
      labels:
        app: vk
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
            annotations: 
                "k8s.aliyun.com/eci-resource-group-id" : "${your_resource_group_id}"
            labels:
                app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:latest
          nodeName: virtual-node-eci-0



Assign a RAM role to an elastic container instance 
-----------------------------------------------------------------------

You can assign an existing Resource Access Management (RAM) role to an elastic container instance. This allows the elastic container instance to access other Alibaba Cloud services. 

    apiVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
    kind: Deployment
    metadata:
      name: set-ram-role
      labels:
        app: vk
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
            annotations: 
                "k8s.aliyun.com/eci-ram-role-name" : "${your_ram_role_name}"
            labels:
                app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:latest
          nodeName: virtual-node-eci-0



Specify the vSwitch used to create an elastic container instance 
-------------------------------------------------------------------------------------

When you deploy Virtual Kubelet, you can set the `ECI_VSWITCH` environment variable to specify the default vSwitch that is used to create elastic container instances. When you create Deployments on the virtual node, the default vSwitch is used to create elastic container instances. To create an elastic container instance by using another vSwitch in the same virtual private cloud (VPC), you can use an annotation to specify a vSwitch. 

    apiVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
    kind: Deployment
    metadata:
      name: set-vswitch
      labels:
        app: nginx
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
            annotations: 
                "k8s.aliyun.com/eci-vswitch" : "${your_vsw_id}"
            labels:
                app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:latest
          nodeName: virtual-node-eci-0



Configure a security group 
-----------------------------------------------

When you deploy Virtual Kubelet, you can set the `ECI_SECURITY_GROUP` environment variable to specify the default security group that is used to create elastic container instances. When you create Deployments on the virtual node, the default security group is used to create elastic container instances. To create an elastic container instance by using another security group in the same VPC, you can use an annotation to specify a security group. 

    apiVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
    kind: Deployment
    metadata:
      name: set-security-group
      labels:
        app: nginx
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
            annotations: 
                "k8s.aliyun.com/eci-security-group" : "${your_security_group_id}"
            labels:
                app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:latest
          nodeName: virtual-node-eci-0


