---
keyword: [Application Center, orchestration template, create an application]
---

# Create an application from an orchestration template

You can view the deployment status and changes of all Kubernetes resources when you deploy an application in Application Center in the Container Service for Kubernetes \(ACK\) console. You can choose different methods to deploy applications based on the data sources of the templates. This topic describes how to create an application from an orchestration template in Application Center.

[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Multi-cluster** \> **Application Center**.

3.  On the Application Center page, click **Create Application**.

4.  In the **Source** step, set the parameters.

    1.  In the **Install appcenter** section, select the cluster where you want to install appcenter and click **Install**.

        **Note:** This step is required only when you use Application Center to create applications for the first time.

    2.  In the **Source** section, click **YAML Template**.

    3.  Click **Next**.

5.  In the **Application Configurations** step, set the parameters.

    1.  Set **Name** and **Description**.

    2.  Set the template. You can select an existing template or create a template. In this topic, a template is created.

        1.  Click the **Create Template** tab. Click **Create with YAML** in the upper-right corner of the tab.
        2.  Copy the following code into the Create panel and click **OK**.

            ```
            apiVersion: apps/v1
            kind: Deployment
            metadata:
              name: demo
              labels:
                app: demo
            spec:
              minReadySeconds: 5
              revisionHistoryLimit: 5
              progressDeadlineSeconds: 60
              strategy:
                rollingUpdate:
                  maxUnavailable: 1
                type: RollingUpdate
              selector:
                matchLabels:
                  app: demo
              template:
                metadata:
                  annotations:
                    prometheus.io/scrape: "true"
                    prometheus.io/port: "9797"
                  labels:
                    app: demo
                spec:
                  containers:
                  - name: demo
                    image: registry.cn-hangzhou.aliyuncs.com/acs/rollouts-demo:red
                    imagePullPolicy: IfNotPresent
                    ports:
                    - name: http
                      containerPort: 8080
                      protocol: TCP
                    readinessProbe:
                      tcpSocket:
                        port: 8080
                      initialDelaySeconds: 5
                      timeoutSeconds: 5
                    resources:
                      limits:
                        cpu: 2000m
                        memory: 512Mi
                      requests:
                        cpu: 100m
                        memory: 64Mi
            ---
            apiVersion: v1
            kind: Service
            metadata:
              name: demo-svc
            spec:
              selector:
                app: demo
              ports:
                - protocol: TCP
                  port: 80
                  targetPort: 8080
            ---
            apiVersion: apps/v1
            kind: Ingress
            metadata:
              name: demo
              labels:
                app: demo
            spec:
              rules:
                - host: app.demo.example.com
                  http:
                    paths:
                      - backend:
                          serviceName: demo-svc
                          servicePort: 80
            ```

    3.  Click **Next**.

6.  In the **Release Configurations** step, set the parameters.

    1.  In the **Release Configurations** section, set the release type. **Full Release** and **Canary Release** are supported.

        **Note:** If this is the initial release of the application, you can select only **Full Release**. You can select **Full Release** or **Canary Release** when you update the application.

    2.  In the **Select Cluster** section, click **Add** to the left side of the cluster you want to select. The added cluster appears in the **Cluster** section. Then, select the **namespace** where you want to deploy the new application version in the **Cluster** section.

        **Note:** If you want to select a cluster that does not have Internet access, you must associate an elastic IP address \(EIP\) with the cluster API server to enable Internet access.

    3.  Click **Release Now**.

7.  In the **Confirm Release** message, click **OK**.

    In the **Release Log** step, you can view the deployment progress.

8.  In the **Release Log** step, click **Complete**.


## Verify the result

1.  Enter app.demo.example.com in the address bar of your browser and press Enter.

    If the page appears as shown in the following figure, it indicates that the application is deployed.

    ![red](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9817128261/p263871.png)


**Related topics**  


[Application Center overview](/intl.en-US/User Guide for Kubernetes Clusters/Application Center/Application Center overview.md)

[t2067143.md\#]()

[t2067077.md\#]()

