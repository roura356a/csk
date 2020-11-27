# Deploy Bookinfo in ACK

This topic describes how to deploy and run the Bookinfo application in Container Service for Kubernetes \(ACK\).

Before you deploy Bookinfo in ACK, make sure that you have performed the following operations:

-   [Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md).

    **Note:** The cluster version must be v1.12 or later.

-   [Create a namespace](/intl.en-US/User Guide for Kubernetes Clusters/Namespace management/Create a namespace.md).
-   [Deploy Istio](/intl.en-US/User Guide for Kubernetes Clusters/Service mesh/Istio management/Deploy Istio.md).
-   [Configure automatic sidecar injection](/intl.en-US/User Guide for Kubernetes Clusters/Service mesh/Istio management/Configure automatic sidecar injection.md).

## Bookinfo application overview

Bookinfo is a sample application used to demonstrate various Istio features. This application consists of the following four separate microservices:

-   `productpage`: calls the `details` and `reviews` microservices to populate the page.
-   `details`: contains book information.
-   `reviews`: contains book reviews. It also calls the `ratings` microservice.

    The `reviews` microservice has the following three versions:

    -   Version v1 does not call the `ratings` service.
    -   Version v2 calls the `ratings` service and displays each rating with one to five black stars.
    -   Version v3 calls the `ratings` service and displays each rating with one to five red stars.
-   `ratings`: contains book ratings that are generated based on book reviews.

The end-to-end architecture of the application is shown in the following figure.

![Architecture](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4855359951/p59173.png)

## Deploy the Bookinfo application

![Deploy Istio](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4855359951/p59174.png)

You can deploy productpage, details, ratings, and the two versions \(v1 and v2\) of the reviews service in the ACK console.

This example describes how to deploy the productpage microservice.

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Service Mesh** \> **Virtual Services**.

3.  On the **Virtual Services** page, click **Create** in the upper-right corner of the page.

4.  Set the parameters such as **Name**, **Version**, **Cluster**, **Namespace**, and **Replicas**. The Replicas parameter specifies the number of pods on which the application runs. Click **Next** to go to the Container wizard page.

    The istio-test namespace is used in this example.

    ![Configure basic information for the application](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4855359951/p59175.png)

5.  Configure the container.

    Set the parameters such as Image Name, Image Version, Required Resources, and Port.

    **Note:**

    In this example, the parameters are set to the following values:

    -   **Image Name**: Click Select Image. In the dialog box that appears, select an image and click **OK**. You can also enter the address of a private image registry. This parameter is set to docker.io/istio/example-bookinfo-productpage-v1 in this example.
    -   **Image Version**: Click Select Image Version and select an image version. If you do not specify this parameter, the latest version is used. The image version is 1.15.0 in this example.
    -   **Required Resources**: The amount of CPU and memory resources that are reserved for this application. These resources are exclusive to the container. This prevents the application from becoming unavailable when other services or processes compete for resources. In this example, 0.25 CPU core and 256 MiB of memory are reserved.
    -   **Port**: You must add the host port and the container port. Both TCP and UDP are supported. In this example, the container port is 9080 and the protocol is TCP.
    For more information about how to set other parameters, see [Use an image to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image to create a stateless application.md).

6.  After you set the required parameters, click **Next**.

7.  Configure the service.

    1.  On the Service Configuration wizard page, click **Create** next to Services. In the Create Service dialog box, set the required parameters.

        ![Service configuration](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4855359951/p59180.png)

        -   **Name**: You can enter a custom name. The default name is `applicationname-svc`.
        -   **Type**: Select one of the following types:
            -   Cluster IP: enables access to the service through an internal IP address of the cluster. If you select this type, the service is only accessible within the cluster.
            -   Node Port: enables access to the service by using the IP address and static port on each node. The NodePort field specifies the static port. A NodePort service can be used to route requests to a ClusterIP service. The ClusterIP service is automatically created by the system. You can access the NodePort service from outside the cluster by sending requests to `<NodeIP>:<NodePort>`.
            -   Server Load Balancer: enables access to the service by using a Server Load balancer \(SLB\) instance over the Internet or through an internal network. The SLB instance can route requests to NodePort and ClusterIP services.
        -   **Port Mapping**: You must specify the service port and container port. Both TCP and UDP are supported.
        -   **Annotations**: Add an annotation to the service. SLB parameters are supported. For more information, see [Use annotations to configure SLB instances](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Use annotations to configure SLB instances.md).
        -   **Label**: Add a label to the service.
    2.  Click **Create**.

        ![Creation complete](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4855359951/p59181.png)


You can use the following YAML files to create the details, ratings, and reviews \(v1\) services:

-   Create the details service

    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: details
      labels:
        app: details
        service: details
    spec:
      ports:
      - port: 9080
        name: http
      selector:
        app: details
    ---
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: bookinfo-details
      labels:
        account: details
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: details-v1
      labels:
        app: details
        version: v1
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: details
          version: v1
      template:
        metadata:
          labels:
            app: details
            version: v1
        spec:
          serviceAccountName: bookinfo-details
          containers:
          - name: details
            image: docker.io/istio/examples-bookinfo-details-v1:1.15.0
            imagePullPolicy: IfNotPresent
            ports:
            - containerPort: 9080
    ```

-   Create the ratings service

    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: ratings
      labels:
        app: ratings
        service: ratings
    spec:
      ports:
      - port: 9080
        name: http
      selector:
        app: ratings
    ---
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: bookinfo-ratings
      labels:
        account: ratings
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: ratings-v1
      labels:
        app: ratings
        version: v1
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: ratings
          version: v1
      template:
        metadata:
          labels:
            app: ratings
            version: v1
        spec:
          serviceAccountName: bookinfo-ratings
          containers:
          - name: ratings
            image: docker.io/istio/examples-bookinfo-ratings-v1:1.15.0
            imagePullPolicy: IfNotPresent
            ports:
            - containerPort: 9080
    ```

-   Create the reviews \(v1\) service

    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: reviews
      labels:
        app: reviews
        service: reviews
    spec:
      ports:
      - port: 9080
        name: http
      selector:
        app: reviews
    ---
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: bookinfo-reviews
      labels:
        account: reviews
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: reviews-v1
      labels:
        app: reviews
        version: v1
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: reviews
          version: v1
      template:
        metadata:
          labels:
            app: reviews
            version: v1
        spec:
          serviceAccountName: bookinfo-reviews
          containers:
          - name: reviews
            image: docker.io/istio/examples-bookinfo-reviews-v1:1.15.0
            imagePullPolicy: IfNotPresent
            ports:
            - containerPort: 9080
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: reviews-v2
      labels:
        app: reviews
        version: v2
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: reviews
          version: v2
      template:
        metadata:
          labels:
            app: reviews
            version: v2
        spec:
          serviceAccountName: bookinfo-reviews
          containers:
          - name: reviews
            image: docker.io/istio/examples-bookinfo-reviews-v2:1.15.0
            imagePullPolicy: IfNotPresent
            ports:
            - containerPort: 9080
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: reviews-v3
      labels:
        app: reviews
        version: v3
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: reviews
          version: v3
      template:
        metadata:
          labels:
            app: reviews
            version: v3
        spec:
          serviceAccountName: bookinfo-reviews
          containers:
          - name: reviews
            image: docker.io/istio/examples-bookinfo-reviews-v3:1.15.0
            imagePullPolicy: IfNotPresent
            ports:
            - containerPort: 9080
    ```


After you create these services, wait until all services and pods are running.

## Create a service gateway

1.  In the left-side navigation pane, choose **Service Mesh** \> **Gateways**.

2.  On the Gateways page, select a cluster and namespace and click **Create** in the upper-right corner of the page.

3.  Set the required parameters and click **Create** to deploy a gateway.

    ![Deploy a gateway](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4855359951/p59214.png)

    Parameter description

    -   **Name**: the name of the gateway.
    -   **Service**: the proxy gateway. Default value: **istio-ingressgateway**. The proxy gateway and the gateway that you create must be deployed in the same namespace.
    -   **Ports**: the port that you can use to access the gateway. You can click **Add** to configure multiple ports for the gateway.
        -   Name: the port name.
        -   Protocol: the gateway protocol. Valid values: HTTP and TCP.
        -   Port: the port number. Valid values: 1 to 65535.
        -   Host: the gateway host. You can configure multiple hosts for the gateway.
    After you set the parameters, you can view the created service gateway on the Gateways page.

    ![Service gateway](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5855359951/p59215.png)

4.  Bind the preceding services to the Bookinfo-gateway gateway. For more information, see [Bind a virtual service to a gateway](/intl.en-US/User Guide for Kubernetes Clusters/Service mesh/Bind a virtual service to a gateway.md).

    1.  In the left-side navigation pane of the ACK console, choose **Service Mesh** \> **Virtual Services**. On the Virtual Servicespage, you can view the service list.

        ![Virtual services](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5855359951/p59218.png)

        **Note:** In the **Gateways** column, the **Bind** button is available for virtual services that are not bound to gateways.

    2.  Find a created service and click **Bind** in the Gateways column. The Add dialog box appears.

        ![Bind a service gateway](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5855359951/p59220.png)

    3.  Select a gateway from the Name drop-down list and click **OK**. The virtual service is bound to the gateway.

5.  On the Gateways page, find the gateway to which the virtual service is bound, click **istio-ingressgateway** in the Services column, and enter Ingress domain name/productpage.

    ![Service gateway](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5855359951/p59226.png)

    The following page appears.

    ![Bookinfo sample](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5855359951/p59225.png)


## Deploy the reviews \(v2\) service

1.  In the left-side navigation pane of the ACK console, choose **Service Mesh** \> **Virtual Services**. On the Virtual Services page, you can view the service list.

2.  Find the service that you want to manage and click **Manage** in the Actions column to go to the details page.

    ![Manage the service](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5855359951/p59229.png)

3.  Click **Add Canary Version**.

    ![Add a canary version](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5855359951/p59231.png)

4.  The Create Application wizard appears. Set the parameters based on your business requirements.

    1.  On the Basic Information wizard page, set the **Version** and **Replicas** parameters. Then, click **Next**.

    2.  On the Container wizard page, set the parameters such as Image Name, Image Version, Required Resources, and Port. Then, click **Next**.

        ![Set parameters for the container](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5855359951/p59236.png)

        **Note:**

        In this example, the parameters are set to the following values:

        -   **Image Name**: Click Select Image. In the dialog box that appears, select an image and click **OK**. You can also enter the address of a private image registry. This parameter is set to docker.io/istio/examples-bookinfo-productpage-v2 in this example.
        -   **Image Version**: Click Select Image Version and select an image version. If you do not specify this parameter, the latest version is used. The image version is 1.15.0 in this example.
        -   **Required Resources**: The amount of CPU and memory resources that are reserved for this application. These resources are exclusive to the container. This prevents the application from becoming unavailable when other services or processes compete for resources. In this example, 0.25 CPU core and 256 MiB of memory are reserved.
        -   **Port**: You must add the host port and the container port. The TCP and UDP protocols are supported. In this example, the container port is 9080 and the protocol is TCP.
        For more information about how to set other parameters, see [Use an image to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image to create a stateless application.md).

    3.  On the Canary Release wizard page, select Weight based Routing in the Type field. In the Traffic Weight field, set v2Traffic Weight to 50% and click **Create**.

5.  After the pod is started, enter Ingress domain name/productpage. The page of the reviews service \(v2\) appears.

    50% of traffic is routed to the v1 version of the reviews service and the other 50% of traffic is routed to the v2 version of the service. You may need to refresh the page several times to view different results.

    ![bookinfo-sample](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5855359951/p59239.png)


You can use load testing tools, such as Siege, to simulate multiple visits, and then observe the traffic topology by using Kiali.

By default, Kiali is installed when you deploy Istio. You only need to create an ingress to access Istio. For more information, see [t16682.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Create an Ingress on the web UI.md).

**Note:** In this example, the cluster where Istio is deployed and the istio-system namespace are selected.

After the ingress is created, you can view the URL of Kiali on the Ingresses page.

![URL](../images/p59243.png)

Click the URL of Kiali to log on to the Kiali console. The following results are displayed.

![Call results](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5855359951/p59244.png)

**Note:** By default, the username and password are both admin.

