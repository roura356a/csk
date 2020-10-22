# Pod annotations supported by ECI

When you schedule pods of a Kubernetes cluster to Elastic Container Instance \(ECI\) through a virtual node, you can add annotations to the pods to use the various features of ECI. When you add annotations, make sure that they comply with Kubernetes semantics. This topic describes the annotations supported by ECI.``

**Note:** These annotations only take effect for the pods to be scheduled to a **virtual node**. They are invalid for the pods to be scheduled to Elastic Compute Service \(ECS\) instances.

## Specify the specifications of an ECI

You can add the `k8s.aliyun.com/eci-use-specs` annotation to specify the specifications of the ECI to be created for running a pod. You can specify the specifications in a list and separate them with commas \(,\). Each element in the list represents a set of specifications. When instances of the specifications specified by an element are out of stock, the specifications specified by the next element are used.

You can specify each element in the list in one of the following ways.

1. Specify the CPU, memory, and GPU specifications.

-   `${cpu}-${mem}Gi`: specifies the CPU and memory specifications. For example, 2-4Gi indicates that the ECI to be created has `2 vCPUs and 4 GB memory`.
-   `ecigpu-${gpuType}-${gpuCount}`: specifies the GPU specifications. Example: ecigpu-P100-4. **ecigpu** specifies that a GPU instance is to be created. **P100** specifies the GPU type. **4** specifies the number of GPUs that you want to request for the ECI. The following table describes the supported values of gpuType and gpuCount and the mapped ECS instances types.

|gpuType|gpuCount|ECS instance type|
|-------|--------|-----------------|
|P4|1|ecs.gn5i-c2g1.large|
|P4|2|ecs.gn5i-c16g1.8xlarge|
|P4|4|ecs.gn5i-c28g1.14xlarge|
|V100|1|ecs.gn6v-c8g1.2xlarge|
|V100|4|ecs.gn6v-c8g1.8xlarge|
|V100|8|ecs.gn6v-c8g1.16xlarge|
|P100|1|ecs.gn5-c8g1.2xlarge|
|P100|2|ecs.gn5-c8g1.4xlarge|
|P100|4|ecs.gn5-c8g1.8xlarge|
|P100|8|ecs.gn5-c8g1.14xlarge|

2. Specify the exact ECS instance type, for example, ecs.c6.xlarge. For more information about ECS instance types supported by ECI, see [Instance families](/intl.en-US/Instance/Instance families.md).

```
apiVersion: apps/v1beta2 # For versions earlier than 1.8.0, use apps/v1beta1.
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
            "k8s.aliyun.com/eci-use-specs": "64-512Gi, 2-4Gi,ecs.c6.xlarge,ecigpu-P100-4"
        labels:
            app: cos
    spec:
      containers:
      - name: u1
        image: "registry-vpc.cn-beijing.aliyuncs.com/lxx/cos-4g"
      nodeName: virtual-node-eci-0
```

## Specify an image cache to accelerate pod creation

When you create a pod, you can use an image cache to accelerate pod creation. For more information, see [Use an image cache CRD to accelerate pod creation]().

## Specify an existing image cache

When you create a deployment, you can specify an existing image cache to accelerate deployment creation.

```
apiVersion: apps/v1beta2 # For versions earlier than 1.8.0, use apps/v1beta1.
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
```

## Enable automatic image cache match

When you create a deployment, you can enable automatic image cache match. The system selects the optimal image cache from existing image caches to accelerate deployment creation.

```
apiVersion: apps/v1beta2 # For versions earlier than 1.8.0, use apps/v1beta1.
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
```

## Associate an EIP with an ECI

You can associate an Elastic IP Address \(EIP\) with an ECI to enable Internet access for the ECI. For more information, see [What are Elastic IP Addresses?](/intl.en-US/.md)

## Enable automatic EIP creation

When you create a deployment, you can enable automatic EIP creation to create an EIP and associate the EIP with the ECI for running the deployment.

The settings of an EIP created in this way are as follows:

-   Default bandwidth: **5 Mbit/s**. You can change the bandwidth through an annotation.
-   Billing method: **pay-as-you-go**.

```
apiVersion: apps/v1beta2 # For versions earlier than 1.8.0, use apps/v1beta1.
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
```

## Specify an existing EIP

When you create a deployment, you can select an existing EIP and associate the EIP with the ECI for running the deployment.

```
apiVersion: apps/v1beta2 # For versions earlier than 1.8.0, use apps/v1beta1.
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
```

## Specify one or more NTP servers

You can specify one or more NTP servers for an ECI through an annotation. For more information, see [Set one or more NTP servers for pods]().

```
apiVersion: apps/v1beta2 # For versions earlier than 1.8.0, use apps/v1beta1.
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
            "k8s.aliyun.com/eci-ntp-server": 100.100.5.1,100.100.5.2  # Specify the IP addresses of your NTP servers.
        labels:
            app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
      nodeName: virtual-node-eci-0
```

## Specify the resource group to which an ECI belongs

You can specify the resource group to which an ECI belongs through an annotation.

```
apiVersion: apps/v1beta2 # For versions earlier than 1.8.0, use apps/v1beta1.
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
```

## Assign a RAM role to an ECI

You can assign an existing Resource Access Management \(RAM\) role to an ECI to allow the ECI to access other Alibaba Cloud services.

```
apiVersion: apps/v1beta2 # For versions earlier than 1.8.0, use apps/v1beta1.
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
```

## Specify the VSwitch used to create an ECI

When you deploy Virtual Kubelet, you can set the `ECI_VSWITCH` environment variable to specify the default VSwitch used to create ECIs. When you create deployments on the virtual node, the default VSwitch is used to create ECIs. To create an ECI by using another VSwitch in the same Virtual Private Cloud \(VPC\), you can specify the VSwitch through an annotation.

```
apiVersion: apps/v1beta2 # For versions earlier than 1.8.0, use apps/v1beta1.
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
```

## Specify the security group used to create an ECI

When you deploy Virtual Kubelet, you can set the `ECI_SECURITY_GROUP` environment variable to specify the default security group used to create ECIs. When you create deployments on the virtual node, the default security group is used to create ECIs. To create an ECI by using another security group in the same VPC, you can specify the security group through an annotation.

```
apiVersion: apps/v1beta2 # For versions earlier than 1.8.0, use apps/v1beta1.
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
```

