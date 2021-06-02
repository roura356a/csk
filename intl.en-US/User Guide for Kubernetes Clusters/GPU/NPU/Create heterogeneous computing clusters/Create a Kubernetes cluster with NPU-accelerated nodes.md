---
keyword: [create a Kubernetes cluster with NPU-accelerated nodes, NPU resources]
---

# Create a Kubernetes cluster with NPU-accelerated nodes

This topic describes how to create a Kubernetes cluster with NPU-accelerated nodes in the Container Service for Kubernetes \(ACK\) console.

ACK performs the following operations when a cluster is created:

-   Creates ECS instances, configures a public key to enable Secure Shell \(SSH\) logon from master nodes to other nodes, and then configures the ACK cluster through CloudInit.
-   Creates a security group that allows access to the VPC over Internet Control Message Protocol \(ICMP\).
-   If you do not specify an existing VPC, ACK creates a VPC and vSwitch and creates SNAT entries for the vSwitch.
-   Adds route entries to the VPC.
-   Creates a NAT gateway and EIPs.
-   Creates a Resource Access Management \(RAM\) user and an AccessKey pair. Grants the following permissions to the RAM user: permissions to query, create, and delete ECS instances, permissions to add and delete disks, and full permissions on SLB, CloudMonitor, VPC, Log Service, and Apsara File Storage NAS. The ACK cluster automatically creates SLB instances, disks, and VPC route entries based on your configuration.
-   Creates an internal-facing SLB instance and opens port 6443.
-   Creates an Internet-facing SLB instance and opens ports 6443, 8443, and 22. If you enable SSH logon when you create the cluster, port 22 is opened. Otherwise, port 22 is not exposed.

## Limits

-   SLB instances that are created along with an ACK cluster support only the pay-as-you-go billing method.
-   ACK clusters support only VPCs.
-   By default, each account has specific quotas on cloud resources that can be created. You cannot create clusters if the quota is reached. Make sure that you have sufficient quotas before you create a cluster.

    To request a quota increase, submit a ticket.

    -   By default, you can create up to 50 clusters across all regions with each account. Each cluster can contain up to 100 nodes. To increase the quota of clusters or nodes,[submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

        **Note:** By default, you can add up to 48 route entries to a VPC. This means that you can deploy up to 48 nodes in an ACK cluster that uses Flannel. An ACK cluster that uses Terway is not subject to this limit. To deploy more nodes in this case,[submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to apply for an increase on the quota of route entries in the VPC that you want to use.

    -   By default, you can create up to 100 security groups with each account.
    -   By default, you can create up to 60 pay-as-you-go SLB instances with each account.
    -   By default, you can create up to 20 EIPs with each account.
-   Limits on ECS instances:

    The pay-as-you-go and subscription billing methods are supported.

    **Note:** After an ECS instance is created, you can change its billing method from pay-as-you-go to subscription in the ECS console. For more information, see [Change the billing method of an instance from pay-as-you-go to subscription](/intl.en-US/Pricing/Change the billing method/Change the billing method of an instance from pay-as-you-go to subscription.md).


## Create a Kubernetes cluster with NPU-accelerated nodes

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  In the upper-right corner of the Clusters page, click **Cluster Templates**.

4.  On the Select Cluster Template page, find Dedicated Cluster for Heterogeneous Computing and click **Create** to go to the Cluster Configurations wizard page.

    In this example, a dedicated Kubernetes cluster for heterogeneous computing is created. You can also select **Heterogeneous Computing Cluster** on the Select Cluster Template page to create a managed Kubernetes cluster for heterogeneous computing.

    **Note:** To create a Kubernetes cluster with NPU-accelerated nodes, you must select NPU-accelerated Elastic Compute Service \(ECS\) instances as worker nodes. For more information about other parameters, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

5.  Configure worker nodes. In this example, NPU-accelerated nodes are used as worker nodes and the ecs.ebman1.26xlarg instance type is selected.

    -   To create new instances, you must specify the instance family, instance type, and the number of worker nodes that you want to create. In this example, two NPU-accelerated nodes are created and the instance type is ecs.ebman1.26xlarge.
    -   To add existing instances, you must first create NPU-accelerated instances in the region where you want to create the cluster. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md).
6.  Set the other parameters and click **Create Cluster** to start the deployment.

7.  Check the NPU devices that are mounted to the nodes.

    1.  In the left-side navigation pane of the ACK console, click **Clusters**.

    2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Applications** in the **Actions** column.

    3.  In the left-side navigation pane of the details page, choose **Nodes** \> **Nodes**.

    4.  On the Nodes page, find the node that you created and choose **More** \> **Details** in the **Actions** column. On the details page of the node, you can view the NPU device that is mounted to the node.


## Configure a Secret to pull private images

Before you can use Docker images with NPU capabilities that are provided by Alibaba Cloud, contact your product managers to obtain an authorized Docker registry account. Download the required Docker image and configure a Secret to pull private images in the cluster.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and choose **More** \> **Open Cloud Shell** in the **Actions** column.

    After you connect to the cluster, the following output is returned:

    ![Result](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6078073061/p70024.png)

4.  Run the following commands to create a docker-registry Secret:

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
5.  Add the Secret in the pod configuration file to pull the private image with NPU capabilities.

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: test-npu
    spec:
      containers:
      - name: <The name of the container>
        image: registry.cn-shanghai.aliyuncs.com/hgai/<The Docker image with NPU capabilities>
      imagePullSecrets:
      - name: <The name of the Secret>
    ```

    **Note:**

    -   `imagePullSecrets` specifies the Secret that is required to pull the image.
    -   `regsecret` must be the same as the name of the Secret that is created in Step [3](#step_u84_b04_t39).
    -   The Docker registry address specified by `image` must be the same as the one that is specified by `--docker-server`.

## Use a Kubernetes cluster with NPU-accelerated nodes

You can manage ALI NPU devices in Kubernetes clusters. This allows you to deploy AI inference tasks with a few clicks and monitor the usage of the NPU resources. For more information, see [Perform NPU scheduling in a Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/NPU resource scheduling/Perform NPU scheduling in a Kubernetes cluster.md).

