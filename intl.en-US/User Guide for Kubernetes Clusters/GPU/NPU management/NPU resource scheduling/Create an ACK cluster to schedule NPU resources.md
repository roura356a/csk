# Create an ACK cluster to schedule NPU resources

This topic describes how to create an ACK cluster that uses neural processing unit \(NPU\) resources.

The Container Service for Kubernetes \(ACK\) and Resource Access Management \(RAM\) services are activated.

Compared with CPUs, the main advantage provided by NPUs is fast data processing in complex algorithmic models. Different from the von Neumann architecture used by CPUs, NPUs adopt the data-driven parallel computing architecture. This architecture significantly increases computing capabilities and reduces power consumption based on data streams. NPUs are the most suitable option used to process large amounts of data, such as video and image data. Compared with CPUs, NPUs offer 100 to 1,000 times higher speeds and significantly lower power consumption.

You can create ACK clusters and use Hanguang NPU to run compute-intensive tasks such as machine learning and image processing. You can quickly deploy workloads and dynamically scale resources to meet your business requirements.

**Note:** For more information about Hanguang NPU, visit [this website](https://www.t-head.cn/).

This topic describes how to use NPU resources. In the following example, an ACK cluster is created and an ecs.ebman1.26xlarge instance is added to the cluster.

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


## Create an ACK cluster with NPU resources

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click **Create Kubernetes Cluster** in the upper-right corner of the page.

4.  On the Select Cluster Template page, find Dedicated Cluster for Heterogeneous Computing and click **Create** to go to the Cluster Configurations wizard page.

    In this example, a dedicated heterogeneous computing cluster is created. You can also select **Managed Cluster for Heterogeneous Computing** in the Managed Clusters section to create a managed cluster.

    **Note:** To create a cluster for NPU computing, select ECS instance types with NPU capabilities to create worker nodes. For more information about other parameters, see [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md).

5.  Configure worker nodes. In this example, NPU nodes are used as worker nodes and the ecs.ebman1.26xlarg instance type is selected.

    -   To create new instances, you must specify the instance family, instance type, and the number of worker nodes to be created. In this example, two NPU nodes are created and the instance type is ecs.ebman1.26xlarge.
    -   To add existing instances, you must create NPU-accelerated instances in the region where you want to create the cluster in advance. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md).
6.  Set the other parameters and click **Create Cluster** to start the deployment.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane, click **Clusters**.

    3.  On the Clusters page, click the name of a cluster or click **Applications** in the **Actions** column.

    4.  The **Workload** page appears. In the left-side navigation pane, click **Nodes**.

    5.  On the Nodes page, find the created node and choose **More** \> **Details** in the **Actions** column of the node. You can view the NPU devices that are attached to the node.


## Set up a private image pull secret

Before you can use Docker images with NPU capabilities that are provided by Alibaba Cloud, contact Alibaba Cloud customer services to obtain an authorized Docker registry account. Download the required Docker image and set up a private image pull secret in the cluster.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  Select the cluster that you want to manage and choose **More** \> **Open Cloud Shell** in the **Actions** column of the cluster.

    After you connect to the cluster, the following output is returned:

    ![Output](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6078073061/p70024.png)

4.  Run the following commands to create a docker-registry secret:

    ```
    kubectl create secret \
    docker-registry regsecret \
    --docker-server=registry.cn-shanghai.aliyuncs.com \
    --docker-username=<your_username>
    --docker-password=<your_password>
    ```

    **Note:**

    -   `regsecret`: the name of the secret. You can enter a custom name.
    -   `--docker-server`: the address of the Docker registry.
    -   `--docker-username`: the username of the Docker registry account.
    -   `--docker-password`: the password of the Docker registry account.
5.  Add a secret in the pod configuration file to pull the private image with NPU capabilities.

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: test-npu
    spec:
      containers:
      - name: <Container name>
        image: registry.cn-shanghai.aliyuncs.com/hgai/<Docker image with NPU capabilities>
      imagePullSecrets:
      - name: <Secret name>
    ```

    **Note:**

    -   `imagePullSecrets` specifies the secret that is required to pull the image.
    -   `regsecret` must be the same as the secret name that is specified in Step [3](#step_cri_jrl_v1r).
    -   The Docker registry address in `image` must be the same as the one specified in `--docker-server`.

## Use NPU resources

To allow a pod to use NPU resources, set the `aliyun.com/npu` parameter in `resources.limits`.

```
apiVersion: v1
kind: Pod
metadata:
  name: <Pod name>
spec:
  containers:
    - name: <Container name>
      image: <Image name>
      resources:
        limits:
          aliyun.com/npu: <Number of requested NPU devices>
```

## Create an NPU test environment to run TensorFLow

You can use NPU resources to train TensorFlow models. In the following example, a pod is started to perform model training with NPU resources.

1.  Connect to the required ACK cluster. For more information, see [Use kubectl on Cloud Shell to manage Kubernetes clusters](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl on Cloud Shell to manage Kubernetes clusters.md).

    Run the following command in Cloud Shell:

    ```
    cat > test-pod.yaml <<- EOF
    apiVersion: v1
    kind: Pod
    metadata:
      name: test-npu-pod
    spec:
      restartPolicy: Never
      imagePullSecrets:
        - name: regsecret
      containers:
        - name: resnet50-npu
          image: registry.cn-shanghai.aliyuncs.com/hgai/tensorflow:v1_resnet50-tensorflow1.9.0-toolchain1.0.2-centos7.6
          resources:
            limits:
              aliyun.com/npu: 1 # requesting  NPUs
    EOF
    ```

2.  Run the following command to create a pod:

    ```
    kubectl apply -f test-pod.yaml
    ```

3.  Run the following command to query the pod status:

    ```
    kubectl get po test-npu-pod
    ```

    **Note:** If the pod status is Error, run the `kubectl logs test-npu-pod` command to check the pod logs and troubleshoot the error.


Wait a few minutes and query the pod status again.

```
kubectl get po test-npu-pod
```

If the pod status is Completed, check the pod logs again.

```
kubectl logs test-npu-pod
```

The following output indicates that the training is completed.

```
2019-10-30 12:10:50.389452: I tensorflow/core/platform/cpu_feature_guard.cc:141] Your CPU supports instructions that this TensorFlow binary was not compiled to use: AVX2 AVX512F FMA
100%|##########| 98/98 [00:26<00:00,  3.67it/s]
resnet_v1_50, result =  {'top_5': 0.9244321584701538, 'top_1': 0.7480267286300659}
```

