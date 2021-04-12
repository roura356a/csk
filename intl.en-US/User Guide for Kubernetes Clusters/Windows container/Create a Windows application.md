---
keyword: [ASP.NET application, Windows application]
---

# Create a Windows application

This topic describes how to create a Windows application by using an orchestration template. In this topic, a Deployment and a Service are created to run an ASP.NET application. The Deployment provisions pods for the application and the Service allows access to application pods at the backend.

-   A Windows node is created. For more information, see [Create a node pool that runs Windows](/intl.en-US/User Guide for Kubernetes Clusters/Windows container/Create a node pool that runs Windows.md).
-   The resource objects required to run an ASP.NET application are created by using an orchestration template in the Container Service for Kubernetes \(ACK\) console. You can provision these resource objects by using mechanisms, such as the label selector.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  In the upper-right corner of the Deployments tab, click **Create from Template**.

6.  Set the parameters and click **Create**.

    -   **Cluster**: Select the cluster where the resource object is deployed.
    -   **Namespace**: Select the namespace to which the resource object belongs. The default namespace is default. Except for underlying computing resources such as nodes and persistent volumes \(PVs\), most resources are limited by namespaces.
    -   **Sample Template**: ACK provides YAML templates of various resource types. This simplifies the deployment of resource objects. You can also create a custom template based on YAML syntax to describe the resource that you want to define.
    -   **Add Deployment**: This feature allows you to define a YAML template.
    -   **Use Existing Template**: You can import an existing template to the configuration page.
    ![Configure the template](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6365359951/p41759.png)

    The following sample template is created from an orchestration template provided by ACK. This sample template provides an example on how to create a Deployment for an ASP.NET application.

    **Note:** ACK supports YAML syntax. If you want to declare multiple resource objects in a template, you can use the `---` symbol to separate the resource objects.

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
    apiVersion: apps/v1
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
            - image: 'mcr.microsoft.com/dotnet/samples:aspnetapp'
              name: aspnet
          nodeSelectors:
            kubernetes.io/os: "windows"
          tolerations:
          - key: "os"
            value: "windows"
    ```

    **Note:** The image `mcr.microsoft.com/dotnet/samples:aspnetapp` is a sample image provided by Microsoft. For more information, see [.NET Samples](https://hub.docker.com/_/microsoft-dotnet-samples).

7.  Click **Create**. A message that indicates the deployment status appears. After the deployment is complete, you can view the Deployment on the **Deployments** tab.

8.  Access the ASP.NET application.

    1.  In the left-side navigation pane of the ACK console, click **Clusters**.

    2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    3.  In the left-side navigation pane of the details page, choose **Services and Ingresses** \> **Services**.

    4.  On the **Services** page, you can find that a Service named **aspnet-svc** is created for the application. An external endpoint is also displayed on the page.

    5.  Click the external endpoint of the **aspnet-svc** Service to access the ASP.NET application.


