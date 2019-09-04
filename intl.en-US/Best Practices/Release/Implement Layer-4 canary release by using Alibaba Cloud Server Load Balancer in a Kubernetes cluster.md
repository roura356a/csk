# Implement Layer-4 canary release by using Alibaba Cloud Server Load Balancer in a Kubernetes cluster {#concept_tcr_drq_zdb .concept}

In a Kubernetes cluster, Layer-7 Ingress cannot properly implement gray release for services accessed by using TCP/UDP. This document introduces how to implement Layer-4 canary release by using Server Load Balancer.

## Prerequisites {#section_q2n_wkz_zdb .section}

-   You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](../../../../reseller.en-US//Create a Kubernetes cluster.md#).
-   You have connected to the master node by using SSH. For more information, see [Access Kubernetes clusters by using SSH](../../../../reseller.en-US//Access a Kubernetes cluster by using SSH.md#).

## Step 1 Deploy the old version of the service {#section_zct_zkz_zdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Click **Application** \> **Deployment** in the left-side navigation pane.
3.  Click **Create by template** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/14271/15675813485705_en-US.png)

4.  Select the cluster and namespace from the Clusters and Namespace drop-down lists. Select a sample template or Custom from the Resource Type drop-down list. Click **DEPLOY**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/14271/15675813485706_en-US.png)

    In this example, an nginx orchestration that exposes the service by using SLB.

    ``` {#codeblock_ois_6rn_mh0}
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
       type: LoadBalancer ##Expose the service by using Alibaba Cloud SLB.
    ```

5.  Click **Application** \> **Deployment** and **Application** \> **Service** in the left-side navigation pane to check the deployment and service.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/14271/15675813485707_en-US.png)

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/14271/15675813485708_en-US.png)

6.  Click the external endpoint at the right of the service to go to the Nginx default welcome page. In this example, **old** is displayed on the Nginx welcome page, which indicates that the currently accessed service corresponds to the backend old-nginx container.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/14271/15675813485711_en-US.png)

    To easily display the results of multiple releases , we recommend that you log on to the master node and execute the `curl` command to view the deployment results.

    ``` {#codeblock_h50_owi_3xc}
    # bash  
    # for x in {1.. 10} ; do curl EXTERNAL-IP; done ##EXTERNAL-IP is the external endpoint of the service.
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


## Step 2 Bring new deployment version online {#section_cgz_hmz_zdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Click **Application** \> **Deployment**in the left-side navigation pane.
3.  Click **Create by template** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/14271/15675813485705_en-US.png)

4.  Select the cluster and namespace from the Clusters and Namespace drop-down lists.Select a sample template or Custom from the Resource Type drop-down list. Click **DEPLOY**.

    In this example, create a new version of nginx deployment that contains the `app:nginx` label. The label is used to use the same nginx service as that of the old version of deployment to bring the corresponding traffic.

    The orchestration template in this example is as follows:

    ``` {#codeblock_822_h1p_3xf}
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

5.  Click **Deployment** in the left-side navigation pane. The deployment of new-nginx is displayed on the Deployment page.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/14271/15675813485712_en-US.png)

6.  Log on to the master node and execute the curl command to view the service access.

    ``` {#codeblock_grq_4gh_g43}
    # bash  
    # for x in {1.. 10} ; do curl EXTERNAL-IP; done ##EXTERNAL-IP is the external endpoint of the service.
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

    You can see that the old service and new service are accessed for five times respectively. This is mainly because the service follows the Server Load Balancer policy of average traffic to process traffic requests, and the old deployment and new deployment are the same pod, which makes their traffic ratio as 1:1.


## Step 3 Adjust traffic weight {#section_myf_wkz_zdb .section}

You must adjust the number of pods in the backend to adjust the corresponding weight for the canary release based on Server Load Balancer. For example, to make the new service to have higher weight, you can adjust the number of new pods to four.

**Note:** If the old application version and new application version coexist, the results returned after executing the curl command of a sample do not conform to the configured weight strictly. In this example, to obtain the approximate effect, execute the curl command for 10 times to observe more samples.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Under Kubernetes, click **Application** \> **Deployment**in the left-side navigation pane.
3.  Select the cluster and namespace from the Clusters and Namespace drop-down lists. Click **Update** at the right of the deployment.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/14271/15675813485715_en-US.png)

4.  In the displayed dialog box, set the number of pods to four.

    **Note:** The default update method of Kubernetes deployment resources is rollingUpdate. Therefore, during the update process, the minimum number of containers that provide the service is guaranteed and this number can be adjusted in the template.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/14271/15675813485716_en-US.png)

5.  After the deployment, log on to the master node and execute the curl command to view the effect.

    ``` {#codeblock_xco_pie_6c5}
    # bash  
     # for x in {1.. 10} ; do curl EXTERNAL-IP; done ##EXTERNAL-IP is the external endpoint of the service.
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


You can see the new service is requested for eight times and the old service is requested twice among the 10 requests.

You can dynamically adjust the number of pods to adjust the weights of the new service and old service and implement the canary release.

