# Create a Linux application by using an orchestration template {#task_vwv_xwl_vdb .task}

In a Container Service Kubernetes orchestration template, you must define resource objects required for running an application, and combine the resource objects into a complete application by using label selector.

Create a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).

Create an Nginx application in this example. Firstly, create a backend pod resource object by creating the deployment. Then, deploy the service to bind it to the backend pod, forming a complete Nginx application.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Under Kubernetes, click **Application** \> **Deployment** in the left-side navigation pane.
3.  Click **Create by Template** in the upper-right corner. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16659/155833878811072_en-US.png)

4.  Configure the template and then click **DEPLOY**. 

    -   **Clusters**: Select the cluster in which in which the resource object is to be deployed.
    -   **Namespace**: Select a namespace to which resource object belongs. The default namespace is default. Except for the underlying computing resources such as nodes and persistent storage volumes, most of the resource objects must act on a namespace.
    -   **Resource Type**: Alibaba Cloud Container Service provides Kubernetes YAML sample templates of many resource types for you to deploy resource objects quickly. You can write your own template based on the format requirements of Kubernetes YAML orchestration to describe the resource type you want to define.
    -   **Add Deployment**: You can quickly define a YAML template with this feature.
    -   **Deploy with exist template**: You can import an existing template into the template configuration page.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16659/155833878811074_en-US.png)

    The following is a sample orchestration for an Nginx application. The orchestration is based on an orchestration template built in Container Service. By using this orchestration template, you can create a deployment that belongs to an Nginx application quickly.

    **Note:** Container Service supports Kubernetes YAML orchestration in which you can use the `---` symbol to separate resource objects so as to create multiple resource objects through a single template.

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
    
    ---
    
    apiVersion: v1     # for versions before 1.8.0 use apps/v1beta1
    kind: Service
    metadata:
       name: my-service1        #TODO: to specify your service name
       labels:
         app: nginx
    spec:
       selector:
         app: nginx             #TODO: change label selector to match your backend pod
       ports:
       - protocol: TCP
         name: http
         port: 30080            #TODO: choose an unique port on each node to avoid port conflict
         targetPort: 80
       type: LoadBalancer        ##In this example, change the type from NodePort to LoadBalancer.
    ```

5.  After you click **DEPLOY**, a message indicating the deployment status is displayed. After the deployment succeeds, click **Kubernetes Dashboard** in the message to go to the dashboard and check the deployment progress. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16659/155833878811075_en-US.png)

6.  In the Kubernetes dashboard, you can see that the service named my-service1 is successfully deployed and its external endpoint is exposed. Click the access address under **External endpoints**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16659/155833878811084_en-US.png)

7.  You can access the Nginx service welcome page in the browser. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16659/155833878811086_en-US.png)


You can also go back to the home page of Container Services and then click **Discovery and Load Balancing** \> **Services** in the left-side navigation pane to view the Nginx service.

