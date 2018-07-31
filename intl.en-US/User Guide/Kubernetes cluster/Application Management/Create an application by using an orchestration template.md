# Create an application by using an orchestration template {#task_vwv_xwl_vdb .task}

Create a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](intl.en-US/User Guide/Kubernetes cluster/Clusters/Create a cluster.md#).

In the Container Service Kubernetes orchestration template, you must define a resource object required for running an application, and combine the resource objects into a complete application by using label selector.

Create an Nginx application in this example. Firstly, create a backend pod resource object by creating the deployment. Then, deploy the service to bind it to the backend pod, forming a complete Nginx application.

1.   Log on to the [Container Service console](https://cs.console.aliyun.com). 
2.   Click Kubernetes \>**Application \>** \> **Deployment**in the left-side navigation pane. 
3.   Click **Create by template** in the upper-right corner. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6896/15330210144399_en-US.png)

4.   Configure the template and then click **DEPLOY**. 

    -   **Clusters:** Select the cluster in which the resource object is to be deployed.
    -   **Namespace:** Select the namespace to which the resource object belongs. default is selected by default. Except for the underlying computing resources such as nodes and persistent storage volumes, most of the resource objects must act on a namespace.
    -   **Resource Type:** Alibaba Cloud Container Service provides Kubernetes YAML sample templates of many resource types for you to deploy resource objects quickly. You can write your own template based on the format requirements of Kubernetes YAML orchestration to describe the resource type you want to define.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6896/15330210144400_en-US.png)

    The deployment sample orchestration of an Nginx application is as follows. By using this orchestration template, you can create a deployment that belongs to an Nginx application quickly.

    ```
    apiVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
     kind: Deployment
     metadata:
        name: nginx-deployment
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
              - containerPort: 80
    ```

5.   After you click **DEPLOY**, a message indicating the deployment status is displayed.  After the successful deployment, click **Kubernetes Dashboard** in the message to go to the dashboard and check the deployment progress. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6896/15330210144401_en-US.png)

6.   Go back to the Deploy templates page and deploy a service resource object. 

    Container Service provides a service sample template of an Nginx application. In this example, modify the template a little by changing the access type to LoadBalancer, and then you can create a service bound to the backend pod, which allows you to access the service in the browser.

    **Note:** In this example, the selector values in the pod orchestration and service orchestration are both nginx, so no modification is required. Make the corresponding modifications according to your actual situations.

    ```
    apiVersion: v1 # for versions before 1.8.0 use apps/v1beta1
     kind: Service
     metadata:
       name: my-service1 #TODO: to specify your service name
       labels:
         app: nginx
     spec:
       selector:
         app: nginx #TODO: change label selector to match your backend pod
       ports:
       - protocol: TCP
         name: http
         port: 30080 #TODO: choose an unique port on each node to avoid port conflict
         targetPort: 80
       type: LoadBalancer ##In this example, change the type from NodePort to LoadBalancer.
    ```

7.   Enter the preceding orchestration contents in the Template field and then click **DEPLOY**. A message indicating the deployment status is displayed after you click DEPLOY.  After the successful deployment, click **Kubernetes Dashboard** in the message to go to the dashboard and check the deployment progress of the service. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6896/15330210144402_en-US.png)

8.   In the Kubernetes dashboard, you can see the service my-service1 is successfully deployed and exposes the external endpoint.  Click the access address under **External endpoints**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6896/15330210144407_en-US.png)

9.   You can access the Nginx service welcome page in the browser. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6896/15330210144408_en-US.png)


