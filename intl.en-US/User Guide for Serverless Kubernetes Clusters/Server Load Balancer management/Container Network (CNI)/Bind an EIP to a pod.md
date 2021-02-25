# Bind an EIP to a pod

This topic describes how to bind an elastic IP address \(EIP\) to a pod in a serverless cluster or a pod on a virtual node.

Container Service for Kubernetes \(ACK\) provides serverless clusters and virtual nodes. You can bind an EIP to a pod in a serverless cluster or a pod on a virtual node. In this case, you can deploy applications to a serverless cluster and access application services in a simplified manner. This feature has the following benefits:

-   A pod that has an EIP can access the Internet. You do not have to configure a NAT gateway for a virtual private cloud \(VPC\).
-   Services on a pod that has an EIP can be accessed from the Internet. You do not have to deploy services to the pod.
-   You can bind an EIP to a pod in a flexible and dynamic manner.

## Prerequisites

-   A serverless cluster is created or a virtual node is deployed on your cluster. For more information, see [Create a serverless Kubernetes cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md) and [Virtual nodes](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy ack-virtual-node in an ACK cluster.md).
-   Required ports are enabled in the security group rules for the cluster. In the following example, port 80 is enabled.

## Procedure

You can use one of the following two methods to bind an EIP to a pod.

## Method 1: Bind an EIP to a pod in automatic mode

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Serverless Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  In the left-side navigation pane, choose **Applications** \> **Deployments**. The Deployments page appears.

6.  Click the **Deployments** tab. On the tab that appears, click **Create from Template**, select a sample template or Custom, and then click **Create**.

    You can use the following YAML template to create a pod. In this example, k8s.aliyun.com/eci-with-eip is set to true. This indicates that the cluster or a virtual node automatically assigns and binds an EIP to the pod.

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
      annotations:
        "**k8s.aliyun.com/eci-with-eip**": "**true**"
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
    ```

    **Note:**

    -   You can use the annotation k8s.aliyun.com/eip-bandwidth to specify the bandwidth for the EIP. Default value: 5. Unit: Mbit/s.
    -   You can also use the annotation k8s.aliyun.com/eip-common-bandwith-package-id to add an Elastic Compute Service \(ECS\) instance that has an EIP to an EIP bandwidth plan instance.
    -   If you create a deployment, the system assigns an EIP to each pod in the deployment. Exercise caution when you perform this operation.
7.  Click the **Pods** tab to view the pod status.

8.  Find the pod that you want to manage and click **Edit**. The Edit YAML dialog box appears.

    **Note:** The IP address of `k8s.aliyun.com/allocated-eipAddress: 47.110.XX.XX` in the YAML file is the public IP address of the EIP.

9.  Enter http://IP address in a browser to visit the NGINX welcome page.

    In http://IP address, enter the IP address obtained from `k8s.aliyun.com/allocated-eipAddress: 47.110.XX.XX` of the YAML file.

    ![NGINX welcome page](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7246858951/p51465.png)

    **Note:** This method dynamically assigns an EIP to a pod, and the lifecycle of the EIP is the same as that of the pod. If you delete a pod, the EIP assigned to the pod is automatically deleted.


## Method 2: Specify an EIP ID

1.  Log on to the [VPC console](https://vpc.console.aliyun.com) and purchase an EIP. For more information, see [Apply for new EIPs](/intl.en-US/User Guide/Create an EIP/Apply for new EIPs.md).

    **Note:** The EIP and the cluster must be deployed in the same region.

2.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

3.  In the left-side navigation pane, click **Serverless Clusters**.

4.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column.

5.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

6.  In the left-side navigation pane, choose **Applications** \> **Deployments**. The Deployments page appears.

7.  Click the **Deployments** tab. On the tab that appears, click **Create from Template**, select a sample template or Custom, and then click **Create**.

    You can use the following YAML template to create a pod. In this example, you can specify the annotation k8s.aliyun.com/eci-eip-instanceid as the ID of the EIP.

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
      annotations:
        "**k8s.aliyun.com/eci-eip-instanceid**": "**<youreipInstanceId\>**"
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
    ```

    **Note:** Replace `<youreipInstanceId>` with the ID of the EIP obtained in Step 1.

8.  Click the **Pods** tab to view the pod status.

9.  Enter http://IP address in a browser to visit the NGINX welcome page.

    ![NGINX welcome page](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8246858951/p47613.png)

    **Note:** In http://IP address, enter the IP address of the EIP obtained in Step 1.


