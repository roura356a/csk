# Use Grafana to display monitoring data {#task_fds_wqb_wdb .task}

-   You have successfully created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](intl.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   In this example, use the Grafana with built-in monitoring templates and the image address is `registry.cn-hangzhou.aliyuncs.com/acs/grafana:5.0.4`.

Among Kubernetes monitoring solutions, compared with open-source solutions such as Prometheus, the combination of Heapster + InfluxDB + Grafana is more simple and direct. Heapster not only collects monitoring data in Kubernetes, but also is relied on by the monitoring interface of the console and the POD auto scaling of HPA. Therefore, Heapster is an essential component of Kubernetes. An Alibaba Cloud Kubernetes cluster has the built-in Heapster + InfluxDB combination. To display the monitoring data, you must configure an available Grafana and the corresponding dashboard.

1.  Log on to the [Container Service console](https://cs.console.aliyun.com). 
2.  Under Kubernetes, click **Application** \> **Deployment** in the left-side navigation pane. 
3.  Click **Create by template** in the upper-right corner. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15812/153958591510516_en-US.png)

4.  Configure the template to create the deployment and service of Grafana. After completing the configurations, click **DEPLOY**. 

    -   Clusters: Select a cluster.
    -   Namespace: Select the namespace to which the resource object belongs, which must be kube-system.
    -   Resource Type: Select Custom in this example. The template must contain a deployment and a service.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15812/153958591810517_en-US.png)

    The orchestration template in this example is as follows:

    ```
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
       name: monitoring-grafana
       namespace: kube-system
    spec: 
       replicas: 1 
       template:
         metadata:
           labels:
             task: monitoring
             k8s-app: grafana
         spec:
           containers:
           - name: grafana
             image: registry.cn-hangzhou.aliyuncs.com/acs/grafana:5.0.4
             ports:
             - containerPort: 3000
               protocol: TCP
             volumeMounts:
             - mountPath: /var
               name: grafana-storage
             env:
             - name: INFLUXDB_HOST
               value: monitoring-influxdb
           volumes:
           - name: grafana-storage
             emptyDir: {}
     ---
    apiVersion: v1
    kind: Service
    metadata:
       name: monitoring-grafana
       namespace: kube-system
    spec:
       ports:
       - port: 80
         targetPort: 3000
       type: LoadBalancer
       selector:
         k8s-app: grafana
    ```

5.  Go back to the Deployment page after the successful deployment. Select the cluster from the Clusters drop-down list and then select kube-system from the Namespace drop-down list to view the deployed applications. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15812/153958591810518_en-US.png)

6.  Click the name monitoring-grafana to view the deployment status. Wait until the running status changes to Running. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15812/153958591810519_en-US.png)

7.  Click **Application** \> **Service** in the left-side navigation pane. Select the cluster from the Clusters drop-down list and kube-system from the Namespace drop-down list to view the external endpoint. 

    The external endpoint is automatically created by using the LoadBalancer type service. For developers who require more secure access policies, we recommend that you increase the security by adding the external endpoint to the IP whitelist or configuring the certificate.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15812/153958591810521_en-US.png)

8.  Click the **external endpoint** at the right of the monitoring-grafana service to log on to the Grafana monitoring page. 

    By default, the username and password of Grafana are both admin. We recommend that you change the password after the logon.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15812/153958591810522_en-US.png)

9.  Select the built-in monitoring templates to view the monitoring dashboards of the pod and node. 

    In this example, the Grafana has two built-in templates, one for displaying physical resources at the node level, and one for displaying resources related to the pod. Developers can also perform more complex presentations by adding custom dashboards or configure resource alarms based on Grafana.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15812/153958592210523_en-US.png) 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15812/153958592210524_en-US.png)


