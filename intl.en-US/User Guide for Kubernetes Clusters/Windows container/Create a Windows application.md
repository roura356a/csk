---
keyword: [web application, Windows application]
---

# Create a Windows application

This section describes how to use an orchestration template to create a web application that consists of a Deployment and a Service. The Deployment provisions pods for the application and the Service manages access to the pods at the backend.

-   A Windows node is created. For more information, see [Create a Windows node pool](/intl.en-US/User Guide for Kubernetes Clusters/Windows container/Create a Windows node pool.md).
-   The resource objects required for running an application are created by using an orchestration template in the Container Service for Kubernetes \(ACK\) cluster. You can provision these resource objects by using mechanisms such as label selectors.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  On the Deployments page, click **Create from YAML** in the upper-right corner.

6.  Set the parameters and click **Create**.

    -   **Namespace**: Select the namespace to which the resource objects belong. By default, the default namespace is selected. Most resources are scoped to namespaces, except for underlying computing resources, such as nodes and persistent volumes \(PVs\).
    -   **Sample Template**: ACK provides YAML templates of various resource types. This simplifies the deployment of resource objects. You can also create a custom template based on YAML syntax to define the resources that you want to create.
    -   **Add Deployment**: This feature allows you to define a YAML template.
    -   **Use Existing Template**: You can import an existing template to the configuration page.
    -   **Save As**: You can save the template that you have configured.
    ![Configure the template](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/6365359951/p41759.png)

    The following sample template is based on an orchestration template provided by ACK. You can use this template to create a Deployment to run a web application.

    **Note:** ACK supports YAML syntax. You can use the `---` symbol to separate multiple resource objects. This allows you to create multiple resource objects in a single template.

    ```
    apiVersion: v1
    kind: Service
    metadata:
      labels:
        app: web-windows
      name: web-windows
    spec:
      type: LoadBalancer
      ports:
        - port: 80
          protocol: TCP
          targetPort: 80
      selector:
        app: web-windows
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      labels:
        app: web-windows
      name: web-windows
    spec:
      selector:
        matchLabels:
          app: web-windows
      template:
        metadata:
          labels:
            app: web-windows
        spec:
          restartPolicy: Always
          terminationGracePeriodSeconds: 30
          tolerations:
          - key: os
            value: windows
          affinity:
            nodeAffinity:
              requiredDuringSchedulingIgnoredDuringExecution:
                nodeSelectorTerms:
                - matchExpressions:
                  - key: kubernetes.io/os
                    operator: In
                    values:
                    - windows
          containers:
         # Replace <cn-hangzhou> in the following image address with the ID of the region where the cluster is deployed. 
            - image: registry-vpc.cn-hangzhou.aliyuncs.com/acs/sample-web-windows:v1.0.1
              name: windows
              ports:
              - containerPort: 80
                protocol: TCP
    ```

    **Note:**

    -   The `registry-vpc.{region}.aliyuncs.com/acs/sample-web-windows` image provided by Container Registry is a sample web application image based on Windows. This image is based on [mcr.microsoft.com/windows/nanoserver](https://hub.docker.com/_/microsoft-windows-nanoserver).
    -   You can also use the sample ASP.NET application image provided by Microsoft: `mcr.microsoft.com/dotnet/samples:aspnetapp`. For more information, see [.NET Samples](https://hub.docker.com/_/microsoft-dotnet-samples).
7.  Click **Create**. A notification that indicates the deployment status appears. After the Deployment is deployed, you can view the Deployment on the **Deployments** page.

8.  Access the web application

    1.  In the left-side navigation pane of the ACK console, click **Clusters**.

    2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    3.  In the left-side navigation pane of the details page, choose **Network** \> **Services**.

    4.  On the **Services** page, you can find that the **web-windows** Service is deployed and exposed through an external endpoint.

    5.  Click the external endpoint of the **web-windows** Service to access the web application.

    6.  To view the `hostname` of the application containers, change the URL to `name.html`.


