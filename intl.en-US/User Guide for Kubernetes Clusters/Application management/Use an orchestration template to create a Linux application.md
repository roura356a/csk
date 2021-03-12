# Use an orchestration template to create a Linux application

In an orchestration template of Container Service for Kubernetes \(ACK\), you must define the resources that are required to run an application and use mechanisms such as label selectors to manage these resources.

[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).

This topic describes how to use an orchestration template to create an NGINX application that consists of a deployment and a service. The service is associated with a pod that is created by the deployment.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  On the Deployments tab, click **Create from Template**.

6.  Set the parameters of the specified template and click **Create**.

    -   **Cluster**: Select the cluster where the resource object is deployed.
    -   **Namespace**: Select the namespace to which the resource object belongs. The default namespace is default. Except for underlying computing resources such as nodes and persistent volumes \(PVs\), most resources are limited by namespaces.
    -   **Sample Template**: ACK provides YAML templates of various resource types. This simplifies the deployment of resource objects. You can also create a custom template based on YAML syntax to describe the resource that you want to define.
    -   **Add Deployment**: This feature allows you to define a YAML template.
    -   **Use Existing Template**: You can import an existing template to the configuration page.
    Based on an orchestration template provided by ACK, the following sample template is used to create a deployment of an NGINX application.

    **Note:** ACK supports YAML syntax. You can use the `---` symbol to separate multiple resource objects. This allows you to create multiple resource objects in a single template.

    ```
    apiVersion: apps/v1 
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
              - containerPort: 80
    
    ---
    
    apiVersion: v1     
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
       type: LoadBalancer        ##In this example, the type is changed from Nodeport to LoadBalancer.
    ```

7.  Click **Create**. A message appears to indicate the deployment status. You can view the deployment progress in the dashboard. For more information, see [Create an application in the Kubernetes dashboard]().

    ![View the deployment progress](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4145359951/p11075.png)

8.  In the dashboard, verify that the my-service1 service is running and its external endpoint is displayed. Click the address in the **External Endpoints** column.

    ![External endpoint](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4145359951/p11084.png)

9.  You can visit the NGINX welcome page in the browser.

    ![Visit the welcome page](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4145359951/p11086.png)


