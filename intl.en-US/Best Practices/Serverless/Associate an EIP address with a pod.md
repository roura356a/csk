# Associate an EIP address with a pod {#concept_266137 .concept}

This topic describes how to associate an EIP address with a pod in a serverless Kubernetes cluster or with a pod on a virtual node of a dedicated or managed Kubernetes cluster.

## Benefits {#section_33u_zx6_h2x .section}

Associating an EIP address with a target pod offers the following benefits:

-   You can easily deploy an application that uses serverless containers.
-   Services provided by the application can be more quickly accessed.
-   Your pod can access the Internet without the need of a VPC NAT gateway.
-   Your target application can be accessed through the Internet without the need of an SLB instance.

## Prerequisites {#section_l86_vis_89h .section}

-   Either a serverless Kubernetes cluster is created, or a virtual node is created in a dedicated or managed Kubernetes cluster. For more information, see [Create a serverless Kubernetes cluster](../../../../reseller.en-US/User Guide/Serverless Kubernetes cluster/Cluster management/Create a serverless Kubernetes cluster.md#) or [Deploy a virtual node](../../../../reseller.en-US/User Guide/Kubernetes cluster/Auto Scaling/Deploy a virtual node.md#).
-   The corresponding port for the target application that you want to run is enabled in the security group of the target Kubernetes cluster.

    **Note:** The Nginx application is used as an example. Therefore, port 80 must be enabled in the security group of the target Kubernetes cluster.


## Procedure {#section_c65_cdu_1gi .section}

1.  Log on to the VPC console to create an EIP address. For more information, see [Create an EIP](../../../../reseller.en-US/User Guide/Create an EIP.md#).

    **Note:** You must create an EIP address in the same region where the target Kubernetes cluster is located.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/220325/155952852047489_en-US.png)

2.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
3.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
4.  In the left-side navigation pane under Container Service-Kubernetes, choose **Applications** \> **Deployments**.
5.  In the upper-right corner, click **Create by Template**.
6.  Select the target cluster and namespace, select an example template or customize a template, and then click **DEPLOY**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/220325/155952852047512_en-US.png)

    You can use the following YAML template to create a pod:

    **Note:** You must replace `<youreipInstanceId>` with the EIP ID obtained in step 1.

    ``` {#codeblock_kma_1jw_bs5}
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

7.  In the left-side navigation pane under Container Service-Kubernetes, choose **Applications** \> **Pods** to view the pod status.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/220325/155952852047513_en-US.png)

8.  In your browser, enter the URL http://ip address to access the welcome page of Nginx.

    **Note:** For this URL, you must enter the IP address obtained in step 1.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/220325/155952852047613_en-US.png)


