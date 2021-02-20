# Use an Ingress to provide Layer 7 service access

In an Alibaba Cloud Serverless Kubernetes cluster, you can use Server Load Balancer \(SLB\) to provide Layer 4 service access. You can also use an Ingress to provide Layer 7 service access. This topic describes how to provide Layer 7 service access in a Serverless Kubernetes cluster.

-   A Serverless Kubernetes cluster is created. You must configure a network address translation \(NAT\) gateway for the VPC where the cluster is located so that the cluster can download container images from the Internet.[Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md)
-   The kubectl client is connected to the Serverless Kubernetes cluster. For more information, see [Use kubectl to connect to an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Cluster management/Manage and access clusters/Use kubectl to connect to an ASK cluster.md).

## Notes

-   If you do not specify an SLB instance, Container Service for Kubernetes \(ACK\) automatically generates a public SLB instance.
-   The default frontend listener ports for the SLB instance are ports 80 \(HTTP\) and 443 \(HTTPS\).
-   By default, the HTTPS certificate of the SLB instance is initialized to the TLS certificate of the Ingress that is created first. If no TLS certificate is configured for the first Ingress, the HTTPS certificate is initialized to the system default certificate. You can modify the HTTPS certificate in the SLB console as needed.
-   If you use an existing SLB instance, the SLB instance must be a high-performance instance that supports Elastic Network Interface \(ENI\). In addition, make sure that ports 80 and 443 are not used by other services.

## Use the default SLB instance

If you do not specify an SLB instance, ACK automatically generates a public SLB instance of the high-performance type when the first Ingress is created.

1.  Deploy test services.

    1.  Create the cafe-service.yaml file, copy the following code to the file, and then run the `kubectl apply -f cafe-service.yaml` command to deploy the coffee service and tea service:

        ```
        apiVersion: extensions/v1beta1
        kind: Deployment
        metadata:
          name: coffee
        spec:
          replicas: 2
          selector:
            matchLabels:
              app: coffee
          template:
            metadata:
              labels:
                app: coffee
            spec:
              containers:
              - name: coffee
                image: registry.cn-hangzhou.aliyuncs.com/acs-sample/nginxdemos:latest
                ports:
                - containerPort: 80
        ---
        apiVersion: v1
        kind: Service
        metadata:
          name: coffee-svc
        spec:
          ports:
          - port: 80
            targetPort: 80
            protocol: TCP
          selector:
            app: coffee
          clusterIP: None
        ---
        apiVersion: extensions/v1beta1
        kind: Deployment
        metadata:
          name: tea
        spec:
          replicas: 1
          selector:
            matchLabels:
              app: tea 
          template:
            metadata:
              labels:
                app: tea 
            spec:
              containers:
              - name: tea 
                image: registry.cn-hangzhou.aliyuncs.com/acs-sample/nginxdemos:latest
                ports:
                - containerPort: 80
        ---
        apiVersion: v1
        kind: Service
        metadata:
          name: tea-svc
          labels:
        spec:
          ports:
          - port: 80
            targetPort: 80
            protocol: TCP
          selector:
            app: tea
          clusterIP: None
        ```

        If the following result appears, the coffee and tea services are deployed:

        ```
        deployment "coffee" created
        service "coffee-svc" created
        deployment "tea" created
        service "tea-svc" created
        ```

    2.  Run the `kubectl get svc,deploy` command to view the service status.

        ```
        NAME             TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
        svc/coffee-svc   ClusterIP   <none>       <none>        80/TCP    1m
        svc/tea-svc      ClusterIP   <none>       <none>        80/TCP    1m
        
        NAME            DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
        deploy/coffee   2         2         2            2           1m
        deploy/tea      1         1         1            1           1m
        ```

2.  Configure an Ingress.

    1.  Create the `cafe-ingress.yaml` file, and copy the following code to the file. Then, run the `kubectl apply -f cafe-ingress.yaml` command to configure the domain name and paths of the coffee service and tea service.

        ```
        apiVersion: extensions/v1beta1
        kind: Ingress
        metadata:
          name: cafe-ingress
        spec:
          rules:
          # Configure a Layer 7 domain name.
          - host: foo.bar.com
            http:
              paths:
              # Configure a context path.
              - path: /tea
                backend:
                  serviceName: tea-svc
                  servicePort: 80
              # Configure a context path.
              - path: /coffee
                backend:
                  serviceName: coffee-svc
                  servicePort: 80
        ```

        If the following result appears, the Ingress is configured:

        ```
        ingress "cafe-ingress" created
        ```

    2.  Run the `kubectl get ing` command to obtain the IP address of the SLB instance.

        ```
        NAME           HOSTS         ADDRESS          PORTS     AGE
        cafe-ingress   foo.bar.com   139. ***. **.***   80        1m
        ```

3.  Access the test services.

    **Note:** You must resolve the domain name to the IP address of the SLB instance.

    In this example, add the following DNS rule to the hosts file to access the test services. We recommend that you apply for an Internet Content Provider \(ICP\) filing for the domain name in your production environment.

    ```
    139.***. **.***    foo.bar.com
    ```

    -   Access the coffee service by using a browser.

        ![Access the coffee service](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9297297951/p10319.png)

    -   Access the coffee service by using the command line tool.

        ```
        curl -H "Host: foo.bar.com" http://139. ***. **.***/coffee
        ```

    -   Access the tea service by using a browser.

        ![Access the tea service](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9297297951/p10320.png)

    -   Access the tea service by using the command line tool.

        ```
        curl -H "Host: foo.bar.com" http://139. ***. **.***/tea
        ```


## Use an existing SLB instance

You can use the `service.beta.kubernetes.io/alicloud-loadbalancer-id` annotation to specify an existing SLB instance. The SLB instance must be a high-performance instance that supports ENI.

**Note:** ACK automatically initializes ports 80 and 443 for the SLB instance. Make sure that the ports are not used by other services.

1.  Deploy a test service.

    1.  Create the `tomcat-service.yml` file, copy the following code to the file, and run the `kubectl apply -f tomcat-service.yml` command to deploy a Tomcat test service:

        ```
        apiVersion: extensions/v1beta1
        kind: Deployment
        metadata:
          name: tomcat
        spec:
          replicas: 1
          selector:
            matchLabels:
              run: tomcat
          template:
            metadata:
              labels:
                run: tomcat
            spec:
              containers:
              - image: tomcat:7.0
                imagePullPolicy: Always
                name: tomcat
                ports:
                - containerPort: 8080
                  protocol: TCP
              restartPolicy: Always
        ---
        apiVersion: v1
        kind: Service
        metadata:
          name: tomcat
        spec:
          ports:
          - port: 8080
            protocol: TCP
            targetPort: 8080
          selector:
            run: tomcat
          clusterIP: None
        ```

        If the following result appears, the Tomcat service is deployed:

        ```
        deployment "tomcat" created
        service "tomcat" created
        ```

    2.  Run the `kubectl get svc,deploy tomcat` command to view the service status.

        ```
        NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
        svc/tomcat   ClusterIP   <none>       <none>        8080/TCP   1m
        
        NAME            DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
        deploy/tomcat   1         1         1            1           1m
        ```

2.  Apply for an SLB instance.

    You must apply for a **high-performance** SLB instance such as slb.s2.small in the same region as the cluster. You can specify an internal or a public instance based on business requirements. For more information, see [Create an SLB instance](/intl.en-US/User Guide/Instance/Create an SLB instance.md). In this example, apply for a public SLB instance and take note of the ID of the SLB instance.

    ![Apply for an SLB instance](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9297297951/p10321.png)

3.  Configure a TLS certificate.

    You must configure a TLS certificate to achieve HTTPS-based access.

    1.  Run the following commands to generate a TLS certificate:

        ```
        $ openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=bar.foo.com/O=bar.foo.com"# Create a secret for the TLS certificate.
        ```

        ```
        Generating a 2048 bit RSA private key
        ......................................................... +++
        ..................................................+++
        writing new private key to 'tls.key'
        -----
        ```

        ```
        kubectl create secret tls cert-example --key tls.key --cert tls.crt
        ```

        ```
        secret "cert-example" created
        ```

    2.  Run the following command to view the created TLS certificate:

        ```
        kubectl get secret cert-example
        ```

        ```
        NAME           TYPE                DATA      AGE
        cert-example   kubernetes.io/tls   2         12s
        ```

    **Note:** ACK automatically initializes the default HTTPS certificate for the SLB instance based on the TLS certificate of the Ingress that is created first. You can modify the default HTTPS certificate in the SLB console. To configure multiple certificates, you can add extended domain names for the HTTPS listener and associate them with certificates in the SLB console.

4.  Configure an Ingress.

    1.  Create the `tomcat-ingress.yml` file, copy the following code to the file, and then run the `kubectl apply -f tomcat-ingress.yml` command to configure an Ingress:

        ```
        apiVersion: extensions/v1beta1
        kind: Ingress
        metadata:
          name: tomcat-ingress
          annotations:
            # Specify the ID of the existing SLB instance.
            service.beta.kubernetes.io/alicloud-loadbalancer-id: lb-xxxxxxxxxx            ##Replace lb-xxxxxxxxxx with the ID of your SLB instance.
            service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners: "true"
        spec:
          tls:
          - hosts:
            - bar.foo.com
            # Configure a TLS certificate.
            secretName: cert-example
          rules:
          # Configure a Layer 7 domain name.
          - host: bar.foo.com
            http:
              paths:
              # Configure a context path.
              - path: /
                backend:
                  serviceName: tomcat
                  servicePort: 8080
        ```

        If the following result appears, the Ingress is configured:

        ```
        ingress "tomcat-ingress" created
        ```

    2.  Run the `kubectl get ing tomcat-ingress` command to obtain the IP address of the SLB instance.

        ```
        NAME             HOSTS         ADDRESS        PORTS     AGE
        tomcat-ingress   bar.foo.com   47. ***. **.**   80, 443   1m
        ```

5.  Access the test service.

    **Note:** You must resolve the domain name to the IP address of the SLB instance.

    In this example, add the following DNS rule to the hosts file to access the test service. We recommend that you apply for an ICP filing for the domain name in your production environment.

    ```
    47.***. **.**   bar.foo.com
    ```

    -   Access the Tomcat service by using a browser.

        ![Access the Tomcat service](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9297297951/p10322.png)

    -   Access the Tomcat service by using the command line tool.

        ```
        curl -k -H "Host: bar.foo.com" https://47. ***. **. **
        ```


