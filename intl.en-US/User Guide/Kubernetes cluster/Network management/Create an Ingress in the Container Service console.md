# Create an Ingress in the Container Service console {#concept_qm4_kyn_vdb .concept}

Alibaba Cloud Container Service console integrates with the Ingress service, which allows you to quickly create an Ingress service in the Container Service console to build the flexible and reliable traffic access layer.

## Prerequisites {#section_kj5_nyn_vdb .section}

-   You have successfully created a Kubernetes cluster and Ingress controller is running normally in the cluster. For how to create a Kubernetes cluster, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   Log on to the master node by using SSH. For more information, see [Access Kubernetes clusters by using SSH](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Access a Kubernetes cluster by using SSH.md#).

## Step 1: Create a deployment and a service {#section_c1p_ryn_vdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Applications** \> **Deployments**.
3.  Click **Create by template** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16682/156350794110771_en-US.png)

4.  Select the cluster and namespace from the Clusters and Namespace drop-down lists. Select a sample template or Custom from the Resource Type drop-down list. Click **DEPLOY**.

    In this example, three nginx applications are created. One for the old application \(old-nginx\), one for the new \(new-nginx\), and an application for testing the cluster access domain name \(domain-nginx\).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16682/156350794210772_en-US.png)

    The orchestration template for old-nginx is as follows:

    ```
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
      name: old-nginx
    spec:
      replicas: 2
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
      name: old-nginx
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: old-nginx
      sessionAffinity: None
      type: NodePort
    ```

    The orchestration template for new-nginx is as follows:

    ```
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
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
      name: new-nginx
    spec: 
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: new-nginx
      sessionAffinity: None
      type: NodePort
    ```

    The orchestration template for domain-nginx is as follows:

    ```
    apiVersion: apps/v1beta2 # For versions before 1.8.0 use apps/v1beta1
    kind: Deployment
    metadata:
      name: domain-nginx
      labels:
        app: nginx
    spec:
      replicas: 2
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:1.7.9 # replace it with your exactly <image_name:tags>
            ports:
            - containerPort: 80
    
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: domain-nginx
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        app: nginx
      sessionAffinity: None
      type: NodePort
    ```

5.  In the left-side navigation pane under Container Service-Kubernetes, choose **Discovery and Load Balancing** \> **Services**.

    After the service is created, you can see it on the Service List page.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16682/156350794210773_en-US.png)


## Step 2: Create an Ingress {#section_gwv_tyn_vdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Discovery and Load Balancing** \> **Ingresses**.
3.  Select the cluster and namespace from the Clusters and Namespace drop-down lists. Then click **Create** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16682/156350794210774_en-US.png)

4.  In the displayed dialog box, enter the Ingress name. In this example, enter nginx-ingress.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16682/156350794210775_en-US.png)

5.  Configure the rules.

    The Ingress rules are the rules that authorize the inbound access to the cluster and are generally the HTTP rules. Configure the domain name \(virtual hostname\), URL path, service name, and port. For more information, see [Ingress configurations](reseller.en-US/User Guide/Kubernetes cluster/Network management/Ingress configurations.md#).

    In this example, add a complicated Ingress rule. Configure the default test domain name and virtual hostname of the cluster to display the Ingress service based on the domain names.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16682/156350794210776_en-US.png)

    -   The simple Ingress based on the default domain name, that is, provide the access service externally by using the default domain name of the cluster.
        -   **Domain**: Enter the default domain name of the cluster. In this example, use `test.[cluster-id].[region-id].alicontainer.com`.

            The default domain name of this cluster is displayed in the Create dialog box, in the `*.[cluster-id].[region-id].alicontainer.com` format. You can also obtain the default domain name on the Basic Information page of the cluster.

        -   **Service**: Configure the access path, name, and port of the service.
            -   Path: Specify the URL path of the service access. The default is the root path /, which is not configured in this example. Each path is associated with a backend service. Before Alibaba Cloud Server Load Balancer forwards the traffic to the backend, all inbound requests must match with the domain name and path.
            -   Service configuration: The backend configuration, which is a combination of service name, port, and service weight. The configuration of multiple services in the same access path is supported, and Ingress traffic is split and is forwarded to the matched backend services.
    -   The simple fanout Ingress based on the domain name. In this example, use a virtual hostname as the testing domain name to provide the access service externally. You can use the recorded domain name in the production environment to provide the access service. You can use the recorded domain name in the production environment to provide the access service.
        -   **Domain**: In this example, use the testing domain name `foo.bar.com`.

            You must modify the hosts file to add a domain name mapping rule.

            ```
            118.178.108.143 foo.bar.com #  Ingress IP address
            ```

        -   **Service**: Configure the access path, name, and port of the service.
            -   Path: Specify the URL path of the service access. Path is not configured in this example, and the root path is /.
            -   Name: In this example, set up both new and old services, nginx-new and nginx-old.
            -   Port: Expose 80 port.
            -   Weight settings: Set the weight of multiple services under this path. The service weight is calculated by relative value. The default value is 100. As shown in this example, the service weight values of both the old and new versions are 50, which means that the weight rate of both services is 50%.
6.  Grayscale publish configuration.

    **Note:** Currently, the Alibaba Cloud Container Service Kubernetes Ingress Controller requires 0.12.0-5 and above to support the traffic segmentation feature.

    Container Service supports different traffic segmentation methods for grayscale publish and AB test scenarios.

    1.  Traffic segmentation based on the request header.
    2.  Traffic segmentation based on cookie.
    3.  Traffic segmentation based on query \(request\) parameters.
    After the grayscale rule is configured, the request that matches the grayscale publish rule can be routed to the new service version new-nginx. If the service sets a weight rate of less than 100%, requests that match the grayscale publish rule continue to be routed to the corresponding service based on the weight rate.

    In this case, set the request header to meet a grayscale publish rule of `foo=^bar$`, only requests with the request header can access the new-nginx service.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16682/156350794210777_en-US.png)

    -   **Service**: Routing rule configuration service.
    -   **Type**: matching request header, cookie, and query \(request\) parameters are supported.
    -   **Name and match value**: User-defined request field, name and match value are key-value pairs.
    -   **Match rules**: Regular and exact matches are supported.
7.  Configure the annotations.

    Click **rewrite annotation**, a typical redirection annotation can be added to the route. `nginx.ingress.kubernetes.io/rewrite-target：/` indicates that the /path is redirected to the root path / that the backend service can recognize.

    **Note:** In this example, the access path is not configured, so no need to configure rewrite annotations. The purpose of the rewrite annotation is to enable Ingress to forward to the backend as the root path, avoiding 404 errors caused by incorrect access path configuration.

    You can also click **Add** to enter the annotation name and value, which is the annotation key-value pair for Ingress. For more information, see [https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16682/156350794210778_en-US.png)

8.  Configure TLS. Select **Enable** and configure the secure Ingress service. For more information, see [Configure a safe routing service](reseller.en-US/User Guide/Kubernetes cluster/Network management/Support for Ingress.md#section_j4d_jrs_vdb).
    -   You can select to use an existing secret.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16682/156350794310779_en-US.png)

        1.  Log on to the master node and create tls.key and tls.crt.

            ```
            openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=foo.bar.com/O=foo.bar.com"
            ```

        2.  Create a secret.

            ```
            kubectl create secret tls foo.bar --key tls.key --cert tls.crt
            ```

        3.  Run the `kubectl get secret` command to see that secret has been successfully created. You can use the secret that you have created in the Web interface, foo.bar.
    -   You can create the secret with one click by using the created TLS private key and certificate.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16682/156350794310781_en-US.png)

        1.  Log on to the master node and create tls.key and tls.crt.

            ```
            openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=foo.bar.com/O=foo.bar.com"
            ```

        2.  Run the `vim tls.key` and `vim tls.crt` to get the generated private key and certificate.
        3.  Copy the generated certificate and private key to the Cert and Key fields.
9.  Add the tags.

    Add the corresponding tags for Ingress to indicate the characteristics of the Ingress.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16682/156350794310783_en-US.png)

10. Click **Create**.

    The Ingress nginx-ingress is displayed on the Ingress page.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16682/156350794310784_en-US.png)

11. Click on the access domain name `test.[cluster-id].[region-id].alicontainer.com` in the route, and `foo.bar.com` to access the welcome page of nginx.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16682/156350794310786_en-US.png)

    Click on the route address pointing the new-nginx service and find the page that points the old-nginx application.

    **Note:** Access the route address in the browser. By default, the request header does not have the `foo=^bar$`, so the traffic is directed to the old-nginx application.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16682/156350794310791_en-US.png)

12. Log on to the master node by using SSH. Run the following command to simulate the access result with a specific request header.

    ```
    curl -H "Host: foo.bar.com" http://47.107.20.35
    old
     curl -H "Host: foo.bar.com" http://47.107.20.35
    old
     curl -H "Host: foo.bar.com" http://47.107.20.35 # Similar to browser access requests
    old
     curl -H "Host: foo.bar.com" -H "foo: bar" http://47.107.20.35 # Simulate an access request with a unique header, returning results based on routing weight
    new
     curl -H "Host: foo.bar.com" -H "foo: bar" http://47.107.20.35
    old
     curl -H "Host: foo.bar.com" -H "foo: bar" http://47.107.20.35
    old
     curl -H "Host: foo.bar.com" -H "foo: bar" http://47.107.20.35
    new
    ```


