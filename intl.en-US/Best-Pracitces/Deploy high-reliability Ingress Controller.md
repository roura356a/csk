# Deploy high-reliability Ingress Controller {#concept_i55_mtc_5db .concept}

In Kubernetes clusters, Ingress is a collection of rules that authorize the inbound access to the cluster and provide you with Layer-7 Server Load Balancer capabilities. You can provide the externally accessible URL, Server Load Balancer, SSL, and name-based virtual host. As the access layer of the cluster traffic, the high reliability of Ingress is important. This document introduces how to deploy a set of high-reliability Ingress access layer with good performance.

## Prerequisites {#section_etk_rtc_5db .section}

-   You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](../../../../intl.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#). 
-   You have connected to the master node by using SSH. For more information, see [../../../../dita-oss-bucket/SP\_235/DNcsk1877477/EN-US\_TP\_16642.md\#](../../../../intl.en-US/User Guide/Kubernetes cluster/Cluster management/SSH访问Kubernetes集群.md#).

## High-reliability deployment architecture {#section_qvt_qtc_5db .section}

To implement high reliability, the single point of failure must be solved first. Generally, the single point of failure is solved by deployment with multiple copies. Similarly, use the multi-node deployment architecture to deploy the high-reliability Ingress access layer in Kubernetes clusters. As Ingress is the access point of the cluster traffic, we recommend that you have the Ingress node exclusive to you to avoid the business applications and Ingress services from competing for resources.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15817/153957304910101_en-US.png)

As mentioned in the preceding deployment architecture figure, multiple exclusive Ingress instances form a unified access layer to carry the traffic at the cluster entrance and expand or contract the Ingress nodes based on the backend business traffic. If your cluster scale is not large in the early stage, you can also deploy the Ingress services and business applications in the hybrid mode, but we recommend that you limit and isolate the resources.

## Instructions on deploying high-reliability Ingress access layer {#section_svt_qtc_5db .section}

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15817/153957305010102_en-US.png)

-   Ingress Server Load Balancer: The frontend Server Load Balancer instance of the Ingress access layer.
-   Ingress node: The cluster node in which the Ingress pod is deployed.
-   Ingress pod: The Ingress service.

The Ingress Server Load Balancer, Ingress node, and Ingress pod are associated based on the tag `node-role.kubernetes.io/ingress=true`:

1.  The Ingress Server Load Balancer backend only mounts the cluster nodes with the tag `node-role.kubernetes.io/ingress=true`.
2.  The Ingress pod is only deployed to the cluster nodes with the tag `node-role.kubernetes.io/ingress=true`.

## Step 1 Add a label for Ingress nodes {#section_wvt_qtc_5db .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
2.  Under Kubernetes, click **Clusters** \> **Nodes** in the left-side navigation pane.
3.  Select the cluster from the Cluster drop-down list. View the instance IDs of the worker nodes and then click **Label Management** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15817/153957305010103_en-US.png)

4.  The Label Management page appears. Select the worker nodes and then click **Add Tag**.  Add the label `node-role.kubernetes.io/ingress：true` to the worker nodes and then click **OK**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15817/153957305010104_en-US.png)

    On the Label Management page, you can see the label is added to the worker nodes.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15817/153957305010105_en-US.png)


You can also log on to the master node and run the command `kubectl label no nodeID node-role.kubernetes.io/ingress=true` to add the label to the worker nodes quickly.

## Step 2 Create an Ingress service {#section_z4j_hx1_b2b .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
2.  Under Kubernetes, click**Application** \> **Deployment **in the left-side navigation pane.
3.  Select the cluster from the Clusters drop-down list and kube-system from the Namespace drop-down list. Click  **Delete** at the right of nginx-ingress-controller and then click OK in the displayed dialog box.

    An Ingress Controller is deployed by default when the cluster is initialized. For more information, see [ingress-nginx](https://github.com/kubernetes/ingress-nginx). You must delete the Ingress Controller deployed by default first  and then deploy a new set of high-reliability Ingress Controller access layer.

    **Note:** The Ingress Controller deployed by default  is associated with the nginx-ingress-lb service. Do not delete the associated service when deleting the deployment.  The nginx-ingress-lb service is about to be updated later.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15817/153957305010106_en-US.png)

4.  Click **Create by template** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15817/153957305010107_en-US.png)

5.  Select the cluster from the Clusters drop-down list and kube-system from the Namespace drop-down list. Select a sample template or Custom from the Resource Type drop-down list. Click **DEPLOY**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15817/153957305010108_en-US.png)

    In this example, redeploy the Ingress Controller to the target Ingress node in the DaemonSet method. You can also deploy the Ingress Controller by using deployment  together with the affinity.

    ```
    # nginx ingress pods
     apiVersion: extensions/v1beta1
     kind: DaemonSet
     metadata:
       name: nginx-ingress-controller
       labels:
         app: ingress-nginx
       namespace: kube-system
     spec:
       template:
         metadata:
           labels:
             app: ingress-nginx
         spec:
           nodeSelector:
             node-role.kubernetes.io/ingress: "true" ##Deploy the pod to the corresponding node by using the label selector.
           serviceAccount: admin
           containers:
             - name: nginx-ingress-controller
               image: registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:aliyun-nginx-0.9.0-beta. 19.2
               args:
                 - /nginx-ingress-controller
                 - --default-backend-service=$(POD_NAMESPACE)/default-http-backend
                 - --configmap=$(POD_NAMESPACE)/nginx-configuration
                 - --tcp-services-configmap=$(POD_NAMESPACE)/tcp-services
                 - --udp-services-configmap=$(POD_NAMESPACE)/udp-services
                 - --annotations-prefix=nginx.ingress.kubernetes.io
                 - --publish-service=$(POD_NAMESPACE)/nginx-ingress-lb
                 - --v=2
               env:
                 - name: POD_NAME
                   valueFrom:
                     fieldRef:
                       fieldPath: metadata.name
                 - name: POD_NAMESPACE
                   valueFrom:
                     fieldRef:
                       fieldPath: metadata.namespace
               ports:
               - name: http
                 containerPort: 80
               - name: https
                 containerPort: 443
               livenessProbe:
                 failureThreshold: 3
                 httpGet:
                   path: /healthz
                   port: 10254
                   scheme: HTTP
                 initialDelaySeconds: 10
                 periodSeconds: 10
                 successThreshold: 1
                 timeoutSeconds: 1
               readinessProbe:
                 failureThreshold: 3
                 httpGet:
                   path: /healthz
                   port: 10254
                   scheme: HTTP
                 periodSeconds: 10
                 successThreshold: 1
                 timeoutSeconds: 1
    ```

6.  A message indicating the deployment status is displayed on the page after you click DEPLOY. After the successful deployment, click **Kubernetes Dashboard** in the message to go to the dashboard. Select kube-system as the namespace. Click **Daemon Sets** in the left-side navigation pane and view the  nginx-ingress-controller.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15817/153957305010109_en-US.png)

7.  Click **Pods** in the left-side navigation pane to view the pods of nginx-ingress-controller.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15817/153957305010110_en-US.png)


## Step 3 Update Ingress Server Load Balancer service {#section_bxt_qtc_5db .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
2.  Under Kubernetes, click**Application** \> **Service in the left-side navigation pane.** in the left-side navigation pane.
3.  Select the cluster from the Clusters drop-down list and kube-system from the Namespace drop-down list. Click **Update**.

    An Ingress Server Load Balancer service is deployed by default when the cluster is initialized.  For more information, see [ingress-nginx](https://github.com/kubernetes/ingress-nginx). You must update the Ingress Server  Loadbalancer service to automatically identify the ingress node that is mounted for marking.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15817/153957305010111_en-US.png)

4.  In the displayed dialog box, add the annotation  `service.beta.kubernetes.io/alicloud-loadbalancer-backend-label "node-role.kubernetes.io/ingress=true"`,  and then click **OK**.

    You can also log on to the master node of the cluster  and run the command `kubectl apply -f https://acs-k8s-ingress.oss-cn-hangzhou.aliyuncs.com/nginx-ingress-slb-service.yml` to update  the nginx-ingress-lb service.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15817/153957305010112_en-US.png)


Then, you have deployed the high-reliability access layer of Ingress, which allows you to effectively deal with the challenges of single point of failure and business traffic, and quickly expand the Ingress access layer by adding tags.

