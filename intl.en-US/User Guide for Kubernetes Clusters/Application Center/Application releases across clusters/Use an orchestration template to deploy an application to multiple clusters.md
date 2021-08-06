---
keyword: [deploy applications, multiple regions]
---

# Use an orchestration template to deploy an application to multiple clusters

If you have multiple Container Service for Kubernetes \(ACK\) clusters deployed in regions within your account, you can use Application Center to deploy an application to these clusters at a time. This topic describes how to use an orchestration template to deploy an application to two ACK clusters in different regions at a time.

Two ACK clusters are created in different regions. These clusters can access the Internet. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Multi-cluster** \> **Application Center**.

3.  On the application center page, click **create application**.

4.  Configure parameters on the application source configuration wizard page.

    1.  In the **install application publishing components** area, select cluster, and then click **Start installation**.

        **Note:** This step is required only for the first installation of the app.

    2.  In the **application source** area, click **yaml template**.

    3.  click **next**.

5.  Set parameters in the **application configuration** wizard page.

    1.  Set the **app name** and **app description**.

    2.  To set a template, you can select an existing template or a new template. This article takes the new template as an example.

        1.  Click the new template tab, and then click **Add through yaml** in the upper right corner.
        2.  Copy the following into the text box and click **OK**.

            This paper takes the demo application of red version as an example.

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
            apiVersion: extensions/v1beta1
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

    3.  Click **next**.

6.  In the **Release Configurations** step, set the parameters.

    1.  In the **Release Configurations** section, set the release type. You can select **Full Release** or **Canary Release**.

        If this is the initial release of the application, you can select only Full Release. You can select Canary Release when you update the application.

    2.  In the **Select Cluster** section, click **Add** to the left side of the two clusters that you created. The added clusters appear in the **Cluster** section. Then, select the **namespace** where you want to deploy the new application version.

        **Note:** Clusters of all regions within your account are displayed in the **Select Cluster** section.

    3.  Click **Release Now**.

7.  Click **OK** in the **publish confirmation** dialog box.

    You can see the progress of publishing applications in the **publishing log** configuration page.

8.  Click **finish** on the **publish log** configuration wizard page.


## Verify the result

1.  Obtain the endpoint of the application.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  In the left-side navigation pane of the details page, choose **Network** \> **Ingresses**.

    5.  On the Ingresses, check the endpoint of the Ingress created for the demo application.

2.  Add the following content to the hosts file:

    ```
    <Endpoint> app.demo.example.com 
    ```

3.  Enter app.demo.example.com in the address bar of your browser and press Enter.

    If the page appears as shown in the following figure, it indicates that the application is deployed.

    ![red ](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9817128261/p263871.png)

4.  Delete the endpoint obtained in [Step 1](#step_kst_on8_nxi) from the hosts file. Then, obtain the endpoint for the application deployed in the other cluster and perform [Step 2](#step_lvi_x68_wmz) and [Step 3](#step_bwb_id7_mfs) again to access the application. If the page appears as shown in the following figure, it indicates that the application is also deployed.

    ![red ](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9817128261/p263871.png)


**Related topics**  


[t2069298.md\#]()

