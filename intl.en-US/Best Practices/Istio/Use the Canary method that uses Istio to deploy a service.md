# Use the Canary method that uses Istio to deploy a service {#concept_tq1_5qh_hhb .concept}

This topic describes how to use the Canary method \(that uses Istio\) to deploy a service through Alibaba Cloud Container Service for Kubernetes \(ACK\).

## Background information {#section_bgc_3ll_14m .section}

Istio is a service mesh that can be used to meet the requirements of the distributed application architectures that involve microservices such as application O&M, debugging, and security management. You also can use Istio for microservice network scenarios such as load balancing, service-to-service authentication, and monitoring.

By using Istio, ACK allows you to manage services of an application. With ACK, you can create and manage multiple versions of one service for an application, manage the distribution of traffic destined for the service, and implement a Canary release for the service.

## Prerequisites {#section_lf2_nrh_hhb .section}

-   A Kubernetes cluster is created. For more information, see [Create a Kubernetes cluster](../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   Istio is deployed for this Kubernetes cluster. For more information, see [Deploy Istio](../../../../reseller.en-US/User Guide/Kubernetes cluster/Istio management/Deploy Istio.md#).

## Procedure {#section_iyf_bsh_hhb .section}

1.  Enable automatic sidecar injection.
    1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
    2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Clusters** \> **Namespaces**.
    3.  Click **Create**.
    4.  In the displayed dialog box, set **Name** to demo, and set **Tag** to follows:

        -   The variable name of the tag is set to istio-injection.
        -   The variable value of the tag is set to enabled.
        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517242293_en-US.png)

    5.  Click **Add**, and then click **OK**.

        Then, the newly created namespace is displayed in the namespace list.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517243151_en-US.jpg)

2.  Create a server-end application and its virtual service.
    1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
    2.  In the left-side navigation pane, choose **Service Mesh** \> **Virtual Service**.
    3.  In the upper-right corner of the page, click **Create**.![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517243152_en-US.jpg)
    4.  On the Basic Information tab page, set the required parameters.

        -   **Name**: Set the application name.
        -   **Version**: Set the application version.
        -   **Clusters**: Select the cluster where you want to deploy the application.
        -   **Namespaces**: Select the namespace where you want to deploy the application. You must select the namespace created in step 1.
        -   **Replicas**: Set the number of replicas, that is, the number of pods.
        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517243153_en-US.jpg)

    5.  Configure the container. Set **Image Name** to **nginx**, and set **Image Version** to **1.14**. For more information about other container settings, see [Create a deployment application by using an image](../../../../reseller.en-US/User Guide/Kubernetes cluster/Application management/Create a deployment application by using an image.md#). Then, click **Next**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517243154_en-US.jpg)

    6.  Set the **Service** parameters.
        1.  In the upper-left corner, click **Create**. Then, in the displayed dialog box, set the required parameters.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517243155_en-US.jpg)

            -   **Name**: Enter the service name.
            -   **Type**: Select the **ClusterIP** service type.
            -   **Port Mapping**: Set the service port name, service port number, container port number, and TCP protocol. The format of a service port name must be <protocol\>\[-<suffix\>-\]. The protocol part of a service port name can be one of the following: gRPC, HTTP, HTTP/2, HTTPS, Mongo, Redis, TCP, TLS, or UDP. Istio supports a routing service through one of these protocols.

                **Note:** The protocol part of a service port name is case insensitive.

            -   **Annotation**: Add an annotation to the service.
            -   **Tag**: Add a tag to the service to identify the service.
            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517243156_en-US.jpg)

        2.  Click **Create**.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517243157_en-US.jpg)

    7.  In the lower-right corner, click **Create**. Then, the system displays all the created resources if no error occurs. You can click **View Details** to view the application details.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517243158_en-US.jpg)

3.  Create a client-end application named `sleep`. This application will be used to send access traffic to the created virtual service.
    1.  Save the following as a file named sleep.yaml.

        ```
        apiVersion: v1
        kind: Service
        metadata:
          name: sleep
          labels:
            app: sleep
        spec:
          ports:
          - port: 80
            name: http
          selector:
            app: sleep
        ---
        apiVersion: extensions/v1beta1
        kind: Deployment
        metadata:
          name: sleep
        spec:
          replicas: 1
          template:
            metadata:
              labels:
                app: sleep
            spec:
              containers:
              - name: sleep
                image: pstauffer/curl
                command: ["/bin/sleep", "3650d"]
                imagePullPolicy: IfNotPresent
        ```

    2.  In the **demo** namespace, run the kubectl apply -f sleep.yaml -n demo command.
    3.  Run the kubectl exec -it -n demo <podName\> bash command to log on to the pod where the `sleep` application runs, and then run the following commands to call the `nginx` service:

        ```
        for i in `seq 1000`
                 do
                 curl -I http://nginx.demo:8080; 
                 echo '';
                 sleep 1; 
                 done;
        ```

        If the service is called, the content shown in the following figure is displayed.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517243159_en-US.png)

    4.  Access the Kiali console to verify that the virtual service is called.

        **Note:** Before you perform this step, you must enable the Kiali visualized service mesh when deploying Istio for the target Kubernetes cluster.

        1.  Run the kubectl port-forward svc/kiali -n istio-system 20001:20001 command to open port 20001 in your local host.
        2.  Enter http://localhost:20001 in your browser.
        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517243160_en-US.jpg)

4.  Create a version of the virtual service for a Canary release.
    1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
    2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Service Mesh** \> **Virtual Service**.
    3.  On the right of the target virtual service, click **Manage**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517243161_en-US.jpg)

    4.  Click **Add new canary version**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517243162_en-US.jpg)

    5.  Set the **Version** and **Replicas**, then click **Next**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517243163_en-US.jpg)

    6.  Configure a container. Set **Image Name** to **nginx**, and set **Image Version** to **1.15**. For more information about other container configurations, see [Create a deployment application by using an image](../../../../reseller.en-US/User Guide/Kubernetes cluster/Application management/Create a deployment application by using an image.md#). Then, click **Next**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517343164_en-US.jpg)

    7.  Set the **Type** parameter to select a Canary deployment policy. For example, you can select the **Weight based Routing** radio button, and then set **v2Traffic Weight** to 30%.

        **Note:** 

        -   If you select the **Weight based Routing** radio button, then you can set the traffic distribution ratio for versions of the service that is to be deployed.
        -   You only need to set the **v2Traffic Weight** parameter. The **v1Traffic Weight** value is shown through automatic calculation.
        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517343165_en-US.jpg)

    8.  Click **Create**. After a creation success message is displayed by the system, choose **Service Mesh** \> **Virtual Services**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517343166_en-US.jpg)

    9.  On the right of the target service, click **Manage**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517343167_en-US.jpg)

    10. Run the kubectl exec -it -n demo <podName\> bash command to log on to the pod where the `sleep` application runs, and then run the following commands to call the `nginx` service:

        ```
        for i in `seq 1000`
                 do
                 curl -I http://nginx.demo:8080; 
                 echo '';
                 sleep 1; 
                 done;
        ```

        If the service is called, the content shown in the following figure is displayed.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517243159_en-US.png)

    11. Access the Kiali console to verify that the access traffic of the virtual service are distributed to the two versions according to the distribution ratio that you set.

        **Note:** Before you perform this step, you must enable the Kiali visualized service mesh when deploying Istio for the target Kubernetes cluster.

        1.  Run the kubectl port-forward svc/kiali -n istio-system 20001:20001 command to open port 20001 in your local host.
        2.  Enter http://localhost:20001 in your browser.
        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517343168_en-US.jpg)

5.  Edit the Canary release policy.
    1.  On the page that displays the details of the target virtual service, click **Edit the canary deployment policy**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517343169_en-US.jpg)

    2.  Change the traffic distribution ratio for the two versions of the target virtual services. For example, set **Version v1** to 20%, and set **Version v2** to 80%. Then, click **OK**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517343170_en-US.jpg)

    3.  In the upper-right corner, click **Refresh**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517343171_en-US.jpg)

    4.  Run the kubectl exec -it -n demo <podName\> bash command to log on to the pod where the `sleep` application runs, and then run the following commands to call the `nginx` service:

        ```
            for i in `seq 1000`
                 do
                 curl -I http://nginx.demo:8080; 
                 echo '';
                 sleep 1; 
                 done;
        ```

        If the service is called, the content shown in the following figure is displayed.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517243159_en-US.png)

    5.  In your browser, enter http://localhost:20001 to log on to the Kiali console to check how the target service is called.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517343172_en-US.jpg)

6.  Use the latest version of the target service to take over all the traffic that is destined for the earlier version.
    1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
    2.  In the left-side navigation pane, choose **Service Mesh** \> **Virtual Service**.
    3.  On the right of the target virtual service, click **Manage**.
    4.  In the **Version Management** area, find **v2**, and click **Take over all the traffic** in the **Action** column.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517343173_en-US.jpg)

    5.  In the displayed dialog box, click **Confirm**. Then, in the **Canary Deployment** column of **v2**, **Traffic Weight 100%** is displayed.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517343174_en-US.jpg)

    6.  Run the kubectl exec -it -n demo <podName\> bash command to log on to the pod where the `sleep` application runs, and then run the following commands to call the `nginx` service:

        ```
            for i in `seq 1000`
                 do
                 curl -I http://nginx.demo:8080; 
                 echo '';
                 sleep 1; 
                 done;
        ```

        If the service is called, the content shown in the following figure is displayed.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517243159_en-US.png)

    7.  In your browser, enter http://localhost:20001 to log on to the Kiali console to check how the target service is called.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517443175_en-US.jpg)

7.  Delete the earlier version of the target service.
    1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
    2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Service Mesh** \> **Virtual Service**.
    3.  On the right of the target virtual service, click **Manage**.
    4.  In the **Version Management** area, find **v1**, and click **Delete** in the **Action** column.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517443176_en-US.jpg)

        Then, only the latest version of the target service operates.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155840517443177_en-US.jpg)

        **Note:** If you want to release one more version of the target service by using the Canary deployment method, you can repeat the preceding steps.


