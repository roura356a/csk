---
keyword: [ALB Ingress, access a Service]
---

# Access Services by using an ALB Ingress

The Application Load Balancer \(ALB\) Ingress is fully compatible with the NGINX Ingress, and uses ALB instances to control and distribute traffic in a more efficient manner. The ALB Ingress supports complex routing, automatic certificate discovery, and the HTTP, HTTPS, and QUIC protocols. The ALB Ingress can meet the requirements of cloud-native applications for ultra-high elasticity and balancing of heavy traffic loads at Layer 7. This topic describes how to use an ALB Ingress to access Services.


An Ingress provides a collection of rules that manage external access to Services in a cluster. You can configure forwarding rules to assign Services different externally-reachable URLs. However, the NGINX Ingress and Layer 4 Server Load Balancer \(SLB\) Ingress cannot meet the requirements of cloud-native applications, such as complex routing, multiple application layer protocols support \(such as QUIC\), and balancing of heavy traffic loads at Layer 7.

## Step 1: Deploy Services

1.  Create a file named cafe-service.yaml and copy the following content to the file. The file is used to deploy two Deployments named `coffee` and `tea` and two Services named `coffee` and `tea`.

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

1.  Create a file named cafe-ingress.yaml and copy the following content to the file:

    ```
    apiVersion: networking.k8s.io/v1beta1
    kind: Ingress
    metadata:
      name: cafe-ingress
      annotations:
        kubernetes.io/ingress.class: alb
        alb.ingress.kubernetes.io/name: ingres_test_base
        alb.ingress.kubernetes.io/address-type: internet
        alb.ingress.kubernetes.io/address-allocated-mode: Fixed
        alb.ingress.kubernetes.io/edition: Standard
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
    |alb.ingress.kubernetes.io/name|The name of the ALB instance. This parameter is optional.|
    |alb.ingress.kubernetes.io/address-type|The IP address type of the ALB instance. This parameter is optional. Valid values:    -   Internet: The ALB instance uses a public IP address. The domain name of a Service is resolved to the public IP address of the ALB instance. Therefore, the Service is accessible over the Internet. This is the default value.
    -   Intranet: The ALB instance uses a private IP address. The domain name of a Service is resolved to the private IP address of the ALB instance. Therefore, the Service is accessible only within the virtual private cloud \(VPC\) where the ALB instance is deployed. |
    |alb.ingress.kubernetes.io/address-allocated-mode|The IP address allocation mode of the ALB instance. This parameter is optional. Valid values:    -   Fixed: The ALB instance uses a static IP address.
    -   Dynamic: An IP address is dynamically assigned to each zone of the ALB instance. This is the default value. |
    |alb.ingress.kubernetes.io/edition|The edition of the ALB instance. Different editions have different limits and billing rules. This parameter is optional. Valid values:    -   Basic: the Basic edition. This is the default value.
    -   Standard: the Standard edition. |

2.  Run the following command to configure an externally-accessible URL and `path` for the `coffee` and `tea` Services separately:

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

    -   Access the `coffee` Service by using the CLI.

        ```
        curl http://alb-m551oo2zn63yov****.cn-hangzhou.alb.aliyuncs.com/coffee
        ```

-   After you obtain the IP address of the ALB instance, use one of the following methods to access the `tea` Service:
    -   Access the `tea` Service by using a browser.

        ![SLB2](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/0847748261/p297351.png)

    -   Access the `tea` Service by using the CLI.

        ```
        curl http://alb-m551oo2zn63yov****.cn-hangzhou.alb.aliyuncs.com/tea
        ```


