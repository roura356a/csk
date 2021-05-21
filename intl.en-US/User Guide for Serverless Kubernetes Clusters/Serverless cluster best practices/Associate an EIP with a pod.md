---
keyword: associate an EIP
---

# Associate an EIP with a pod

This topic describes how to associate an elastic IP address \(EIP\) with a pod in a serverless Kubernetes \(ASK\) cluster or a pod on a virtual node.

Container Service for Kubernetes \(ACK\) provides ASK clusters and virtual nodes. You can associate an EIP with a pod in an ASK cluster or a pod on a virtual node. This facilitates how you deploy applications to an ASK cluster and access these applications. This feature has the following benefits:

-   A pod that is associated with an EIP can access the Internet. You do not need to configure a NAT gateway for the virtual private cloud \(VPC\).
-   A pod that is associated with an EIP can be accessed from the Internet. You do not need to deploy a Service to expose the pod.
-   You can dynamically associate an EIP with a pod.

## Prerequisites

-   An ASK cluster is created or a virtual node is deployed in a Kubernetes cluster. For more information, see [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md) and [Virtual nodes](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy the virtual node controller and use it to create Elastic Container Instance-based pods.md).
-   Required ports are exposed in the security group rules for the cluster. In the following example, port 80 is exposed.

**Note:**

-   Upgrade Virtual Kubelet to a version that is supported by v1.0.0.7-aliyun.
-   You can only associate an EIP with a pod when you create the pod.

## Procedure

You can use the following methods to associate an EIP with a pod:

## Method 1: Automatically associate an EIP with a pod

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  In the left-side navigation pane, choose **Applications** \> **Deployments** to go to the Deployments page.

6.  On the **Deployments** page, click **Create from Template**, select a sample template or customize a template, and then click **Create**.

    You can use the following YAML template to create a pod. In this example, k8s.aliyun.com/eci-with-eip is set to true. This indicates that the ASK cluster or a virtual node automatically assigns and associates an EIP with the pod.

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
      annotations:
        k8s.aliyun.com/eci-with-eip: "true"
      # k8s.aliyun.com/eip-bandwidth: '5' #Note: Do not set a unit for the specified bandwidth
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

    -   You can use the k8s.aliyun.com/eip-bandwidth annotation to specify the bandwidth limit of the EIP. Default value: 5. Unit: Mbit/s.
    -   You can also use the k8s.aliyun.com/eip-common-bandwith-package-id annotation to associate the EIP with an EIP bandwidth plan.
    -   If the YAML template creates a Deployment, the system assigns an EIP to each pod in the Deployment. Proceed with caution.
7.  In the left-side navigation pane, choose **Workloads** \> **Pods** to view the states of pods.

8.  Find the pod that you want to manage and click **Edit** in the Actions column. The Edit YAML dialog box appears.

    **Note:** In the YAML file of the pod, the IP address in the `k8s.aliyun.com/allocated-eipAddress: 47.110.XX.XX` field refers to the public IP address of the EIP.

9.  Enter http://IP address into the address bar of a browser to visit the NGINX welcome page.

    http://IP address refers to the IP address in `k8s.aliyun.com/allocated-eipAddress: 47.110.XX.XX` of the YAML file.

    ![NGINX welcome page](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7246858951/p51465.png)

    **Note:** This method dynamically assigns an EIP to a pod. The lifecycle of the EIP is the same as that of the pod. If you delete the pod, the EIP assigned to the pod is automatically deleted.

10. If you want to specify a line for the EIP that is associated with an Elastic Container Instance-based pod, you must add the `k8s.aliyun.com/eip-isp` field and specify a value.

    ISP indicates the Internet connection type of the EIP. By default, this parameter is set to `BGP`. For more information, see [AllocateEipAddressPro](ISP字段表示线路类型，默认为BGP，更多信息，请参见AllocateEipAddressPro。).

    The following YAML template is an example:

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
      annotations:
        k8s.aliyun.com/eci-with-eip: "true"
        k8s.aliyun.com/eip-isp: "BGP"
    spec:
      containers:
      - image: registry-vpc.cn-hangzhou.aliyuncs.com/jovi/nginx:alpine
        name: nginx
        ports:
        - containerPort: 80
          name: http
          protocol: TCP
      restartPolicy: OnFailure
    ```


## Method 2: Specify an EIP ID

1.  Log on to the [VPC console](https://vpc.console.aliyun.com) and apply for an EIP. For more information, see [Apply for new EIPs](/intl.en-US/User Guide/Create an EIP/Apply for new EIPs.md).

    **Note:** The EIP that you apply for and the cluster must be deployed in the same region.

2.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

3.  In the left-side navigation pane, click **Clusters**.

4.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column.

5.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

6.  In the left-side navigation pane, choose **Applications** \> **Deployments** to go to the Deployments page.

7.  Click the **Deployments** tab. On the tab that appears, click **Create from Template**, select a sample template or customize a template, and then click **Create**.

    You can use the following YAML template to create a pod. In this example, you can specify the ID of the EIP in the k8s.aliyun.com/eci-eip-instanceid annotation.

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
      annotations:
        "k8s.aliyun.com/eci-eip-instanceid": "<youreipInstanceId>"
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

    -   Replace `<youreipInstanceId>` with the ID of the EIP obtained in Step 1.
    -   If the system automatically assigns an EIP to a pod and you also specify an EIP for the pod, the EIP that you specify is not used.
8.  In the left-side navigation pane, choose **Workloads** \> **Pods** to view the states of pods.

9.  Enter http://IP address into the address bar of a browser to visit the NGINX welcome page.

    ![NGINX welcome page](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8246858951/p47613.png)

    **Note:** http://IP address refers to the IP address of the EIP that you applied for in Step 1.


