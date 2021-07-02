Pod annotations supported by Elastic Container Instance 
============================================================================

When you use a virtual node to schedule pods of a Kubernetes cluster to Elastic Container Instance, you can add annotations to the pods to make full use of the Elastic Container Instance features. Make sure that the annotations that you want to add comply with the Kubernetes syntax. This topic describes the annotations supported by Elastic Container Instance and provides examples on how to configure the annotations. 

The following table describes the annotations supported by Elastic Container Instance. 
**Note**

The annotations described in the following table are applicable only to the pods that are scheduled to Elastic Container Instance by using virtual nodes. These pods are elastic container instances. These annotations cannot be applied to the pods that are scheduled to Elastic Compute Service (ECS).


|                   Annotation                   |            Example             |                                                                                                                                                                          Description                                                                                                                                                                          |                                                        References                                                        |
|------------------------------------------------|--------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------|
| k8s.aliyun.com/eci-security-group              | sg-bp1dktddjsg5nktv\*\*\*\*    | The ID of the security group.                                                                                                                                                                                                                                                                                                                                 | None. See the sections that follow.                                                                                      |
| k8s.aliyun.com/eci-resource-group-id           | rg-acfmzvwytch\*\*\*\*         | The ID of the resource group.                                                                                                                                                                                                                                                                                                                                 | None. See the sections that follow.                                                                                      |
| k8s.aliyun.com/eci-vswitch                     | vsw-bp1xpiowfm5vo8o3c\*\*\*\*  | The IDs of vSwitches. You can specify multiple vSwitches to specify multiple zones.                                                                                                                                                                                                                                                                           | [Specify multiple zones to create an elastic container instance]()                                       |
| k8s.aliyun.com/eci-schedule-strategy           | VSwitchOrdered                 | The multi-zone scheduling policy. Valid values: * VSwitchOrdered: Resources in the specified zones are scheduled in the order that the vSwitches are specified.   * VSwitchRandom: Resources in the specified zones are scheduled at random.               | [Specify multiple zones to create an elastic container instance]()                                       |
| k8s.aliyun.com/eci-ram-role-name               | AliyunECIContainerGroupRole    | The Resource Access Management (RAM) role that grants Elastic Container Instance permissions to access other Alibaba Cloud services.                                                                                                                                                                                                                          | [Authorize a RAM role]()                                                                                 |
| k8s.aliyun.com/eci-use-specs                   | 2-4Gi,4-8Gi,ecs.c6.xlarge      | The elastic container instance types. You can specify multiple elastic container instance types. An elastic container instance type can be a combination of vCPUs and memory or an ECS instance type.                                                                                                                                                         | [Specify multiple instance types to create an elastic container instance]()                              |
| k8s.aliyun.com/eci-spot-strategy               |  SpotAsPriceGo | The bidding policy for the preemptible instance. Valid values: * SpotAsPriceGo: The system places bids based on the spot price.   * SpotWithPriceLimit: You must set the maximum hourly price that you are willing to pay for the preemptible instance.    | [Create a preemptible instance]()                                                                        |
| k8s.aliyun.com/eci-spot-price-limit            | 0.5                            | The maximum hourly price of the preemptible instance. This parameter is valid only when k8s.aliyun.com/eci-spot-strategy is set to SpotWithPriceLimit.                                                                                                                                                                                                        | [Create a preemptible instance]()                                                                        |
| k8s.aliyun.com/eci-cpu-option-core             | 2                              | The number of physical CPU cores.                                                                                                                                                                                                                                                                                                                             | [Customize CPU options]()                                                                                |
| k8s.aliyun.com/eci-cpu-option-ht               | 1                              | The number of threads per core.                                                                                                                                                                                                                                                                                                                               | [Customize CPU options]()                                                                                |
| k8s.aliyun.com/eci-reschedule-enable           | "true"                         | Specifies whether to enable rescheduling for elastic container instances.                                                                                                                                                                                                                                                                                     | None. See the sections that follow.                                                                                      |
| k8s.aliyun.com/pod-fail-on-create-err          | "true"                         | Specifies whether to put the elastic container instances that cannot be created into the Failed state.                                                                                                                                                                                                                                                        | None. See the sections that follow.                                                                                      |
| k8s.aliyun.com/eci-image-snapshot-id           | imc-2zebxkiifuyzzlhl\*\*\*\*   | The ID of the image cache.  **Note** You can manually specify image caches or enable automatic image cache match. We recommend that you enable automatic image cache match.                                                                                                                                                                   | [Use an image cache CRD to accelerate pod creation]()                                                    |
| k8s.aliyun.com/eci-image-cache                 | "true"                         | Specifies whether to enable automatic image cache match.  **Note** You can manually specify image caches or enable automatic image cache match. We recommend that you enable automatic image cache match.                                                                                                                                     | [Use an image cache CRD to accelerate pod creation]()                                                    |
| k8s.aliyun.com/acr-instance-id                 | cri-j36zhodptmyq\*\*\*\*       | The ID of the Container Registry Enterprise Edition instance.                                                                                                                                                                                                                                                                                                 | [Configure password-free settings to pull images from Container Registry Enterprise Edition instances]() |
| k8s.aliyun.com/eci-eip-instanceid              | eip-bp1q5n8cq4p7f6dzu\*\*\*\*  | The ID of the elastic IP address (EIP).                                                                                                                                                                                                                                                                                                                       | [Bind an EIP to a pod]()                                                                                 |
| k8s.aliyun.com/eci-with-eip                    | "true"                         | Specifies whether to automatically create and associate an EIP.                                                                                                                                                                                                                                                                                               | [Bind an EIP to a pod]()                                                                                 |
| k8s.aliyun.com/eip-bandwidth                   | 5                              | The bandwidth value for the EIP.                                                                                                                                                                                                                                                                                                                              | [Bind an EIP to a pod]()                                                                                 |
| k8s.aliyun.com/eip-common-bandwidth-package-id | cbwp-2zeukbj916scmj51m\*\*\*\* | The ID of the EIP bandwidth plan.                                                                                                                                                                                                                                                                                                                             | [Bind an EIP to a pod]()                                                                                 |
| k8s.aliyun.com/eip-isp                         | BGP                            | The line type for the EIP. This parameter is applicable only to pay-as-you-go EIPs. Valid values: * BPG: BGP (Multi-ISP) line   * BGP_PRO: BGP (Multi-ISP) Pro line                                                                                        | [Bind an EIP to a pod]()                                                                                 |
| k8s.aliyun.com/eip-internet-charge-type        | PayByBandwidth                 | The metering method of the EIP. Valid values: * PayByBandwidth: pay-by-bandwidth   * PayByTraffic: pay-by-data-transfer                                                                                                                                    | [Bind an EIP to a pod]()                                                                                 |
| kubernetes.io/ingress-bandwidth                | 40M                            | The inbound bandwidth.                                                                                                                                                                                                                                                                                                                                        | [Limit the bandwidth of an elastic container instance]()                                                 |
| kubernetes.io/egress-bandwidth                 | 20M                            | The outbound bandwidth.                                                                                                                                                                                                                                                                                                                                       | [Limit the bandwidth of an elastic container instance]()                                                 |
| k8s.aliyun.com/eci-extra-ephemeral-storage     | 50Gi                           | The size of the temporary storage space.                                                                                                                                                                                                                                                                                                                      | [Create a custom-sized temporary storage space]()                                                        |
| k8s.aliyun.com/eci-core-pattern                | /pod/data/dump/core            | The directory where core dump files are stored.                                                                                                                                                                                                                                                                                                               | [View core dump files]()                                                                                 |
| k8s.aliyun.com/eci-ntp-server                  | 100.100.\*.\*                  | The IP address of the Network Time Protocol (NTP) server.                                                                                                                                                                                                                                                                                                     | [Configure an NTP server for pods]()                                                     |



Configure a security group and a resource group for a pod 
------------------------------------------------------------------------------

When Virtual Kubelet starts, it uses environment variables to configure a default security group and resource group. By default, all the pods created on each virtual node use the security group and resource group configured by Virtual Kubelet. You can also add annotations to specify a security group and a resource group for a pod based on your needs. 

The following sample code provides an example on how to add annotations to specify a security group and a resource group for a pod:

    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: demo
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
    
                k8s.aliyun.com/eci-security-group: "sg-bp1dktddjsg5nktv****"      #Specify a security group.
                k8s.aliyun.com/eci-resource-group-id: "rg-acfmzvwytch****"    #Specify a resource group.
            labels:
                app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:latest
          nodeName: virtual-kubelet



Specify multiple zones to create a pod 
-----------------------------------------------------------

When you create a pod, you can specify multiple zones by specifying multiple vSwitches. The system then selects a zone based on resource availability to create the pod. For more information, see [Specify multiple zones to create an elastic container instance](). 

The following sample code provides an example on how to add annotations to specify multiple zones:

    apiVersion: v1
    kind: Pod
    metadata:
      annotations:
        k8s.aliyun.com/eci-vswitch: "vsw-bp1xpiowfm5vo8o3c****,vsw-bp1rkyjgr1xwoho6k****"    #Specify multiple vSwitch IDs.
        k8s.aliyun.com/eci-schedule-strategy: "VSwitchOrdered"   #Specify the multi-zone scheduling policy.
      name: nginx-test
    spec:
      containers:
      - name: nginx
        image: nginx:latest



Configure a RAM role 
-----------------------------------------

You can add an annotation to configure a RAM role for a pod. The role grants the pod permissions to access Alibaba Cloud services. 
**Notice**

Make sure that the configured RAM role can be assumed by ECS.

The following sample code provides an example on how to add an annotation to configure a RAM role for a pod:

    apiVersion: apps/v1
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
                k8s.aliyun.com/eci-ram-role-name : "AliyunECIContainerGroupRole"   #Specify a RAM role.
            labels:
                app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:latest
          nodeName: virtual-kubelet



Specify one or more instance types to create a pod 
-----------------------------------------------------------------------

You can add an annotation to specify instance types that can be used to create a pod. If resources of a specified instance type are insufficient, the system sequentially traverses other specified instance types to find one that provides sufficient resources to create the pod. For more information, see [Specify multiple instance types to create an elastic container instance](). 
**Note**

An elastic container instance type can be a combination of vCPUs and memory or an ECS instance type. You can specify special ECS instance types such as GPU-accelerated ECS instances types, ECS instance types with high clock speeds, and ECS instance types equipped with local disks based on your needs. For more information, see [Specify an ECS instance type to create an elastic container instance]().

The following sample code provides an example on how to add an annotation to specify one or more instance types:

    apiVersion: apps/v1
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
                "k8s.aliyun.com/eci-use-specs": "2-4Gi,4-8Gi,ecs.c6.xlarge" #Specify one or more instance types. An elastic container instance type can be a combination of vCPUs and memory or an ECS instance type.
            labels:
                app: cos
        spec:
          containers:
          - name: u1
            image: "registry-vpc.cn-beijing.aliyuncs.com/lxx/cos-4g"
          nodeName: virtual-kubelet



Create a preemptible instance 
--------------------------------------------------

You can run stateless applications and jobs on preemptible instances to reduce costs. You can add annotations to create a preemptible instance. For more information, see [Create a preemptible instance](). 

The following sample code provides an example on how to add annotations to create a preemptible instance:

    apiVersion: apps/v1 # for versions before 1.8.0 use apps/v1beta1
    kind: Deployment
    metadata:
      name: nginx-deployment-basic
      labels:
        app: nginx
    spec:
      replicas: 2
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          labels:
            app: nginx
          annotations:
            k8s.aliyun.com/eci-use-specs : "ecs.c5.large"  #Specify an ECS instance type.
            k8s.aliyun.com/eci-spot-strategy: "SpotWithPriceLimit"  #Set k8s.aliyun.com/eci-spot-strategy to SpotWithPriceLimit.
            k8s.aliyun.com/eci-spot-price-limit: "0.250"   #Specify a maximum hourly price for the preemptible instance.
        spec:
        #  nodeSelector:
        #    env: test-team
          containers:
          - name: nginx
            image: nginx:1.7.9 # replace it with your exactly <image_name:tags>
            ports:
            - containerPort: 80



Customize CPU options 
------------------------------------------

The CPU options of an elastic container instance include the number of physical CPU cores and the number of threads per core. An elastic container instance may support custom CPU options. This is determined based on how the instance is created. For more information, see [Customize CPU options](). 

The following sample code provides an example on how to add annotations to customize CPU options:

    apiVersion: v1
    kind: Pod
    metadata:
      annotations:
        k8s.aliyun.com/eci-use-specs : "ecs.c6.2xlarge"    #Specify an ECS instance type that supports custom CPU options.
        k8s.aliyun.com/eci-cpu-option-core: 2                 #Set the number of physical CPU cores to 2.
        k8s.aliyun.com/eci-cpu-option-ht: 1                    #Set the number of threads per core to 1 to disable Hyper-Threading.
      name: nginx-test
    spec:
      containers:
      - name: nginx
        image: nginx:latest
      restartpolicy: Always



Configure rescheduling for elastic container instances 
---------------------------------------------------------------------------

Pods may fail to be scheduled to virtual nodes. You can add an annotation to enable rescheduling for pods. This ensures that the system continues to retry to schedule pods instead of returning failures even if the asynchronous scheduling fails. 

The following sample code provides an example on how to add an annotation to enable rescheduling for pods:

    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: set-eci
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
                k8s.aliyun.com/eci-reschedule-enable: "true"    #Enable rescheduling for elastic container instances.
            labels:
                app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:latest
          nodeName: virtual-kubelet



Put the pods that cannot be created into the Failed state 
------------------------------------------------------------------------------

By default, if an error occurs when a pod is being created, the system tries again for up to the specified maximum number of retries. If the pod still cannot be created after the maximum number of retries, the pod enters the Pending state. For some jobs, you may want the pod to enter the Failed state. In this case, you can add an annotation to put a pod that cannot be created into the Failed state. 

The following sample code provides an example on how to add an annotation to put a pod that cannot be created into the Failed state:

    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: set-pod-fail-on-create-err
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
                k8s.aliyun.com/pod-fail-on-create-err: "true"  #Configure the pod to enter the Failed state if the pod cannot be created.
            labels:
                app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:latest
          nodeName: virtual-kubelet



Use an image cache as a custom resource definition (CRD) to accelerate the pod creation process 
--------------------------------------------------------------------------------------------------------------------

You can use image caches to accelerate the pod creation process. You can add annotations to specify image caches or enable automatic image cache match. For more information, see [Use an image cache CRD to accelerate pod creation](). 

The following sample code provides examples on how to add an annotation to specify an image cache and how to add an annotation to enable automatic image cache match:

* Specify an image cache

      apiVersion: v1
      kind: Pod
      metadata:
        annotations:
          k8s.aliyun.com/eci-image-snapshot-id: imc-2ze5tm5gehgtiiga****   #Specify an image cache.
        name: nginx-imagecache-id
      spec:
        containers:
        - image: nginx:1.7.9
          imagePullPolicy: IfNotPresent
          name: nginx
          resources:
            limits:
              cpu: 300m
              memory: 200Mi
            requests:
              cpu: 200m
              memory: 100Mi
        nodeName: virtual-kubelet

  

* Enable automatic image cache match

      apiVersion: v1
      kind: Pod
      metadata:
        annotations:
          k8s.aliyun.com/eci-image-cache: "true"    #Enable automatic image cache match.
        name: nginx-auto-match
      spec:
        containers:
        - image: nginx:1.7.9
          imagePullPolicy: IfNotPresent
          name: nginx
          resources:
            limits:
              cpu: 300m
              memory: 200Mi
            requests:
              cpu: 200m
              memory: 100Mi
        nodeName: virtual-kubelet

  




Specify a Container Registry Enterprise Edition instance 
-----------------------------------------------------------------------------

Container Registry allows you to pull images without entering a password. You can add annotations to specify a Container Registry Enterprise Edition instance and pull images from an image repository in the instance. For more information, see [Configure password-free settings to pull images from Container Registry Enterprise Edition instances](). 

The following sample code provides an example on how to add annotations to specify a Container Registry Enterprise Edition instance and pull images from an image repository in the instance:

    apiVersion: v1
    kind: Pod
    metadata:
      annotations:
        k8s.aliyun.com/acr-instance-id: cri-j36zhodptmyq****      #Specify the ID of a Container Registry Enterprise Edition instance.
      name: cri-test
    spec:
      containers:
      - image: test****-registry.cn-beijing.cr.aliyuncs.com/eci_test/nginx:1.0   #Pull images over the Internet.
        imagePullPolicy: Always
        name: nginx
      restartPolicy: Never



Associate an EIP with a pod 
------------------------------------------------

If a pod needs to communicate over the Internet, you can associate an EIP with the pod. For more information, see [Bind an EIP to a pod](). 

The following sample code provides examples on how to add annotations to automatically create an EIP and associate it with a pod and how to add an annotation to associate an existing EIP with a pod:

* Automatically create and associate an EIP

      apiVersion: v1
      kind: Pod
      metadata:
        name: nginx
        annotations:
          k8s.aliyun.com/eci-with-eip: "true"    #Enable automatic creation of EIPs.
          k8s.aliyun.com/eip-bandwidth: ",10"     #Specify a bandwidth. Unit: Mbit/s. Default value: 5.
      spec:
        containers:
        - image: registry-vpc.cn-hangzhou.aliyuncs.com/jovi/nginx:alpine
          imagePullPolicy: Always
          name: nginx
          ports:
          - containerPort: 80
            name: http
            protocol: TCP
        restartPolicy: OnFailure

  

* Associate an existing EIP

      apiVersion: v1
      kind: Pod
      metadata:
        name: nginx
        annotations:
          k8s.aliyun.com/eci-eip-instanceid: "eip-bp1q5n8cq4p7f6dzu****"   #Specify an EIP.
      spec:
        containers:
        - image: registry-vpc.cn-hangzhou.aliyuncs.com/jovi/nginx:alpine
          imagePullPolicy: Always
          name: nginx
          ports:
          - containerPort: 80
            name: http
            protocol: TCP
        restartPolicy: OnFailure

  




Configure inbound and outbound bandwidths 
--------------------------------------------------------------

You can configure inbound and outbound bandwidths for elastic container instances. You can add annotations to limit the inbound and outbound bandwidths of pods. For more information, see [Limit the bandwidth of an elastic container instance](). 

The following sample code provides an example on how to add annotations to limit the inbound and outbound bandwidths of a pod.

    apiVersion: v1
    kind: Pod
    metadata:
      name: eci-qos
      annotations:
        kubernetes.io/ingress-bandwidth: 40M    #Specify the inbound bandwidth.
        kubernetes.io/egress-bandwidth: 10M    #Specify the outbound bandwidth.
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        command: ["bash","-c","sleep 100000"]



Specify the size of the temporary storage space 
--------------------------------------------------------------------

A storage space of 20 GiB is provided for each elastic container instance free of charge. If this storage space is insufficient, you create a temporary storage space. You can add an annotation to specify the size of the temporary storage space. For more information, see [Create a custom-sized temporary storage space](). 

The following sample code provides an example on how to add an annotation to specify the size of the temporary storage space:

    apiVersion: v1
    kind: Pod
    metadata:
      name: test
      annotations:
        k8s.aliyun.com/eci-extra-ephemeral-storage: "50Gi"  #Specify the size of the temporary storage space.
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        imagePullPolicy: IfNotPresent
      restartPolicy: Always
      nodeName: virtual-kubelet



Configure a directory where to store core dump files 
-------------------------------------------------------------------------

If a program unexpectedly terminates or exits, a core dump file is generated. By default, this file is named core.pid and stored in the current directory. You can add an annotation to configure a directory where to store core dump files. For more information, see [View core dump files](). 

The following sample code provides an example on how to add an annotation to configure a directory where to store core dump files:

    apiVersion: v1
    kind: Pod
    metadata:
      name: test
      annotations:
        k8s.aliyun.com/eci-core-pattern: "pod/data/dump/core"  #Specify the directory where to store core dump files.
    spec:
      containers:
      - image: nginx:latest
        name: test-container
        volumeMounts:
        - mountPath: /pod/data/dump/
          name: default-volume
      volumes:
      - name: nfs
        nfs:
          server: 143b24****-gfn3.cn-beijing.nas.aliyuncs.com
          path: /dump/
          readOnly: false



Configure the NTP service 
----------------------------------------------

You can add the k8s.aliyun.com/eci-ntp-server annotation to configure the NTP service for pods. For more information, see [Configure an NTP server for pods](). 

The following sample code provides an example on how to add the annotation to configure the NTP service for a pod:

    apiVersion: apps/v1
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
                k8s.aliyun.com/eci-ntp-server: 100.100.5.*,100.100.5.*  #Specify the IP address of the NTP server.
            labels:
                app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:latest
          nodeName: virtual-kubelet


