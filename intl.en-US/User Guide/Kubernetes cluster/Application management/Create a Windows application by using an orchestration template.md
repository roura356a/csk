# Create a Windows application by using an orchestration template {#task_vwv_xwl_vdb .task}

This topic describes how to create a Windows application by using an orchestration template. Such a template is used to customize the resources required by a Windows application to operate.

A Kubernetes cluster that supports Windows is created. For more information, see [Create a Windows application by using an orchestration template](#).

In this topic, an application named `aspnet` is created by using an orchestration template. This application contains a deployment and a service. On the backend, the deployment creates pods according to settings. Then, the service is associated with the pods.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Application** \> **Deployment**.
3.  In the upper-right corner, click **Create by Template**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150114/155710680441756_en-US.png)

4.  To set the orchestration template, set the following: 

    -   **Clusters**: Select the target cluster. The resources required by the application are deployed in a cluster.
    -   **Namespace**: Select the target namespace. The default namespace is preset. Except for nodes, persistent volumes, and other underlying resource types, most resources required by the application are deployed in a namespace.
    -   **Sample Template**: Select the target sample template. Alibaba Cloud Container Service for Kubernetes provides many built-in YAML orchestration templates for different types of resources. You can customize an orchestration template to set a type of resource according to the YAML orchestration requirements.
    -   **Add Deployment**: Edit a YAML template quickly by using this function.
    -   **Using Existing Template**: Import an existing template to the template setting area.
    Then, click **DEPLOY**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150114/155710680441759_en-US.png)

    The following is an orchestration template for the Windows application named `aspnet`. With such an orchestration template, you can quickly create a deployment and a service for the application.

    **Note:** If you want to create multiple resources in a template, you can use `---` to separate different resources.

    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: aspnet-svc
    spec:
      ports:
        - port: 80
          protocol: TCP
          targetPort: 80
      selector:
        app: aspnet
      type: LoadBalancer
    ---
    apiVersion: apps/v1beta2
    kind: Deployment
    metadata:
      name: aspnet
    spec:
      selector:
        matchLabels:
          app: aspnet
      template:
        metadata:
          labels:
            app: aspnet
        spec:
          containers:
            - image: 'microsoft/dotnet-samples:aspnetapp'
              name: aspnet
    ```

5.  A message is displayed on the bottom of the **Template** area to show the result. If a success message is displayed \(shown in the preceding figure\), click **Kubernetes Dashboard** at the end of the message to view the progress. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150114/155710680441783_en-US.png)

6.  In the left-side navigation pane, choose **Discovery and Load Balancing** \> **Service**. Then, in the External endpoints column, click the IP address of the created service named `aspnet-svc` to visit the home page of the `aspnet` application in your browser. 

    **Note:** In the Kubernetes dashboard, you can view that a service named `aspnet-svc` and its external endpoint are created.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150114/155710680441786_en-US.png)

    The following figure shows the home page of the `aspnet` application.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150114/155710680441787_en-US.png)


You can also return to the home page of Container Service-Kubernetes, and then choose **Discovery and Load Balancing** \> **Service** in the left-side navigation pane to view the service of the `aspnet` application.

