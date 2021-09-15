---
keyword: [ALB Ingress, access a Service]
---

# Access Services by using an ALB Ingress

Application Load Balancer \(ALB\) Ingresses are compatible with NGINX Ingresses, and provide improved traffic routing capabilities based on ALB instances. ALB Ingresses support complex routing, automatic certificate discovery, and the HTTP, HTTPS, and Quick UDP Internet Connection \(QUIC\) protocols. ALB Ingresses meet the requirements of cloud-native applications for ultra-high elasticity and balancing of heavy traffic loads at Layer 7. This topic describes how to use an ALB Ingress to access Services.

-   A serverless Kubernetes \(ASK\) cluster is created. You must configure a network address translation \(NAT\) gateway for the virtual private cloud \(VPC\) where the cluster is created so that the cluster can download container images from the Internet. For more information, see [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Cluster/Create an ASK cluster.md).
-   The kubectl client is connected to the ASK cluster. For more information, see [Use kubectl to connect to an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Cluster/Manage and access clusters/Use kubectl to connect to an ASK cluster.md).

An Ingress provides a collection of rules that manage external access to Services in a cluster. You can configure forwarding rules to assign Services different externally-accessible URLs. However, NGINX Ingresses and Layer 4 Server Load Balancer \(SLB\) Ingresses cannot meet the requirements of cloud-native applications, such as complex routing, multiple application layer protocols support \(such as QUIC\), and balancing of heavy traffic loads at Layer 7.

## Step 1: Deploy Services

1.  Create a cafe-service.yaml file and copy the following content to the file. The file is used to deploy two Deployments named `coffee` and `tea` and two Services named `coffee` and `tea`.

    ```
    apiVersion: apps/v1 
    kind: Deployment
    metadata:
      name: coffee
    spec:
      replicas: 1
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
    apiVersion: apps/v1 
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

2.  Run the following command to deploy the Deployments and Services:

    ```
    kubectl apply -f cafe-service.yaml
    ```

    Expected output:

    ```
    deployment "coffee" created
    service "coffee-svc" created
    deployment "tea" created
    service "tea-svc" created
    ```

3.  Run the following command to query the status of the Services:

    ```
    kubectl get svc,deploy
    ```

    Expected output:

    ```
    NAME             TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
    svc/coffee-svc   ClusterIP   <none>       <none>        80/TCP    1m
    svc/tea-svc      ClusterIP   <none>       <none>        80/TCP    1m
    NAME            DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
    deploy/coffee   2         2         2            2           1m
    deploy/tea      1         1         1            1           1m
    ```


## Step 2: Configure an Ingress

1.  Create a cafe-ingress.yaml and copy the following content to the file:

    ```
    apiVersion: networking.k8s.io/v1beta1
    kind: Ingress
    metadata:
      name: cafe-ingress
      annotations:
        kubernetes.io/ingress.class: alb
        alb.ingress.kubernetes.io/name: ingres_test_base
        alb.ingress.kubernetes.io/address-type: internet
        alb.ingress.kubernetes.io/vswitch-ids: "vsw-k1akdsmts6njkvhas****,vsw-k1amdv9ax94gr5iwa****"
        alb.ingress.kubernetes.io/healthcheck-enabled: "true"
        alb.ingress.kubernetes.io/healthcheck-path: "/"
        alb.ingress.kubernetes.io/healthcheck-protocol: "HTTP"
        alb.ingress.kubernetes.io/healthcheck-method: "HEAD"
        alb.ingress.kubernetes.io/healthcheck-httpcode: "http_2xx"
        alb.ingress.kubernetes.io/healthcheck-timeout-seconds: "5"
        alb.ingress.kubernetes.io/healthcheck-interval-seconds: "2"
        alb.ingress.kubernetes.io/healthy-threshold-count: "3"
        alb.ingress.kubernetes.io/unhealthy-threshold-count: "3"
    spec:
      rules:
      - http:
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

    The following table describes the parameters in the YAML template.

    |Parameter|Description|
    |---------|-----------|
    |alb.ingress.kubernetes.io/name|The name of the ALB instance that you want to use.|
    |alb.ingress.kubernetes.io/address-type|The type of IP address that the ALB instance uses to provide services. Valid values:    -   Internet: The ALB instance uses a public IP address. The domain name of the Ingress is resolved to the public IP address of the ALB instance. Therefore, the ALB instance is accessible over the Internet. This is the default value.
    -   Intranet: The ALB instance uses a private IP address. The domain name of the Ingress is resolved to the private IP address of the ALB instance. Therefore, the ALB instance is accessible only within the virtual private cloud \(VPC\) where the ALB instance is deployed. |
    |alb.ingress.kubernetes.io/vswitch-ids|The IDs of the vSwitches that are used by the ALB Ingress. You must specify at least two vSwitch IDs and the vSwitches must be deployed in different zones. For more information about the regions and zones that are supported by ALB Ingresses, see [Supported regions and zones](/intl.en-US/Application Load Balancer/ALB Product Introduction/Supported regions and zones.md).|
    |alb.ingress.kubernetes.io/healthcheck-enabled|Specifies whether to enable health checks. Default value: true.|
    |alb.ingress.kubernetes.io/healthcheck-path|The path through which health checks are performed. Default value: /.     -   Enter the URL of the web page on which you want to perform health checks. We recommend that you enter the URL of a static web page. The URL must be 1 to 80 characters in length, and can contain letters, digits, hyphens \(-\), forward slashes \(/\), periods \(.\), percent signs \(%\), question marks \(?\), number signs \(\#\), and ampersands \(&\). The URL can also contain the following extended characters: \_ ; ~ ! \( \) \* \[ \] @ $ ^ : ' , +. The URL must start with a forward slash \(/\).
    -   By default, to perform health checks, the ALB instance sends HTTP HEAD requests to the default application homepage configured on the backend Elastic Compute Service \(ECS\) instance. The ALB instance sends the requests to the private IP address of the ECS instance. If you do not want to use the default application homepage for health checks, you must specify a URL. |
    |alb.ingress.kubernetes.io/healthcheck-protocol|The protocol that is used to perform health checks.     -   HTTP: The ALB instance sends HEAD or GET requests to a backend server to simulate access from a browser and check whether the backend server is healthy. This is the default protocol.
    -   TCP: The ALB instance sends TCP SYN packets to a backend server to check whether the port of the backend server is available to receive requests.
    -   GRPC: The ALB instance sends POST or GET requests to a backend server to check whether the backend server is healthy. |
    |alb.ingress.kubernetes.io/healthcheck-method|Specifies a health check method.     -   HEAD: By default, HTTP health checks send HEAD requests to a backend server. This is the default method. Make sure that your backend server supports HEAD requests. If your backend server does not support HEAD requests or HEAD requests are disabled, health checks may fail. In this case, you can use GET requests to perform health checks.
    -   POST: By default, gRPC health checks use the POST method. Make sure that your backend servers support POST requests. If your backend server does not support POST requests or POST requests are disabled, health checks may fail. In this case, you can use GET requests to perform health checks.
    -   GET: If the length of a response packet exceeds 8 KB, the response is fragmented into smaller packets. However, the health check result is not affected. |
    |alb.ingress.kubernetes.io/healthcheck-httpcode|Specify the status codes that are returned when health check results are normal.     -   When the health check protocol is set to HTTP, valid values are http\_2xx, http\_3xx, http\_4xx, and http\_5xx. The default value for HTTP health checks is http\_2xx.
    -   When the health check protocol is set to GRPC, valid values are 0 to 99. Value ranges are supported. You can enter at most 20 value ranges and must separate them with commas \(,\). |
    |alb.ingress.kubernetes.io/healthcheck-timeout-seconds|Specifies the timeout period of a health check. If a backend server does not respond within the specified timeout period, the server fails the health check. Valid values: 1 to 300. Default value: 5. Unit: seconds.|
    |alb.ingress.kubernetes.io/healthcheck-interval-seconds|The interval at which health checks are performed. Unit: seconds. Valid values: 1 to 50. Default value: 2. Unit: seconds.|
    |alb.ingress.kubernetes.io/healthy-threshold-count|Specifies the number of times that an unhealthy backend server must consecutively pass health checks before the server is considered healthy. Valid values: 2 to 10. Default value: 3.|
    |alb.ingress.kubernetes.io/unhealthy-threshold-count|Specifies the number of times that a healthy backend server must consecutively fail health checks before the server is considered unhealthy. Valid values: 2 to 10. Default value: 3.|

2.  Run the following command to configure an externally-accessible domain name and a `path` for the `coffee` and `tea` Services separately:

    ```
    kubectl apply -f cafe-ingress.yaml
    ```

    Expected output:

    ```
    ingress "cafe-ingress" created
    ```

3.  Run the following command to query the IP address of the ALB instance:

    ```
    kubectl get ingress
    ```

    Expected output:

    ```
    NAME           CLASS    HOSTS   ADDRESS                                               PORTS   AGE
    cafe-ingress   <none>   *       alb-m551oo2zn63yov****.cn-hangzhou.alb.aliyuncs.com   80      50s
    ```


## Step 3: Access the Services

-   After you obtain the IP address of the ALB instance, use one of the following methods to access the `coffee` Service:
    -   Access the `coffee` Service by using a browser.

        ![1](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9747748261/p297352.png)

    -   Access the `coffee` Service by using a CLI.

        ```
        curl http://alb-m551oo2zn63yov****.cn-hangzhou.alb.aliyuncs.com/coffee
        ```

-   After you obtain the IP address of the ALB instance, use one of the following methods to access the `tea` Service:
    -   Access the `tea` Service by using a browser.

        ![SLB2](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/0847748261/p297351.png)

    -   Access the `tea` Service by using a CLI.

        ```
        curl http://alb-m551oo2zn63yov****.cn-hangzhou.alb.aliyuncs.com/tea
        ```


