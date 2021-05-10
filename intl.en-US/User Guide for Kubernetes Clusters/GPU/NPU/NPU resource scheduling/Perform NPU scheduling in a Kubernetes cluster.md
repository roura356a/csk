---
keyword: [NPU scheduling, NPU resources]
---

# Perform NPU scheduling in a Kubernetes cluster

This topic describes how to create and use a Container Service for Kubernetes \(ACK\) cluster with neural processing unit \(NPU\) resources.

ACK and Resource Access Management \(RAM\) services are activated.

Compared with CPUs, the most obvious advantage of NPUs is fast data processing in complex algorithmic models. Different from the von Neumann architecture used by CPUs, NPUs adopt the data-driven parallel computing architecture. This architecture significantly increases computing capabilities and reduces power consumption by using data streams. NPUs are the most suitable option in scenarios where large amounts of data are processed, such as video and image processing. Compared with CPUs, NPUs offer 100 to 1,000 times faster processing speeds and also significantly lower power consumption.

You can create ACK clusters and use Hanguang NPU to run compute-intensive tasks, such as machine learning and image processing. You can deploy workloads and dynamically scale resources to meet your business requirements.

**Note:** For more information about Hanguang NPU, visit [this website](https://www.t-head.cn/).

This topic describes how to use NPU resources in a Kubernetes cluster. In the following example, an ACK cluster is created and an ecs.ebman1.26xlarge instance is added to the cluster.

ACK performs the following operation when it creates the cluster:

-   Create Elastic Compute Service \(ECS\) instances, configures a public key to enable Secure Shell \(SSH\) logon from master nodes to other nodes, and then configures the ACK cluster by using cloud-init.
-   Create a security group that allows access to a virtual private cloud \(VPC\) over Internet Control Message Protocol \(ICMP\).
-   If you do not specify an existing VPC, ACK creates a VPC and a vSwitch and creates SNAT rules for the vSwitch.
-   Add route entries to the VPC.
-   Create a NAT gateway and an elastic IP address \(EIP\).
-   Create a RAM user and grants it permissions to query, create, and delete ECS instances, and permissions to add and delete disks. The RAM user is also granted all permissions on Server Load Balancer \(SLB\), CloudMonitor, VPC, Log Service, and Apsara File Storage NAS. ACK also creates an AccessKey pair for the RAM user. ACK automatically creates SLB instances, disks, and VPC route entries based on your configurations.
-   Create an internal-facing SLB instance and open port 6443.
-   Create an Internet-facing SLB instance and open ports 6443, 8443, and 22. If you enable SSH logon when you create the cluster, port 22 is open. Otherwise, port 22 is not open.

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

    **Note:** After an ECS instance is created, you can change its billing method from pay-as-you-go to subscription in the ECS console. For more information, see [Change the billing method of an instance from pay-as-you-go to subscription](/intl.en-US/Pricing/Change the billing method/Change the billing method of an instance from pay-as-you-go to subscription.md).


## Create an ACK cluster with NPU capabilities

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  In the upper-right corner of the Clusters page, click **Cluster Templates**.

4.  On the Select Cluster Template page, find Dedicated Cluster for Heterogeneous Computing and click **Create** to go to the Cluster Configurations wizard page.

    In this example, a dedicated Kubernetes cluster for heterogeneous computing is created. You can also select **Managed Cluster for Heterogeneous Computing** to create a managed cluster.

    **Note:** To create an ACK cluster with NPU capabilities, select NPU-accelerated ECS instance types to create worker nodes. For more information about other parameters, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

5.  Configure worker nodes. In this example, NPU-accelerated nodes are used as worker nodes and the ecs.ebman1.26xlarg instance type is selected.

    -   To create new instances, you must specify the instance family, instance type, and the number of worker nodes to be created. In this example, two NPU-accelerated nodes are created and the instance type is ecs.ebman1.26xlarge.
    -   To add existing instances, you must first create NPU-accelerated instances in the region where you want to create the cluster. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md).
6.  Set the other parameters and click **Create Cluster** to start the deployment.

7.  Check the NPU devices mounted to nodes.

    1.  In the left-side navigation pane of the ACK console, click **Clusters**.

    2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Applications** in the **Actions** column.

    3.  In the left-side navigation pane of the details page, choose **Nodes** \> **Nodes**.

    4.  On the Nodes page, find a worker node and choose **More** \> **Details** in the **Actions** column. You can view the NPU devices that are mounted to the node.


## Create a Secret to pull private images

Before you can use Docker images that require NPU capabilities and are provided by Alibaba Cloud, contact Alibaba Cloud customer services to obtain an authorized Docker registry account. Download the required Docker image and create a Secret that is used to pull private images.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and choose **More** \> **Open Cloud Shell** in the **Actions** column.

    After you connect to the cluster, the following output is returned:

    ![Output](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6078073061/p70024.png)

4.  Run the following commands to create a Secret of docker-registry type:

    ```
    kubectl create secret \
    docker-registry regsecret \
    --docker-server=registry.cn-shanghai.aliyuncs.com \
    --docker-username=<your_username>
    --docker-password=<your_password>
    ```

    **Note:**

    -   `regsecret`: the name of the Secret. You can enter a custom name.
    -   `--docker-server`: the address of the Docker registry.
    -   `--docker-username`: the username of the Docker registry account.
    -   `--docker-password`: the password of the Docker registry account.
5.  Add a Secret in the pod configuration file to pull the private image that requires NPU capabilities.

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: test-npu
    spec:
      containers:
      - name: <The name of the container>
        image: registry.cn-shanghai.aliyuncs.com/hgai/<A Docker image that requires NPU capabilities>
      imagePullSecrets:
      - name: <The name of the Secret>
    ```

    **Note:**

    -   `imagePullSecrets` specifies the Secret that is used to pull the image.
    -   `regsecret` must be the same as the name of the Secret that is created in Step [3](#step_cri_jrl_v1r).
    -   The Docker registry address specified by `image` must be the same as the one specified by `--docker-server`.

## Use NPU resources

To allow a pod to use NPU resources, set the `aliyun.com/npu` parameter in `resources.limits`.

```
apiVersion: v1
kind: Pod
metadata:
  name: <The name of the pod>
spec:
  containers:
    - name: <The name of the container>
      image: <The name of the image>
      resources:
        limits:
          aliyun.com/npu: <The amount of NPU resources>
```

## Set up an NPU environment to run TensorFLow

You can use NPU resources to train TensorFlow models. In the following example, a pod is started to perform model training with NPU resources.

1.  Connect to the ACK cluster. For more information, see [Use kubectl on Cloud Shell to manage ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl on Cloud Shell to manage ACK clusters.md).

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

3.  Run the following command to query the pod state:

    ```
    kubectl get po test-npu-pod
    ```

    **Note:** If the pod state is Error, run the `kubectl logs test-npu-pod` command to check the pod log and troubleshoot the error.


Wait a few minutes and query the pod state again.

```
kubectl get po test-npu-pod
```

If the pod state is Completed, check the pod log again.

```
kubectl logs test-npu-pod
```

The following output indicates that the training is complete.

```
2019-10-30 12:10:50.389452: I tensorflow/core/platform/cpu_feature_guard.cc:141] Your CPU supports instructions that this TensorFlow binary was not compiled to use: AVX2 AVX512F FMA
100%|##########| 98/98 [00:26<00:00,  3.67it/s]
resnet_v1_50, result =  {'top_5': 0.9244321584701538, 'top_1': 0.7480267286300659}
```

