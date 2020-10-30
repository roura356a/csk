# Create an application on a node that runs Windows

This topic describes how to create an ASP.NET Core application by using an orchestration template. In this topic, a Deployment and a Service are created to run the ASP.NET Core application. The Deployment provisions pods for the application and the Service allows access to backend pods.

-   A node that runs Windows is created. For more information, see [Create a node pool that runs Windows](/intl.en-US/User Guide for Kubernetes Clusters/Windows container/Create a node pool that runs Windows.md).
-   The resource objects required to run an ASP.NET Core application are created by using an orchestration template in the Container Service for Kubernetes \(ACK\) console. You can provision these resource objects to run the application by using mechanisms such as the label selector.

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Workload**.

5.  On the **Workload** page, click the **Deployments** tab. In the upper-right corner of the Deployments tab, click **Create from Template**.

6.  Set the parameters and click **Create**.

    -   **Namespace**: Select the namespace to which the resource object belongs. The default namespace is default. Except for underlying computing resources such as nodes and persistent volumes \(PVs\), most resources are limited by namespaces.
    -   **Sample Template**: ACK provides YAML templates of various resource types. This simplifies the deployment of resource objects. You can also create a custom template based on YAML syntax to describe the resource that you want to define.
    -   **Add Deployment**: This feature allows you to define a YAML template.
    -   **Use Existing Template**: You can import an existing template to the configuration page.
    ![Configure the template](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6365359951/p41759.png)

    Based on an orchestration template provided by ACK, the following sample template can be used to create a Deployment for an ASP.NET Core application.

    **Note:** ACK supports YAML syntax. If you want to create multiple resource objects in a template, you can use the `---` symbol to separate the resource objects.

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
            - image: 'microsoft/dotnet-samples:aspnetapp'
              name: aspnet
          nodeSelectors:
            kubernetes.io/os: "windows"
          tolerations:
          - key: "os"
            value: "windows"
    ```

7.  Click **Create**. A message that indicates the deployment status appears. After the Deployment is deployed, you can view the Deployment on the **Deployments** tab.

    ![View the deployment result](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6365359951/p41783.png)

8.  You can access the ASP.NET Core application.

    1.  In the left-side navigation pane, click **Clusters**.

    2.  On the Clusters page, find the cluster where the ASP.NET Core application is deployed and choose **More** \> **Dashboard** in the **Actions** column.

    3.  In the left-side navigation pane, select the namespace where the application is deployed from the Namespace drop-down list.

    4.  In the left-side navigation pane, click **Discovery and Load Balancing** and click **Services**. On the Services page, you can find that a Service named **aspnet-svc** is deployed and is open to external access through an external endpoint.

    5.  You can click the external endpoint of the **aspnet-svc** Service to access the ASP.NET Core application.


In the left-side navigation page of the details page of the cluster, click **Services** to view the Service that is created for the ASP.NET Core application.

