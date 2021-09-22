---
keyword: [migration of application traffic, canary release]
---

# Use ASM to migrate TCP traffic

You can use Alibaba Cloud Service Mesh \(ASM\) to migrate traffic between different versions of an application. This topic describes how to use ASM to migrate TCP traffic.

-   The following services are activated:
    -   [Container Service](https://cs.console.aliyun.com/)
    -   [Server Load Balancer \(SLB\)](https://slb.console.aliyun.com/)
-   A Container Service for Kubernetes \(ACK\) cluster is created. For more information, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md) and [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   An ASM instance is created. The ACK cluster is added to the ASM instance. For more information, see [Create an ASM instance]() and [Add a cluster to an ASM instance]().

This topic describes how to migrate TCP traffic between two versions of a service by using the TCP Traffic Shifting sample task. This sample task is provided by the Istio official website. In this task, a simple tcp-echo service has two versions: v1 and v2. In the version v1, the service adds a prefix "one" to the content of each request as a response. In the version v2, the service adds a prefix "two" to the content of each request as a response.

## Step 1: Deploy the two versions of the sample application

1.  Deploy the two versions of the tcp-echo application.

    1.  Log on to the [Container Service console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Applications** in the **Actions** column.

    4.  At the top of the **Deployments** page, select a namespace from the **Namespace** drop-down list.

    5.  In the upper-right corner of the Deployments page, click **Create from YAML**.

    6.  On the **Create** page, copy the following YAML code to the **Template** editor and click **Create**.

        ```
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: tcp-echo-v1
        spec:
          replicas: 1
          selector:
            matchLabels:
              app: tcp-echo
              version: v1
          template:
            metadata:
              labels:
                app: tcp-echo
                version: v1
            spec:
              containers:
              - name: tcp-echo
                image: docker.io/istio/tcp-echo-server:1.1
                imagePullPolicy: IfNotPresent
                args: [ "9000", "one" ]
                ports:
                - containerPort: 9000
        ---
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: tcp-echo-v2
        spec:
          replicas: 1
          selector:
            matchLabels:
              app: tcp-echo
              version: v2
          template:
            metadata:
              labels:
                app: tcp-echo
                version: v2
            spec:
              containers:
              - name: tcp-echo
                image: docker.io/istio/tcp-echo-server:1.1
                imagePullPolicy: IfNotPresent
                args: [ "9000", "two" ]
                ports:
                - containerPort: 9000
        ```

        The two versions of the tcp-echo application appear on the **Deployments** page.

2.  Create a service that is named tcp-echo and expose it to external access.

    1.  Log on to the [Container Service console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Applications** in the **Actions** column.

    4.  In the left-side navigation pane of the details page, choose **Network** \> **Services**.

    5.  At the top of the **Services** page, select the namespace where you want to create a service from the **Namespace** drop-down list. Then, click **Create** in the upper-right corner of the page.

    6.  In the **Create Service** dialog box, set the relevant parameters and click **Create**.

        You must set the following parameters:

        -   **Name**: the name of the service. In this example, set the service name to tcp-echo.
        -   **Type**: the type of the service, which specifies how to expose the service. Valid values: **Cluster IP**, **Node Port**, and **Server Load Balancer**.

            **Note:** The **Headless Service** check box is displayed only when you set the service type to **Cluster IP**. If you select this check box, the service can be discovered and accessed by other types of services, instead of only by Kubernetes services.

        -   **Backend**: the deployment to be associated with the service. In this example, select tcp-echo-v1.

            **Note:** The service uses the app tag of the associated deployment as the selector to determine the deployment to which the traffic is routed. The tcp-echo-v1 and tcp-echo-v2 deployments share the same app tag, which is app:tcp-echo. Therefore, the service can be associated with either one of the two deployments.

        -   **External Traffic Policy**: the routing policy for inbound traffic. Valid values: Local and Cluster.

            **Note:** The **External Traffic Policy** parameter is displayed only when you set the service type to **Node Port** or **Server Load Balancer**.

        -   **Port Mapping**: the mappings of the service and container ports. Set the **Name** parameter to tcp, the **Service Port** parameter to 9000, the **Container Port** parameter to 9000, and the **Protocol** parameter to TCP.
        -   **Annotations**: the annotations of the service. You can set SLB parameters. For example, an annotation of `service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth:20` specifies that the peak bandwidth of the service is set to 20 Mbit/s. This allows the system to shape the service traffic. For more information, see [t16677.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use annotations to configure load balancing.md).
        -   **Label**: the one or more tags that you want to add to the service.
        After you create the tcp-echo service, it appears on the **Services** page.


## Step 2: Set routing rules for the ASM instance

You can create an Istio gateway, a virtual service, and a destination rule for the ASM instance to route all inbound traffic to the version v1 of the tcp-echo service.

1.  Log on to the [ASM console](https://servicemesh.console.aliyun.com).

2.  In the left-side navigation pane, choose **Service Mesh** \> **Mesh Management**.

3.  On the **Mesh Management** page, find the ASM instance that you want to configure. Click the name of the ASM instance or click **Manage** in the **Actions** column of the ASM instance.

4.  Create an Istio gateway.

    1.  On the details page of the ASM instance, choose **Traffic Management** \> **Gateway** in the left-side navigation pane. On the Gateway rules page, click **Create from YAML**.

    2.  In the **Create** panel, select default from the **Namespace** drop-down list and copy the following YAML code to the code editor. Then, click **OK**.

        ```
        apiVersion: networking.istio.io/v1alpha3
        kind: Gateway
        metadata:
          name: tcp-echo-gateway
        spec:
          selector:
            istio: ingressgateway
          servers:
          - port:
              number: 31400
              name: tcp
              protocol: TCP
            hosts:
            - "*"
        ```

5.  Create a virtual service.

    1.  On the details page of the ASM instance, choose **Traffic Management** \> **VirtualService** in the left-side navigation pane. On the Virtual service page, click **Create from YAML**.

    2.  In the **Create** panel, select default from the **Namespace** drop-down list and copy the following YAML code to the code editor. Then, click **OK**.

        ```
        apiVersion: networking.istio.io/v1alpha3
        kind: VirtualService
        metadata:
          name: tcp-echo
        spec:
          hosts:
          - "*"
          gateways:
          - tcp-echo-gateway
          tcp:
          - match:
            - port: 31400
            route:
            - destination:
                host: tcp-echo
                port:
                  number: 9000
                subset: v1
        ```

6.  Create a destination rule.

    1.  On the details page of the ASM instance, choose **Traffic Management** \> **DestinationRule** in the left-side navigation pane. On the Target rule page, click **Create from YAML**.

    2.  In the **Create** panel, select default from the **Namespace** drop-down list and copy the following YAML code to the code editor. Then, click **OK**.

        ```
        apiVersion: networking.istio.io/v1alpha3
        kind: DestinationRule
        metadata:
          name: tcp-echo-destination
        spec:
          host: tcp-echo
          subsets:
          - name: v1
            labels:
              version: v1
          - name: v2
            labels:
              version: v2
        ```


## Step 3: Deploy an ingress gateway service

Enable port 31400 in the ingress gateway service and point the port to port 31400 of the Istio gateway.

1.  Log on to the [ASM console](https://servicemesh.console.aliyun.com).

2.  In the left-side navigation pane, choose **Service Mesh** \> **Mesh Management**.

3.  On the **Mesh Management** page, find the ASM instance that you want to configure. Click the name of the ASM instance or click **Manage** in the **Actions** column of the ASM instance.

4.  On the details page of the ASM instance, click **ASM Gateways** in the left-side navigation pane. On the ASM Gateways page, click **Deploy Default Ingress Gateway**.

5.  In the **Deploy Ingress Gateway** panel, deploy an ingress gateway service in a cluster.

    1.  Select the cluster where you want to deploy an ingress gateway service from the **Cluster** drop-down list.

    2.  Set the SLB Instance Type parameter to **Internet Access**.

    3.  Configure an SLB instance.

        -   Use Existing SLB Instance: Select an existing SLB instance from the drop-down list.
        -   Create SLB Instance: Click **Create SLB Instance**. Then, select an instance specification type from the drop-down list.
        **Note:** We recommend that you assign a dedicated SLB instance to each Kubernetes service in the cluster. If multiple Kubernetes services share the same SLB instance, the following risks and limits may occur:

        -   If you assign a Kubernetes service with an SLB instance that is used by another Kubernetes service, the existing listeners of the SLB instance are forcibly overwritten. This may interrupt the original Kubernetes service and make your application unavailable.
        -   If you create an SLB instance when you create a Kubernetes service, the SLB instance cannot be shared among Kubernetes services. Only SLB instances that you create in the SLB console or by calling API operations can be shared.
        -   Kubernetes services that share the same SLB instance must use different frontend listening ports. Otherwise, port conflicts may occur.
        -   When multiple Kubernetes services share the same SLB instance, you must use the listener names and the vServer group names as unique identifiers in Kubernetes. Do not modify the names of listeners or vServer groups.
        -   You cannot share SLB instances across clusters or regions.
    4.  Click **Add Port** and set the **Name** parameter to tcp, the **Service Port** parameter to 31400, and the **Container Port** parameter to 31400.

        **Note:** The service port exposes the ASM instance to external access. Inbound traffic accesses the ASM instance through the service port and is routed to a port on the Istio gateway, which serves as the container port. Make sure that the container port is the same as the specified port on the Istio gateway.

    5.  Click **OK**.


## Step 4: Verify the deployment

Use the kubectl client to check whether the traffic to the tcp-echo service is routed as expected.

1.  Use the kubectl client to connect to the relevant Kubernetes cluster. For more information, see [Step 2: Select a type of cluster credentials](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md).

2.  Run the following commands to query the IP address and port number of the tcp-echo service:

    ```
    export INGRESS_HOST=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
    export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="tcp")].port}')
    ```

3.  Run the telnet command to connect to the tcp-echo service.

    ```
    telnet $INGRESS_HOST $INGRESS_PORT
    Trying xxx.xxx.xxx.xxx...
    Connected to xxx.xxx.xxx.xxx.
    Escape character is '^]'
    ```

4.  Enter a random string and press Enter. If the return string is prefixed with "one", the tcp-echo service is properly deployed and all the traffic is routed to the tcp-echo-v1 deployment.


## Step 5: Transfer a specific ratio of the traffic to the tcp-echo-v2 deployment

In this example, 20% of the traffic is routed to the tcp-echo-v2 deployment and the other 80% is routed to the tcp-echo-v1 deployment.

1.  Modify the configuration of the virtual service of the ASM instance.

    1.  On the details page of the ASM instance, choose **Traffic Management** \> **VirtualService** in the left-side navigation pane.

    2.  On the **VirtualService** page, click **YAML** in the **Actions** column of the tcp-echo virtual service.

    3.  In the **Edit** panel, copy the following YAML code to the code editor and click **OK**:

        ```
        apiVersion: networking.istio.io/v1alpha3
        kind: VirtualService
        metadata:
          name: tcp-echo
        spec:
          hosts:
          - "*"
          gateways:
          - tcp-echo-gateway
          tcp:
          - match:
            - port: 31400
            route:
            - destination:
                host: tcp-echo
                port:
                  number: 9000
                subset: v1
              weight: 80
            - destination:
                host: tcp-echo
                port:
                  number: 9000
                subset: v2
              weight: 20
        ```

2.  Run the following command to send 10 requests to the tcp-echo service:

    ```
     for i in {1..10}; do \
    docker run -e INGRESS_HOST=$INGRESS_HOST -e INGRESS_PORT=$INGRESS_PORT -it --rm busybox sh -c "(date; sleep 1) | nc $INGRESS_HOST $INGRESS_PORT"; \
    done
    one Mon Nov 12 23:38:45 UTC 2018
    two Mon Nov 12 23:38:47 UTC 2018
    one Mon Nov 12 23:38:50 UTC 2018
    one Mon Nov 12 23:38:52 UTC 2018
    one Mon Nov 12 23:38:55 UTC 2018
    two Mon Nov 12 23:38:57 UTC 2018
    one Mon Nov 12 23:39:00 UTC 2018
    one Mon Nov 12 23:39:02 UTC 2018
    one Mon Nov 12 23:39:05 UTC 2018
    one Mon Nov 12 23:39:07 UTC 2018
    ```

    The preceding command output indicates that 20% of the traffic is routed to the tcp-echo-v2 deployment.

    **Note:** If you send 10 requests in a test, the traffic may not always be routed to the tcp-echo-1 and tcp-echo-v2 deployments at the ratio of 80 to 20. However, the overall ratio is closer to 80:20 as the sample size increases.


