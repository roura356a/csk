# Implement blue-green release by using an Ingress in a Kubernetes cluster {#concept_pvj_zqq_zdb .concept}

Generally, before releasing an application, you must bring a new version online and test the availability of this new version with low traffic.  The Ingress resource of Kubernetes cannot control and segment the traffic.  Therefore, only one service works under the path of the same domain name,  which is not good for gray release.  This document introduces how to implement the blue-green release and segment the traffic in an easy way by using the Alibaba Cloud Container Service Ingress.

## Prerequisites {#section_cbh_53z_zdb .section}

-   You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](../../../../intl.en-US/User Guide/Kubernetes cluster/Clusters/Create a cluster.md#).
-   You have connected to the master node by using SSH. For more information, see [Access Kubernetes clusters by using SSH](../../../../intl.en-US/User Guide/Kubernetes cluster/Clusters/Access Kubernetes clusters by using SSH.md#).

## Step 1 Create an application {#section_oz4_v3z_zdb .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  Click Kubernetes **Application** \> **Deployment**in the left-side navigation pane.
3.  Click **Create by template**  in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/14270/15330293315686_en-US.png)

4.  Select the cluster and namespace from the Clusters and Namespace drop-down lists. Select a sample template or Custom from the Resource Type drop-down list. Click **DEPLOY**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/14270/15330293315687_en-US.png)

    In this example, deploy an Nginx application that contains a deployment, a service, and an Ingress.  The deployment exposes the port  by using NodePort and an Ingress  is providing the external service.  The orchestration template is as follows:

    ```
    apiVersion: extensions/v1beta1
     kind: Deployment
     metadata:
       labels:
         run: old-nginx
       name: old-nginx
     spec:
       replicas: 1
       selector:
         matchLabels:
           run: old-nginx
       template:
         metadata:
           labels:
             run: old-nginx
         spec:
           containers:
           - image: registry.cn-hangzhou.aliyuncs.com/xianlu/old-nginx
             imagePullPolicy: Always
             name: old-nginx
             ports:
             - containerPort: 80
               protocol: TCP
           restartPolicy: Always
     ---
     apiVersion: v1
     kind: Service
     metadata:
       labels:
         run: old-nginx
       name: old-nginx
     spec:
       ports:
       -port: 80
         protocol: TCP
         targetPort: 80
       selector:
         run: old-nginx
       sessionAffinity: None
       type: NodePort
     ---
     apiVersion: extensions/v1beta1
     kind: Ingress
     metadata:
       name: echo
     spec:
       backend:
         serviceName: default-http-backend
         servicePort: 80
       rules:
       - host: mini-echo.io ##The virtual hostname.
         http:
           paths:
           - path: /
             backend:
               serviceName: old-nginx
               servicePort: 80
    ```

5.  After the successful deployment, click **Application** \> **Application \> Ingress in the left-side navigation pane**.

    You can see that the virtual hostname points to old-nginx.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/14270/15330293315689_en-US.png)

6.  Log on to the master node and run the curl commnad  to check the Ingress access.

    ```
    # curl -H "Host: mini-echo.io" http://101.37.224.229 ##The Ingress access address.
    old
    ```


## Step 2 Create new deployment and service {#section_ezh_5jz_zdb .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  Click Kubernetes **Application** \> **Deployment**in the left-side navigation pane.
3.  Click **Create by template**  in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/14270/15330293315686_en-US.png)

4.  Select the cluster and namespace from the Clusters and Namespace drop-down lists. Select a sample template or Custom from the Resource Type drop-down list. Click **DEPLOY**. 

    The new deployment  and service orchestration is as follows.

    ```
    apiVersion: extensions/v1beta1
     kind: Deployment
     metadata:
       labels:
         run: new-nginx
       name: new-nginx
     spec:
       replicas: 1
       selector:
         matchLabels:
           run: new-nginx
       template:
         metadata:
           labels:
             run: new-nginx
         spec:
           containers:
           - image: registry.cn-hangzhou.aliyuncs.com/xianlu/new-nginx
             imagePullPolicy: Always
             name: new-nginx
             ports:
             - containerPort: 80
               protocol: TCP
           restartPolicy: Always
     ---
     apiVersion: v1
     kind: Service
     metadata:
       labels:
         run: new-nginx
       name: new-nginx
     spec:
       ports:
       -port: 80
         protocol: TCP
         targetPort: 80
       selector:
         run: new-nginx
       sessionAffinity: None
       type: NodePort
    ```


## Step 3 Modify Ingress to implement blue-green release {#section_emp_t3z_zdb .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  Click Kubernetes **Application** \> **Application \> Ingress in the left-side navigation pane**Click Kubernetes \> Application \> Ingress in the left-side navigation pane.  
3.  Select the cluster and namespace from the Clusters and Namespace drop-down lists. Click **Update** at the right of the Ingress created in the preceding step.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/14270/15330293315691_en-US.png)

4.  In the displayed dialog box, modify the Ingress and then click **OK**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/14270/15330293315693_en-US.png)

    -   **Add annotations**: The name of the new service new-nginx is after the `/`.  50 is the traffic value, representing 50%.  The label `ingress.aliyun.weight/new-nginx: "50"` indicates to bring 50% of the traffic to the pod of the new service.
    -   **Configure new serviceName**: Parallel to the old service, configure the new serviceName,  which indicates to mount two services under the same path to correspond to the new application and old application respectively.
    On the Ingress page, you can see a new rule pointing to the new service new-nginx is added to the Ingress.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/14270/15330293315694_en-US.png)

5.  Log on to the master node and run the curl commnad  to check the Ingress access.

    ```
    # curl -H "Host: mini-echo.io" http://101.37.224.229 ##The Ingress access address.
     old
     # curl -H "Host: mini-echo.io" http://101.37.224.229
     new
     # curl -H "Host: mini-echo.io" http://101.37.224.229
     old
     # curl -H "Host: mini-echo.io" http://101.37.224.229
     new
     # curl -H "Host: mini-echo.io" http://101.37.224.229
     old
     # curl -H "Host: mini-echo.io" http://101.37.224.229
     new
    ```

    In this example, run  the curl command for six times and you get the new service for three times and old service for three times as the returned results respectively, which indicates that the weight configuration takes effect.


You can configure the traffic ratio of the blue-green release by adjusting the value in the Ingress annotations  `ingress.aliyun.weight/new-nginx: "50"` .

After completing the test of the new application version, you can configure the Ingress weight to 100 to bring traffic to the new service completely, or delete the annotations and old service version in the Ingress to implement the blue-green release.

