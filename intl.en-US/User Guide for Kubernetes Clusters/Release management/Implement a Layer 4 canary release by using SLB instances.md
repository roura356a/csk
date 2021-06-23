---
keyword: [SLB, canary release]
---

# Implement a Layer 4 canary release by using SLB instances

In a Kubernetes cluster, Layer 7 Ingresses cannot provide high performance for handling traffic through TCP or UDP in canary releases. This topic describes how to implement Layer 4 canary releases by using Server Load Balancer \(SLB\) instances.

## Prerequisites

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Use SSH to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use SSH to connect to an ACK cluster.md)

## Step 1: Create a Deployment

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  On the **Deployments** page, click **Create from Template** in the upper-right corner of the page.

6.  In the **Sample Template** field, select a template from the drop-down list and click **Create**.

    ![Sample template](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3983662061/p148094.png)

    The following code provides an example on how to create an NGINX application that provides external services through an SLB instance.

    ```
     apiVersion: apps/v1
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
             app: nginx
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
         run: nginx
       name: nginx
     spec:
       ports:
       - port: 80
         protocol: TCP
         targetPort: 80
       selector:
         app: nginx
       sessionAffinity: None
       type: type: LoadBalancer                       ## Enable external access to the application through an SLB instance.
    ```

    After the application is deployed, you can find the created Deployment on the **Deployments** page.

7.  In the left-side navigation pane, choose **Services and Ingresses** \> **Services**. On the **Services** page, click the external endpoint on the right side of the Service to visit the NGINX welcome page. In this example, **old** is displayed on the welcome page. This indicates that the old-nginx container at the backend is accessed.

    ![old](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4983662061/p9939.png)

    We recommend that you log on to a master node and send requests by using `curl`. In the following examples, requests are sent by using curl.

    ```
    bash  
    for x in {1..10} ; do curl EXTERNAL-IP; done                    ## Replace EXTERNAL-IP with the external endpoint of the Service.
    old
    old
    old
    old
    old
    old
    old
    old
    old
    old
    ```


## Step 2: Release a new application version by using a Deployment

1.  In the left-side navigation pane of the ACK console, click **Clusters**.

2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

4.  On the **Deployments** page, click **Create from Template** in the upper-right corner of the page.

5.  In the **Sample Template** field, select a template from the drop-down list and click **Create**.

    In this example, a Deployment is created to deploy a new version of the NGINX application. This Deployment is also added with the `app:nginx` label. This label allows both versions of the NGINX application to be discovered by the Service. This way, traffic is distributed to both application versions.

    The following template is used as an example:

    ```
     apiVersion: apps/v1
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
             app: nginx
         spec:
           containers:
           - image: registry.cn-hangzhou.aliyuncs.com/xianlu/new-nginx
             imagePullPolicy: Always
             name: new-nginx
             ports:
             - containerPort: 80
               protocol: TCP
           restartPolicy: Always
    ```

6.  After the new version of application is deployed, you can find a Deployment named new-nginx on the **Deployments** page.

7.  Log on to a master node and send requests to the NGINX application by using curl.

    ```
    bash  
    for x in {1..10} ; do curl EXTERNAL-IP; done                    ## Replace EXTERNAL-IP with the external endpoint of the Service.
    new
    new
    new
    old
    new
    old
    new
    new
    old
    old
    ```

    The result indicates that five requests are routed to the old version of the application and the other five to the new version. This is because the load balancing policy of the Service evenly distributes traffic. Each Deployment runs one pod. Therefore, the two pods receive the same number of requests.


## Step 3: Adjust traffic weights

To adjust traffic weights in a canary release implemented by using SLB instances, you must change the number of pods at the backend. If you want to route more traffic to the new application version, increase the number of the pods that run the new version. In this example, the number of the pods is increased to four.

**Note:** After you send 10 requests by using curl, the result may not strictly comply with the adjusted traffic weights. To obtain a more precise result, we recommend that you send more requests.

1.  In the left-side navigation pane of the ACK console, click **Clusters**.

2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

4.  Select the namespace where the Deployment is deployed, find the Deployment, and then click **Scale** in the **Actions** column.

5.  In the dialog box that appears, set Desired Number of Pods to 4.

    **Note:** By default, a Deployment is updated based on the rollingUpdate strategy. This ensures that a minimum number of pods are available during the update. You can change this number in the YAML file.

6.  After the Deployment is updated, log on to a master node and send requests by using curl.

    ```
    bash  
     for x in {1..10} ; do curl EXTERNAL-IP; done                    ## Replace EXTERNAL-IP with the external endpoint of the Service.
     new
     new
     new
     new
     new
     old
     new
     new
     new
     old
    ```

    In this example, 10 requests are sent. The result shows that eight requests are routed to the new version of the application and two to the old version.

    You can dynamically change the number of pods at the backend to balance the traffic weights of the old version and the new version to implement canary releases.


