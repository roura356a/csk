# Create a service {#concept_vgg_n1n_vdb .concept}

A Kubernetes service, which is generally called a microservice, is an abstraction which defines a logical set of pods and a policy by which to access them. The set of pods accessed by a Kubernetes service is usually determined by a Label Selector.

Kubernetes pods are created and deleted in a short time even if they have their own IP addresses. Therefore, using pods directly to provide services externally is not a solution of high availability. The service abstraction decouples the relationship between the frontend and the backend. Therefore, the loose-coupling microservice allows the frontend to not care about the implementations of the backend.

For more information, see [Kubernetes service](https://kubernetes.io/docs/concepts/services-networking/service).

## Prerequisite {#section_s2y_x1n_vdb .section}

You have created a Kubernetes cluster successfully. For how to create a Kubernetes cluster, see [EN-US\_TP\_6880.md\#](reseller.en-US/User Guide/Kubernetes cluster/Clusters/Create a cluster.md#).

## Step 1 Create a deployment {#section_ahh_z1n_vdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Under Kubernetes, click **Application \>** \> **Deployment in the left-side navigation pane.**Click **Create by Template** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16662/155030268911022_en-US.png)

3.  Select the cluster and namespace to create the deployment. In the Resource Type drop-down list, select Custom to customize the template or a sample template. Then, click **DEPLOY**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16662/155030268911023_en-US.png)

    In this example, the sample template is an Nginx deployment.

    ```
    apiVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
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
         spec:
           containers:
           - name: nginx
             image: nginx:1.7.9 # replace it with your exactly <image_name:tags>
             ports:
             - containerPort: 80 ##You must expose this port in the service.
    ```

4.  Go to the Kubernetes dashboard to view the running status of this deployment.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6899/15503026894455_en-US.png)


## Step 2 Create a service {#section_efy_x1n_vdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Under Kubernetes, click **Application \>** \> **Service **in the left-side navigation pane.
3.  Select the cluster and namespace from the Clusters and Namespace drop-down lists. Click **Create** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6899/15503026894456_en-US.png)

4.  Complete the configurations in the displayed Create Service dialog box.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16662/155030268911026_en-US.png)

    -   **Name:** Enter the service name. In this example, enter nginx-svc.
    -   **Type:** Select the service type, namely, the access method of the service.
        -   ClusterIP: Exposes the service by using the internal IP address of your cluster. With this type selected, the service is only accessible from within the cluster. This type is the default service type.
        -   NodePort: Exposes the service by using the IP address and static port \(NodePort\) on each node. A ClusterIP service, to which the NodePort service is routed, is automatically created. You can access the NodePort service from outside the cluster by requesting `<NodeIP>:<NodePort>` .
        -   Server Load Balancer: Exposes the service by using Server Load Balancer, which is provided by Alibaba Cloud. Select public or inner to access the service by using the Internet or intranet. Alibaba Cloud Server Load Balancer can route to the NodePort and ClusterIP services.
    -   **Related deployment:** Select the backend object to bind with this service. In this example, select nginx-deployment-basic, the deployment created in the preceding step. The corresponding Endpoints object is not created if no deployment is selected here. You can manually map the service to your own endpoints. For more information, see [Services without selectors](https://kubernetes.io/docs/concepts/services-networking/service/#services-without-selectors).
    -   **Port Mapping:** Add the service port and container port. The container port must be the same as the one exposed in the backend pod.
5.  Click **Create**. The nginx-svc service is displayed on the Service List page.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16662/155030268911027_en-US.png)

6.  View the basic information of the service. Access the external endpoint of the nginx-svc service in the browser.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6899/15503026894461_en-US.png)


Then, you have created a service that is related to a backend deployment and accessed the Nginx welcome page successfully.

