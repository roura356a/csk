# Use an SLB instance to enable a Layer 4 phased release

In a Kubernetes cluster, a Layer 7 ingress does not support TCP or UDP services. Therefore, you may fail to implement a phased release at Layer 7. This topic describes how to use a Server Load Balancer \(SLB\) instance to implement a Layer 4 phased release.

## Prerequisites

-   A cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   A master node is connected by using SSH. For more information, see [Use SSH to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use SSH to connect to an ACK cluster.md).

## Step 1: Create a deployment

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Applications** in the **Actions** column. On the page that appears, click the **Deployments** tab.

4.  On the **Deployments** tab, click **Create from Template** in the upper-right corner.

5.  In the **Sample Template** field, select a template from the drop-down list and click **Create**.

    ![Select a sample template](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3983662061/p148094.png)

    You can use the following template to create an NGINX application service and enable Internet access to the service through an SLB instance.

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
       type: LoadBalancer                       ## Enable Internet access to the service through an SLB instance.
    ```

6.  Click the **Deployments** tab. You can view the old-nginx deployment.

    ![Deployments](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3983662061/p148100.png)

    To view the NGINX service, click **Services** in the left-side navigation pane.

7.  On the **Services** page, find the service that you want to access and click its public endpoint. The default NGINX welcome page appears. In this example, **old** appears on the NGINX welcome page. This indicates that the service that you access is running on pod old-nginx.

    ![old](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4983662061/p9939.png)

    We recommend that you log on to a master node and use `curl` to send requests. In this case, you can view the requests for the service.

    ```
    bash  
    for x in {1... 10} ; do curl EXTERNAL-IP; done                    ## Replace EXTERNAL-IP with the public endpoint of the service.
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


## Step 2: Update the deployment to roll out a new service version

1.  In the left-side navigation pane, click **Clusters**.

2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Applications** in the **Actions** column. On the page that appears, click the **Deployments** tab.

3.  On the **Deployments** tab, click **Create from Template** in the upper-right corner.

4.  In the **Sample Template** field, select a template from the drop-down list and click **Create**.

    You can use the following template to update the deployment in Step 1. Both the old and new deployments have label `app:nginx`. This indicates that the old and new deployments are used to run the same NGINX service. In this case, when you send requests, traffic can be routed to the old and new versions.

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

5.  Click the **Deployments** tab. You can view the new-nginx deployment.

    ![Deployments](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4983662061/p9941.png)

6.  Log on to a master node and use curl to send requests. In this case, you can view the requests for the old and new versions.

    ```
    bash  
    for x in {1... 10} ; do curl EXTERNAL-IP; done                    ## Replace EXTERNAL-IP with the public endpoint of the service.
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

    In this example, the service adopts a load balancing policy that enables even traffic distribution and each version of the service runs on a pod. Therefore, when you send requests, traffic is evenly distributed to the old and new versions. The command output shows that a total of 10 requests are sent and each version receives five requests.


## Step 3: Enable weight-based traffic routing

Assume that you have implemented a phased release based on an SLB instance. To enable weight-based traffic routing, you can change the number of pods that run service versions. For example, if you want to route more traffic to the new version, you can increase the number of pods that run the new version to four.

**Note:** Assume that the old and new versions are available. To check whether traffic is routed based on the weighted routing rules, use curl to send requests in parallel. If you send a small number of requests, the command output may show that traffic is not routed based on the weighted routing rules. Therefore, we recommend that you use curl to send a large number of requests. In this example, 10 requests are sent.

1.  In the left-side navigation pane, click **Clusters**.

2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Applications** in the **Actions** column. On the page that appears, click the **Deployments** tab.

3.  Select the namespace where the service is deployed, find the deployment for the new version, and then click **Scale** in the Actions column.

    ![Deployments](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4983662061/p9942.png)

4.  In the dialog box that appears, set Desired Number of Pods to 4.

    **Note:** In Kubernetes, a deployment is updated based on a rolling update strategy. This ensures the minimum number of pods that can be available during the update. You can modify this value in the template file.

    ![Scale](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4983662061/p9943.png)

5.  After the deployment is updated, log on to a master node and use curl to run the following command:

    ```
    bash  
     for x in {1... 10} ; do curl EXTERNAL-IP; done                    ## Replace EXTERNAL-IP with the public endpoint of the service.
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

    In this example, a total of 10 requests are sent. The command output shows that the new version receives eight requests and the old version receives two requests.

    You can modify the number of pods to enable weight-based traffic routing for a phased release.


