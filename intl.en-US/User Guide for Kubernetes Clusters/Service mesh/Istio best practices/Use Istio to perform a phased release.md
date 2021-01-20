# Use Istio to perform a phased release

Istio is a service mesh that makes up for the disadvantages of the distributed microservice architecture in O&M, debugging, and security management. It also supports load balancing, service-to-service authentication, and monitoring. Alibaba Cloud Container Service for Kubernetes \(ACK\) allows you to manage services of an application by using Istio. For example, you can create multiple versions of an application service, manage the distribution of traffic to the versions, and implement a phased release for the service.

## Prerequisites

-   A Kubernetes cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   Istio is deployed for the Kubernetes cluster. For more information, see [Deploy Istio](/intl.en-US/User Guide for Kubernetes Clusters/Service mesh/Istio management/Deploy Istio.md).

## Procedure

1.  Enable automatic sidecar injection.
    1.  Log on to the [ACK console](https://cs.console.aliyun.com).
    2.  In the left-side navigation pane, click **Clusters**.
    3.  On the Clusters page, find the cluster where you want to deploy the service and click the cluster name or **Details** in the **Actions** column.
    4.  In the left-side navigation pane, click **Namespaces**.
    5.  On the page that appears, click **Create** to create a namespace.
    6.  In the Create Namespace dialog box, set **Name** to demo and set **Label**. The following content shows how to set Label.

        -   Set **Variable Key** to istio-injection.
        -   Set **Variable Value** to enabled.
        **Note:** Click **Add**, and then click **OK**.

    7.  In the namespace list, view the created namespace.
2.  Create a virtual service
    1.  Log on to the [ACK console](https://cs.console.aliyun.com).
    2.  In the left-side navigation pane, choose **Service Mesh** \> **Virtual Services**.
    3.  In the upper-right corner of the page that appears, click **Create**.
    4.  On the Create Application page, configure the parameters and click **Next**. The following content describes the parameters.
        -   Name: The name of the application.
        -   Version: The version of the application, which is used for multiple version management.
        -   Cluster:The cluster where you want to deploy the application.
        -   Namespace: The namespace where you want to deploy the application. You must select the demo namespace created in Step 1.
        -   Replicas: The number of pods that run the application.
    5.  Configure the container. In this example, set **Image Name** to **nginx** and set **Image Version** to **1.14**. For information about how to set other parameters, see the related description in [Use an image to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image to create a stateless application.md). After you configure the parameters, click **Next**.
    6.  Configure the service.
        1.  In the upper-left corner of the Service Configuration page, click **Create** and configure the parameters in the dialog box that appears. The following content describes the parameters.
            -   Name: The name of the service, such as nginx. You can custom the name.
            -   Type: The type of the service. Valid value: Cluster IP. You must select Cluster IP to manage the service by using the Istio service mesh.
            -   Port Mapping: Set the service port name, service port number, container port number, and protocol. **You must set the service port name**. The format of a service port name is <Protocol\>\[-<Suffix\>-\]. The protocol can be gRPC, HTTP, HTTP/2, HTTPS, Mongo, Redis, TCP, TLS, or UDP. Istio supports the protocols to provide routing services.
            -   Annotations: Add an annotation to the service.
            -   Label: Add a label to the service.
        2.  After you configure the parameters, click **Create**.
    7.  In the lower-right corner of the Create Application wizard, click **Create**. Then, the system displays all the created resources if no error occurs. You can click View Details to view the application details.
3.  Create a client application named sleep.
    1.  Save the following content as the sleep.yaml file.

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

    2.  In the **demo** namespace, run the following command:

        `kubectl apply -f sleep.yaml -n demo`

    3.  Run the `kubectl exec -it -n demo <podName> bash` command to log on to the pod where the sleep application runs, and run the following command to call the Nginx service:

        ```
        for i in `seq 1000`
        do
        curl -I http://nginx.demo:8080; 
        echo '';
        sleep 1; 
        done;
        ```

        The following figure shows the response of a successful service call.

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0855359951/p43159.png)

    4.  Log on to the Kiali console to verify that the service is called. Before you perform this step, ensure that you have enabled the Kiali visualized service mesh when you deployed Istio for the Kubernetes cluster.

        Run the `kubectl port-forward svc/kiali -n istio-system 20001:20001` command to open port 20001 in your local host. Open a browser and enter http://localhost:20001 in the address box.

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8755359951/p43160.jpg)

4.  Create a version of the virtual service for a phased release.
    1.  Log on to the [ACK console](https://cs.console.aliyun.com).
    2.  In the left-side navigation pane, choose **Service Mesh** \> **Virtual Services**.
    3.  On the Virtual Services page, find the virtual service for which you want to create a version and click **Manage**.
    4.  On the page that appears, click **Add Canary Version**.
    5.  Set the application version and the number of pods. Click **Next**.
    6.  Configure the container. In this example, set **Image Name** to **nginx** and **Image Version** to **1.15**. For information about how to set other parameters, see the related description in [Use an image to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image to create a stateless application.md). After you configure the parameters, click **Next**.
    7.  Configure a phased release policy. For example, you can set **Type** to **Weight based Routing**, **v2Traffic Weight** to 30%, and **v1Traffic Weight** to 70%.
    8.  After you configure the parameters, click **Create**. Then, choose **Service Mesh** \> **Virtual Services**.
    9.  On the Virtual Services page, find the virtual service for which you want to create a version and click **Manage**.
    10. Run the `kubectl exec -it -n demo <podName> bash` command to log on to the pod where the sleep application runs, and run the following command to call the Nginx service:

        ```
        for i in `seq 1000`
        do
        curl -I http://nginx.demo:8080; 
        echo '';
        sleep 1; 
        done;
        ```

        The following figure shows the response of a successful service call.

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0855359951/p43159.png)

    11. Log on to the Kiali console to verify that the traffic destined for the virtual service is distributed to the two versions based on the specified phased release policy. Before you perform this step, ensure that you have enabled the Kiali visualized service mesh when you deployed Istio for the Kubernetes cluster.

        Run the `kubectl port-forward svc/kiali -n istio-system 20001:20001` command to open port 20001 in your local host. Open a browser and enter http://localhost:20001 in the address bar.

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9755359951/p43168.jpg)

5.  Modify the phased release policy.
    1.  On the page that displays the details of the virtual service, click **Edit Canary Release Policy**.
    2.  Modify the ratio of traffic distributed to the two versions of the virtual service. For example, set **Version v1** to 20 and **Version v2** to 80. Click **OK**.
    3.  In the upper-right corner, click **Refresh**
    4.  Run the `kubectl exec -it -n demo <podName> bash` command to log on to the pod where the sleep application runs, and run the following command to call the Nginx service:

        ```
            for i in `seq 1000`
            do
            curl -I http://nginx.demo:8080; 
            echo '';
            sleep 1; 
            done;
        ```

        The following figure shows the response of a successful service call.

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0855359951/p43159.png)

    5.  Open a browser and enter http://localhost:20001 in the address box to log on to the Kiali console. The following figure shows the traffic distribution of the service.

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0855359951/p43172.jpg)

6.  Use the latest version of the virtual service to manage all the traffic that is destined for the earlier version.
    1.  Log on to the [ACK console](https://cs.console.aliyun.com).
    2.  In the left-side navigation pane, choose **Service Mesh** \> **Virtual Services**.
    3.  On the Virtual Services page, find the virtual service and click **Manage**.
    4.  In the **Version Management** section, find **v2** and click **Handle All Traffic**.
    5.  After you confirm the operation, the traffic weight of **v2** changes to **100%** on the page.
    6.  Run the `kubectl exec -it -n demo <podName> bash` command to log on to the pod where the sleep application runs, and run the following command to call the Nginx service:

        ```
            for i in `seq 1000`
            do
            curl -I http://nginx.demo:8080; 
            echo '';
            sleep 1; 
            done;
        ```

        The following figure shows the response of a successful service call.

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0855359951/p43159.png)

    7.  Open a browser and enter http://localhost:20001 in the address box to log on to the Kiali console. The following figure shows the traffic distribution of the service.

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0855359951/p43175.jpg)

7.  Delete the earlier version of the service.
    1.  Log on to the [ACK console](https://cs.console.aliyun.com).
    2.  In the left-side navigation pane, choose **Service Mesh** \> **Virtual Services**.
    3.  On the right of the service, click **Manage**.
    4.  In the **Version Management** section, find **v1** and click **Delete**.
    5.  After you delete the earlier version, only v2 of the service is running. If you want to release one more version of the service by using the phased release method, repeat the preceding steps.

