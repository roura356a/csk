# Configure a Kubernetes GPU cluster to support GPU scheduling {#concept_lmp_sq5_12b .concept}

From version 1.8, Kubernetes will support hardware acceleration devices such as NVIDIA GPU, InfiniBand, and FPGA, by using [device plugins](https://kubernetes.io/docs/concepts/cluster-administration/device-plugins/). Furthermore, GPU solutions of Kubernetes open source communities will be deprecated in version 1.10, and removed from the master code in version 1.11.

We recommend that you use an Alibaba Cloud Kubernetes cluster combined with GPU to run highly dense computational tasks such as machine learning and image processing. With this method, you can implement one-click deployment, elastic scaling, and other functions, without needing to install NVIDIA drivers or Compute Unified Device Architecture \(CUDA\) beforehand.

## Background information {#section_l3s_ss5_12b .section}

During cluster creation, Container Service performs the following operations:

-   Creates Elastic Compute Service \(ECS\) instances, sets the public key used for SSH logon from the management node to other nodes, and installs and configures the Kubernetes cluster by using CloudInit.
-   Creates a security group to allow inbound access to all ICMP ports in a VPC.
-   Creates a new VPC and VSwitch if you do not use the existing VPC, and also creates an SNAT entry for the VSwitch.
-   Creates VPC routing rules.
-   Creates a NAT gateway and Elastic IP \(EIP\).
-   Creates a Resource Access Management \(RAM\) user and AccessKey \(AK\). This RAM user has the permissions to query, create, and delete ECS instances, add and delete cloud disks, and all relevant access permissions for Server Load Balancer \(SLB\) instances, CloudMonitor, VPC, Log Service, and Network Attached Storage \(NAS\) services. The Kubernetes cluster dynamically creates the SLB instances, cloud disks, and VPC routing rules according to your configurations.
-   Creates an intranet SLB instance and exposes port 6443.
-   Creates an Internet SLB instance and exposes ports 6443, 8443, and 22. \(If you enable the SSH logon for Internet access when creating the cluster, port 22 is exposed. Otherwise, port 22 is not exposed.\)

## Prerequisites {#section_ugh_hvv_ydb .section}

You have activated Container Service, Resource Orchestration Service \(ROS\), and RAM.

You have logged on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs), [ROS console](https://partners-intl.console.aliyun.com/#/ros), and [RAM console](https://partners-intl.console.aliyun.com/#/ram) to activate the corresponding services.

**Note:** The deployment of Container Service Kubernetes clusters depends on the application deployment capabilities of Alibaba Cloud ROS. Therefore, you need to activate ROS before creating a Kubernetes cluster.

## Limits {#section_kqj_pvv_ydb .section}

-   The SLB instance created with the Kubernetes cluster only supports the Pay-As-You-Go billing method.
-   The Kubernetes cluster supports only Virtual Private Cloud \(VPC\).
-   By default, each account has a specified quota of the number of cloud resources that it can create. If the number of cloud resources has reached the quota limit, the account cannot create a cluster. Make sure you have sufficient resource quota to create a cluster. You can open a ticket to increase your quota.
    -   By default, each account can create up to 5 clusters across all regions and add up to 40 nodes to each cluster. You can open a ticket to create more clusters or nodes.
    -   By default, each account can create up to 100 security groups.
    -   By default, each account can create up to 60 Pay-As-You-Go SLB instances.
    -   By default, each account can create up to 20 EIPs.
-   The limits for ECS instances are as follows:
    -   Only the CentOS operating system is supported.
    -   Only Pay-As-You-Go ECS instances can be created.

        **Note:** After creating an instance, you can [Switch from Pay-As-You-Go to Subscription billing](../../../../reseller.en-US/Pricing/Switch from Pay-As-You-Go to Subscription billing.md#) in the ECS console.


## Create a GN5 Kubernetes cluster {#section_zmm_xs5_12b .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Kubernetes, click **Clusters**.
3.  Click **Create Kubernetes Cluster** in the upper-right corner. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16641/155858015210863_en-US.png)

    By default, the **Create Kubernetes Cluster** page is displayed.

    **Note:** Worker nodes are set to use GPU ECS instances to create a GPU cluster. For information about other parameter settings, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16641/155858015210864_en-US.png)

4.  Set the Worker nodes. In this example, the gn5 GPU instance type is selected to set Worker nodes as GPU working nodes.
    1.  If you choose to create Worker instances, you must select the instance type and the number of Worker nodes. In this example, two GPU nodes are created.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16641/155858015210865_en-US.png)

    2.  If you choose to add existing instances, you need to have already created GPU cloud servers in the same region where the cluster is to be created.
5.  After you have completed all required settings, click **Create** to start cluster deployment.
6.  After the cluster is created, choose **Clusters** \> **Nodes** in the left-side navigation pane.
7.  To view the GPU devices mounted to either of the created nodes, select the created cluster from the clusters drop-down list, select one of the created Worker nodes, and choose **More** \> **Details** in the action column.

## Create a GPU experimental environment to run TensorFLow {#section_xpj_bw5_12b .section}

Jupyter is a popular tool used by data scientists for the experimental environment TensorFlow. This topic describes an example of how to deploy a Jupyter application.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Kubernetes, choose **Applications** \> **Deployments**.
3.  Click **Create by Template** in the upper-right corner.
4.  Select the target cluster and namespace and then select a sample template or the custom template from the resource type drop-down list. After you orchestrate your template, click **DEPLOY**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16641/155858015210866_en-US.png)

    In this example, a Jupyter application template is orchestrated. The template includes a deployment and a service.

    ```
    ---
    # Define the tensorflow deployment
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: tf-notebook
      labels:
        app: tf-notebook
    spec:
      replicas: 1
      selector: # define how the deployment finds the pods it manages
        matchLabels:
          app: tf-notebook
      template: # define the pods specifications
        metadata:
          labels:
            app: tf-notebook
        spec:
          containers:
          - name: tf-notebook
            image: tensorflow/tensorflow:1.4.1-gpu-py3
            resources:
              limits:
                nvidia.com/gpu: 1                      #specify the number of NVIDIA GPUs that are called by the application
            ports:
            - containerPort: 8888
              hostPort: 8888
            env:
              - name: PASSWORD                         #specify the password used to access the Jupyter service. You can modify the password as needed.
                value: mypassw0rd
    
    # Define the tensorflow service
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: tf-notebook
    spec:
      ports:
      - port: 80
        targetPort: 8888
        name: jupyter
      selector:
        app: tf-notebook
      type: LoadBalancer                           #set Alibaba Cloud SLB service for the application so that its services are accessible from the Internet.
    ```

    If you use a GPU deployment solution of Kubernetes earlier than 1.9.3, you must define the following volumes in which the NVIDIA drivers reside:

    ```
    volumes:
        - hostPath:
            path: /usr/lib/nvidia-375/bin
            name: bin
        - hostPath:
            path: /usr/lib/nvidia-375
            name: lib
    ```

    When you orchestrate your deployment template in a cluster by using the GPU deployment solution of Kubernetes earlier than 1.9.3, your template must be highly dependent on the cluster. As a result, portability of the template is not achievable. However, in Kubernetes version 1.9.3 and later, you do not need to specify these hostPaths because the NIVEA plugins automatically discover the library links and execution files required by the drivers.

5.  In the left-side navigation pane under Container Service-Kubernetes, choose **Discovery and Load Balancing** \> **Services**. Then, select the target cluster and namespace, and then view the external endpoint of the tf-notebook service.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16641/155858015210867_en-US.png)

6.  Access the Jupyter application in a browser. The access address is `http://EXTERNAL-IP`. You need to enter the password set in the template.
7.  By running the following program, you can verify that this Jupyter application can use GPU, and the program is able to list all devices that can be used by Tensorflow:

    ```
    from tensorflow.python.client import device_lib
    
    def get_available_devices():
        local_device_protos = device_lib.list_local_devices()
        return [x.name for x in local_device_protos]
    
    print(get_available_devices())
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16641/155858015210868_en-US.png)


