# Deploy Bookinfo in an ACK cluster

This topic describes how to deploy and run the Bookinfo sample application in an Alibaba Cloud Container Service for Kubernetes \(ACK\) cluster.

-   A managed Kubernetes cluster is created. For more information, see [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md).

    **Note:** The cluster version must be V1.12 or later.

-   A namespace is created for the cluster. For more information, see [Create a namespace](/intl.en-US/User Guide for Kubernetes Clusters/Namespace management/Create a namespace.md).
-   Istio is deployed for the cluster. For more information, see [Deploy Istio](/intl.en-US/User Guide for Kubernetes Clusters/Service mesh/Istio management/Deploy Istio.md).
-   Automatic sidecar injection is enabled for the cluster. For more information, see [Configure automatic sidecar injection](/intl.en-US/User Guide for Kubernetes Clusters/Service mesh/Istio management/Configure automatic sidecar injection.md).

## Bookinfo application overview

Bookinfo is a sample application that is used to demonstrate various Istio features. This application consists of the following four separate microservices:

-   `productpage`: This microservice calls the `details` and `reviews` microservices to populate the page.
-   `details`: This microservice contains the book information.
-   `reviews`: This microservice contains the book reviews. It also calls the `ratings` microservice.

    The `reviews` microservice has three versions:

    -   reviews v1 does not call the `ratings` microservice.
    -   reviews v2 calls the `ratings` microservice and uses one to five black stars to display ratings.
    -   reviews v3 calls the `ratings` service and uses one to five red stars to display ratings.
-   `ratings`: This microservice contains the book ratings that are based on book reviews.

The following figure shows the architecture of the application.

![Application architecture](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4855359951/p59173.png)

## Deploy Bookinfo

![Deploy Istio](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4855359951/p59174.png)

You can deploy productpage, details, ratings, reviews v1, and reviews v2 in the console.

This topic describes how to deploy productpage.

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Service Mesh** \> **Virtual Services**.

3.  On the **Virtual Services** page, click **Create** in the upper-right corner.

4.  Specify **Name**, **Version**, **Cluster**, **Namespace**, and **Replicas**. The Replicas parameter specifies the number of pods on which the application resides. Click **Next**.

    The istio-test namespace is used in this example.

    ![Configure the basic application information](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4855359951/p59175.png)

5.  Configure the container.

    Configure the image name, image version, required resources, port, and other parameters.

    ![The Container step](../images/p94985.png)

    **Note:**

    The following content describes how to configure the parameters:

    -   **Image Name**: Click Select Image. In the dialog box that appears, select an image and click **OK**. You can also enter the address of a private image repository. In this example, the image name is docker.io/istio/example-bookinfo-productpage-v1.
    -   **Image Version**: Click Select Image Version and select an image version. If you do not specify an image version, the latest version is used. In this example, the image version is 1.15.0 .
    -   **Required Resources**: The CPU and memory resources that are reserved for this application. These resources are exclusive to the container. This ensures that the application has sufficient resources when other services or processes compete for resources. In this example, 0.25 CPU core and 256 MiB memory are reserved.
    -   **Port Mapping**: You must add the host port and the container port. The TCP and UDP protocols are supported. In this example, the container port is 9080 and the protocol is TCP.
    For information about how to configure other parameters, see [Use an image to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image to create a stateless application.md).

6.  After you configure the parameters, click **Next**.

7.  Configure the service.

    1.  In the Services column, click **Create** and configure the service in the dialog box that appears.

        ![Service configuration](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4855359951/p59180.png)

        -   **Name**: You can enter a custom name. The default name is `applicationname-svc`.
        -   **Type**: Select one from the following three types.
            -   Cluster IP: enables access to the service through a private IP address of the cluster. If you select this type, the service is accessible only within the cluster.
            -   Node Port: enables access to the service through the IP address and static port of each node. The NodePort field specifies the static port. A NodePort service can route requests to a ClusterIP service. After you create a NodePort service, the system automatically creates a ClusterIP service. You can access a NodePort service from outside the cluster by requesting `<NodeIP>:<NodePort>`.
            -   Server Load Balancer: enables access to the service by using an SLB instance over the Internet or an internal network. The SLB instance can route requests to NodePort and ClusterIP services.
        -   **Port Mapping**: Specify the service port and container port. TCP and UDP are supported.
        -   **Annotations**: Add an annotation to the service. You can configure SLB instances in the annotation. For more information, see [Use annotations to configure SLB instances](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Use annotations to configure SLB instances.md).
        -   **Label**: Attach a label to the service.
    2.  Click **Create**.

        ![Application created](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4855359951/p59181.png)


You can use the following YAML files to create details, ratings, and reviews v1.

-   Create details

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

-   Create ratings

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

-   Create reviews v1

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


After you create the microservices, wait until all the microservices and pods are started.

## Create a service gateway

1.  In the left-side navigation pane, choose **Service Mesh** \> **Gateways**.

2.  Select the cluster and namespace, and click **Create** in the upper-right corner.

3.  Set the gateway parameters and click **Create**. The gateway is created.

    ![Create a gateway](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4855359951/p59214.png)

    Parameter description

    -   **Name**: the name of the gateway.
    -   **Service**: the proxy gateway. Default value: **istio-ingressgateway**. The proxy gateway and the gateway that you create must be deployed in the same namespace.
    -   **Ports**: the port that you can use to access the gateway. You can click **Add** to configure multiple ports for the gateway.
        -   Name: the port name.
        -   Protocol: the gateway protocol. Valid values: HTTP and TCP.
        -   Port: the port number. Valid values: 1 to 65535.
        -   Host: the gateway host. You can configure multiple hosts for the gateway.
    You can find the bookinfo-gateway gateway on the Gateways page.

    ![Gateways](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5855359951/p59215.png)

4.  Bind the preceding microservices to bookinfo-gateway. For more information, see [Bind a virtual service to a gateway](/intl.en-US/User Guide for Kubernetes Clusters/Service mesh/Bind a virtual service to a gateway.md).

    1.  In the left-side navigation pane under Container Service - Kubernetes, choose **Service Mesh** \> **Virtual Services**. The Virtual Services page appears.

        ![Virtual Services](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5855359951/p59218.png)

        **Note:** In the **Gateways** column, the **Bind** button is available for virtual services that are not bound to gateways.

    2.  In the Gateways column, find the virtual service that you want to bind and click **Bind**. The Add page appears.

        ![Bind a service gateway](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5855359951/p59220.png)

    3.  Select bookinfo-gateway from the Name drop-down list and click **OK**. The virtual service is bound to the gateway.

5.  On the Gateways page, find the bookinfo-gateway gateway and click **istio-ingressgateway** in the Services column, and enter Ingress domain name/productpage.

    ![Gateways page](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5855359951/p59226.png)

    The following page appears.

    ![Bookinfo Sample page](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5855359951/p59225.png)


## Create reviews v2.

1.  In the left-side navigation pane under Container Service - Kubernetes, choose **Service Mesh** \> **Virtual Services**. The Virtual Services page appears.

2.  Find the service that you want to manage and click **Manage** in the Actions column to go to the details page.

    ![Manage services](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5855359951/p59229.png)

3.  Click **Add Canary Version**.

    ![Add a canary version](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5855359951/p59231.png)

4.  Go to the Create Application page. You can configure the parameters as needed.

    1.  On the Basic Information page, specify **Version** and **Replicas**, and click **Next**.

    2.  On the Container page, set the image name, image version, required resources, container port, and other configuration information, and click **Next**.

        ![Configure container parameters](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5855359951/p59236.png)

        **Note:**

        The following content describes how to configure the parameters:

        -   **Image Name**: Click Select Image. In the dialog box that appears, select an image, and click **OK**. You can also enter the address of a private image repository. In this example, the image name is Docker. io/istio/example-bookinfo-productpage-v2.
        -   **Image Version**: Click Select Image Version and select an image version. If you do not specify an image version, the latest version is used. In this example, the image version is 1.15.0.
        -   **Required Resources**: The CPU and memory resources that are reserved for this application. These resources are exclusive to the container. This ensures that the application has sufficient resources when other services or processes compete for resources. In this example, 0.25 CPU core and 256 MiB memory are reserved.
        -   **Port Mapping**: You must add the host port and the container port. The TCP and UDP protocols are supported. In this example, the container port is 9080 and the protocol is TCP.
        For information about how to configure other parameters, see [Use an image to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image to create a stateless application.md).

    3.  On the Canary Release page, select Weight based Routing. In the Traffic Weight column, set the traffic weight to 50%, and click **Create**.

5.  After the pod is started, enter Ingress domain name/productpage. The page of reviews v2 appears.

    Each of v1 and v2 accounts for 50% of the traffic. You may need to refresh the page several times.

    ![bookinfo-sample](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5855359951/p59239.png)


You can use stress testing tools, such as siege, to simulate multiple visits, and then observe the traffic topology by using Kiali.

Kiali is installed by default when Istio is deployed. You only need to create an ingress to access Istio. For more information, see [t16682.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Create an Ingress on the web UI.md).

**Note:** In this example, the cluster where Istio is deployed and the isti-system namespace are selected.

After the ingress is created, the URL of Kiali appears in the ingress list.

![URL](../images/p59243.png)

Click the URL of Kiali to log on to the Kiali console. You can see the following results.

![Results](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5855359951/p59244.png)

**Note:** By default, the username and password are set to admin.

