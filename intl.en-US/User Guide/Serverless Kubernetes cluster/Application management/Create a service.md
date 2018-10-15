# Create a service {#concept_djn_1f1_ydb .concept}

Kubernetes service, which is generally called a microservice, is an abstraction which defines a logical set of pods and a policy by which to access them. This set of pods can be accessed by the service, typically by using the Label Selector.

Kubernetes pods are created and deleted in a short time even if they have their own IP addresses. Therefore, using pods directly to provide services externally is not a solution of high availability. The service abstraction decouples the relationship between the frontend and the backend. Therefore, the loose-coupling microservice allows the frontend to not care about the implementations of the backend.

For more information, see [Kubernetes service](https://kubernetes.io/docs/concepts/services-networking/service).

## Prerequisites {#section_v15_jf1_ydb .section}

You have successfully created a Serverless Kubernetes cluster, see [Create a Serverless Kubernetes cluster](reseller.en-US/User Guide/Serverless Kubernetes cluster/Cluster management/Create a Serverless Kubernetes cluster.md#).

## Step 1. Create a deployment {#section_w15_jf1_ydb .section}

Create a deployment by using the image, in this example create serverless-app-deployment. For more information, see [Create an application by using an image](reseller.en-US/User Guide/Serverless Kubernetes cluster/Application management/Create an application by using an image.md#).

## Step 2. Create a service {#section_x15_jf1_ydb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Under Kubernetes, click **Application** \> **Service** in the left-side navigation pane to enter the Service List page.
3.  Select the target cluster and namespace and click **Create** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16487/153959019310259_en-US.png)

4.  Complete the configurations in the displayed Create Service dialog box.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16487/153959019310260_en-US.png)

    -   **Name**: Enter the name of the service, in this example serverless-service.
    -   **Type**: Select the service type, namely, the access method of the service. Currently, only load balancing type is supported. Load Balancer is the load balancing service provided by Alibaba Cloud, public network access or intranet access can be used.
    -   **Related deployment**: Select the backend object to bind with this service, in this example, select nginx-deployment-basic. The corresponding Endpoints object is not created if no deployment is selected here. You can manually map the service to your own endpoints. For more information, see [services-without-selectors](https://kubernetes.io/docs/concepts/services-networking/service/#services-without-selectors).
    -   **Port Mapping:** Add the service port and container port. The container port must be the same as the one exposed in the backend pod.
    -   **Annotation**: Add an annotation to the service and configure load balancing parameters such as `service.beta.kubernetes.io/alicloud- Loadbalancer-bandwidth:20` indicating that the bandwidth of the service is set to 20 Mbit/s to control the traffic of the service. For more information, see [Server Load Balancer](reseller.en-US/User Guide/Serverless Kubernetes cluster/Server Load Balancer management/Server Load Balancer.md#).
    -   **Label**: You can add a label to the service to identify the service.
5.  Click **Create**, and the serverless-service appears in the list of services.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16487/153959019310261_en-US.png)

6.  You can view the basic information of the service and access the external endpoint of serverless-service in your browser.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16487/153959019310262_en-US.png)


Then, you have created a service that is related to a backend deployment and accessed the Nginx welcome page successfully.

