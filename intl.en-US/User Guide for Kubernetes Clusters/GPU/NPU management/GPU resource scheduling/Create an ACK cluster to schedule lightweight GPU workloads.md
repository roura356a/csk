# Create an ACK cluster to schedule lightweight GPU workloads

In scenarios such as cloud gaming, AI model inference, and deep learning teaching, a single task cannot make full use of the computing and storage resources of a graphics processing unit \(GPU\) device. To maximize the usage of GPU resources in small tasks, Alibaba Cloud provides lightweight GPU-accelerated instances based on the virtualization of NVIDIA GPUs. This topic describes how to create an ACK cluster to manage and schedule lightweight GPU workloads.

The Container Service for Kubernetes \(ACK\) service is activated. For more information, see [Enable Container Service for Kubernetes](/intl.en-US/Quick Start/Enable Container Service for Kubernetes.md).

The Resource Access Management \(RAM\) service is activated. For more information, see [Grant permissions to a RAM user](/intl.en-US/RAM User Management/Grant permissions to a RAM user.md).

The ACK cluster runs Kubernetes 1.14.8 or later.

A lightweight GPU-accelerated instance is an Alibaba Cloud Elastic Compute Service \(ECS\) instance that is equipped with a lightweight graphics card. The memory size of this lightweight graphics card is only a fraction of that of a regular graphics card. The memory size varies based on the instance type. For more information, see [Compute optimized instance families with GPU capabilities](/intl.en-US/Instance/Instance type families/Compute optimized type family with GPU/Compute optimized instance families with GPU capabilities.md).

When you create or expand an ACK cluster, you can specify lightweight GPU-accelerated instance types as worker nodes based on your business requirements. For example, you can select the instance types that start with ecs.vgn. Existing lightweight GPU-accelerated instances can be manually or automatically added to an ACK cluster. The procedure to run tasks on lightweight GPU-accelerated instances is the same as that of regular GPU-accelerated instances.

This topic shows how to use lightweight GPU-accelerated instances. In the following example, an ACK cluster is created and VGN5I instances are added to the cluster.

To create an ACK cluster, perform the following operations in the ACK console:

-   Create ECS instances, configure a public key to enable Secure Shell \(SSH\) logon from master nodes to other nodes, and then configure the ACK cluster by using cloud-init.
-   Create a security group that allows access to a Virtual Private Cloud \(VPC\) network over Internet Control Message Protocol \(ICMP\).
-   Create a VPC network and a VSwitch and create SNAT rules for the VSwitch if you do not specify an existing VPC network.
-   Add route entries to a VPC network.
-   Create a NAT gateway and an elastic IP address.
-   Create a RAM user and grant it permissions to query, create, and delete ECS instances and permissions to add and delete cloud disks. The RAM user is also granted all permissions on Server Load Balancer \(SLB\), Cloud Monitor, VPC, Log Service, and Network Attached Storage \(NAS\). The ACK cluster automatically creates SLB instances, cloud disks, and VPC route entries based on your configurations.
-   Create an internal SLB instance and open port 6443.
-   Create a public Server Load Balancer \(SLB\) instance and open ports 6443, 8443, and 22. If you enable SSH logon when you create the cluster, port 22 is enabled. Otherwise, port 22 is not enabled.

## Limits

-   SLB instances that are created together with the ACK cluster support only the pay-as-you-go billing method.
-   ACK clusters support only VPC networks.
-   Each account can consume only a limited amount of computing resources. You fail to create clusters if you do not have sufficient computing resources. When you create clusters, make sure that you have sufficient resources.

    To increase the quota of computing resources for your account, submit a ticket.

    -   You can create up to 50 clusters across regions for each account. You can deploy up to 100 nodes in each cluster. To increase the quota of clusters or nodes for your account, [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

        **Note:** By default, you can add up to 48 route entries to each VPC network in an ACK cluster. This means that you can deploy up to 48 nodes in an ACK cluster that uses Flannel. An ACK cluster that uses Terway is not subject to this limit. To increase the quota of route entries for a VPC network, [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

    -   You can create up to 100 security groups under each account.
    -   You can create up to 60 pay-as-you-go SLB instances under each account.
    -   You can create up to 20 EIPs under each account.
-   Limits on ECS instances:

    The pay-as-you-go and subscription billing methods are supported.

    **Note:** After an ECS instance is created, you can change its billing method from pay-as-you-go to subscription in the ECS console. For more information, see [Change the billing method of an instance from pay-as-you-go to subscription](/intl.en-US/Pricing/Change the billing method/Switch the billing method from pay-as-you-go to subscription.md).


## Create a VGN5I ACK cluster

Both dedicated and managed clusters support VGN5I instances. The following example shows how to create a managed cluster with VGN5I instances.

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click **Create Kubernetes Cluster** in the upper-right corner of the page.

4.  On the Select Cluster Template page, find Standard Managed Cluster and click **Create** to go to the **Cluster Configurations** wizard page.

    **Note:** To create a cluster that supports GPU computing, select ECS instance types with GPU capabilities to create worker nodes. For more information about other parameters, see [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md).

5.  Configure worker nodes. In this example, Light-weight Compute Optimized Type with GPU vgn5i is used as the instance type of worker nodes.

    -   To create new instances, you must specify the instance family, instance type, and the number of worker nodes to be created. In this example, two GPU nodes are created and the instance type is ecs.vgn5i-m2.xlarge.
    -   To add existing instances, you must first create lightweight GPU instances in the region where you want to create the cluster.
6.  Set the other parameters and click **Create Cluster** to start the deployment.

    After the cluster is created, click **Clusters** in the left-side navigation pane of the ACK console. On the Clusters page, find the created cluster and click **Applications** in the **Actions** column of the cluster. The **Deployments** tab appears. In the left-side navigation pane, click **Nodes** to go to the Nodes page.

    Find one of the created nodes and choose **More** \> **Details** in the Actions column of the node. You can view the GPU devices that are attached to the node.

    In the **Overview** section, you can view the labels that are added to the lightweight GPU node. The labels indicate the number of GPU devices, the GPU memory, and the virtualized GPU device name. For example, the following labels can be added to the node: `aliyun.accelerator/nvidia_count: 1`, `aliyun.accelerator/nvidia_mem: 2048MiB`, and `aliyun.accelerator/nvidia_name: GRID-P4-2Q`.

    ![Label details](../images/p69641.png)


## Run a CUDA container to test the lightweight GPU nodes

You can use lightweight GPU nodes in the same way that you use regular GPU nodes. In the pod configuration file, specify that the **nvidia.com/gpu** resource is required to run the task. Each GPU-accelerated instance has only one lightweight GPU device. You must set `nvidia.com/gpu` to 1.

**Note:** If your cluster contains both lightweight GPU instances and regular GPU instances, you must set nodeSelector and node labels to schedule pods to different nodes.

In the following example, a CUDA sample application is used to test GPU devices.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Workload**.

5.  In the upper-right corner of the page, click **Create from Template**.

6.  Select the required cluster and namespace. Select a sample template, or set Sample Template to Custom and customize the template in the Template field. Then, click **Create** to create the application.

    In this example, the sample template defines a deployment that runs a CUDA container.

    The `aliyun.accelerator/nvidia_mem: 2048MiB` label is used to deploy the pod to the lightweight GPU node that is created in the [Create a VGN5I ACK cluster](#section_mjp_yzl_zy6) section.

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: cuda-sample
      labels:
        app: cuda-sample
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: cuda-sample
      template:
        metadata:
          labels:
            app: cuda-sample
        spec:
          nodeSelector:
            aliyun.accelerator/nvidia_mem: 2048MiB
          containers:
          - name: cuda-sample
            image: nvidia/cuda:9.0-cudnn7-devel-ubuntu16.04
            command: ["tail", "-f", "/dev/null"]
            resources:
              limits:
                nvidia.com/gpu: "1"
    ```

7.  After cuda-sample is started, run the kubectl exec command to view the GPU device status and CUDA sample code in the container.

    1.  [Use kubectl to connect to a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to a cluster.md).

    2.  Run the following command to obtain the name of the pod:

        ```
        kubectl get pod
        ```

        ```
        NAME                           READY   STATUS    RESTARTS   AGE
        cuda-sample-79f9fc9cc5-hlpp5   1/1     Running   0          4h27m
        ```

    3.  Run the following command to view the GPU device information.

        The output shows that the container has a GPU device named `GRID P4-2Q`. The memory of the device is 2,048 MiB.

        ```
        kubectl exec -it cuda-sample-79f9fc9cc5-hlpp5 bash
        ```

        ```
        root@cuda-sample-79f9fc9cc5-hlpp5:/# nvidia-smi
        Thu Nov 21 14:45:45 2019
        +-----------------------------------------------------------------------------+
        | NVIDIA-SMI 418.70       Driver Version: 418.70       CUDA Version: 10.1     |
        |-------------------------------+----------------------+----------------------+
        | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
        | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
        |===============================+======================+======================|
        |   0  GRID P4-2Q          On   | 00000000:00:07.0 Off |                  N/A |
        | N/A   N/A    P8    N/A /  N/A |    144MiB /  2048MiB |      0%      Default |
        +-------------------------------+----------------------+----------------------+
        
        +-----------------------------------------------------------------------------+
        | Processes:                                                       GPU Memory |
        |  GPU       PID   Type   Process name                             Usage      |
        |=============================================================================|
        |  No running processes found                                                 |
        +-----------------------------------------------------------------------------+
        ```

    4.  Run the following commands to view the CUDA version, install the CUDA sample application, and test the sample code:

        ```
        root@cuda-sample-79f9fc9cc5-hlpp5:/# nvcc -V
        nvcc: NVIDIA (R) Cuda compiler driver
        Copyright (c) 2005-2017 NVIDIA Corporation
        Built on Fri_Sep__1_21:08:03_CDT_2017
        Cuda compilation tools, release 9.0, V9.0.176
        root@cuda-sample-79f9fc9cc5-hlpp5:/# apt-get update && apt-get install -y cuda-samples-9-0
        ...
        
        root@cuda-sample-79f9fc9cc5-hlpp5:/# cd /usr/local/cuda-9.0/samples/0_Simple/simpleTexture
        root@cuda-sample-79f9fc9cc5-hlpp5:/usr/local/cuda-9.0/samples/0_Simple/simpleTexture# make
        ...
        
        root@cuda-sample-79f9fc9cc5-hlpp5:/usr/local/cuda-9.0/samples/0_Simple/simpleTexture# ./simpleTexture
        simpleTexture starting...
        GPU Device 0: "GRID P4-2Q" with compute capability 6.1
        
        Loaded 'lena_bw.pgm', 512 x 512 pixels
        Processing time: 0.038000 (ms)
        6898.53 Mpixels/sec
        Wrote './data/lena_bw_out.pgm'
        Comparing files
            output:    <./data/lena_bw_out.pgm>
            reference: <./data/ref_rotated.pgm>
        simpleTexture completed, returned OK
        ```


