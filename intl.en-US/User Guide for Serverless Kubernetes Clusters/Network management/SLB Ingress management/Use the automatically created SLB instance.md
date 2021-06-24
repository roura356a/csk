---
keyword: Internet-facing SLB instance
---

# Use the automatically created SLB instance

If you do not use an annotation to specify an existing Server Load Balancer \(SLB\) instance when you create an Ingress, the system automatically creates an Internet-facing SLB instance. This topic describes how to use the automatically created SLB instance with an Ingress to set up forwarding.

-   A serverless Kubernetes \(ASK\) cluster is created. You must configure a network address translation \(NAT\) gateway for the virtual private cloud \(VPC\) where the cluster is created so that the cluster can download container images from the Internet. For more information, see [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).
-   The kubectl client is connected to the ASK cluster. For more information, see [Use kubectl to connect to an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Cluster/Manage and access clusters/Use kubectl to connect to an ASK cluster.md).

## Procedure

**Step 1: Deploy applications**

1.  Create the cafe-service.yaml file, copy the following code to the file, and run the `kubectl apply -f cafe-service.yaml` command to deploy a **coffee** application and a **tea** application:

    ```
    apiVersion: apps/v1 
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

    The following output is returned:

    ```
    deployment "coffee" created
    service "coffee-svc" created
    deployment "tea" created
    service "tea-svc" created
    ```

2.  Run the following command to view the application status:

    ```
    kubectl get svc,deploy
    ```

    The following output is returned:

    ```
    NAME             TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
    svc/coffee-svc   ClusterIP   <none>       <none>        80/TCP    1m
    svc/tea-svc      ClusterIP   <none>       <none>        80/TCP    1m
    
    NAME            DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
    deploy/coffee   2         2         2            2           1m
    deploy/tea      1         1         1            1           1m
    ```


**Step 2: Configure an Ingress**

1.  Create the cafe-ingress.yaml file, copy the following code to the file, and run the `kubectl apply -f cafe-ingress.yaml` command to configure an Ingress. The Ingress specifies the domain name and path that are used to expose the **coffee** and **tea** applications to the Internet.

    ```
    apiVersion: networking.k8s.io/v1
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

    The following output is returned:

    ```
    ingress "cafe-ingress" created
    ```

2.  Run the following command to obtain the IP address of the SLB instance:

    ```
    kubectl get ing
    ```

    The following output is returned:

    ```
    NAME           HOSTS         ADDRESS          PORTS     AGE
    cafe-ingress   foo.bar.com   139.168.XX.XX    80        1m
    ```


**Step 3: Access the applications**

**Note:** You must resolve the domain name to the IP address of the SLB instance.

In this example, the following DNS rule is added for the domain name to enable access to the applications. We recommend that you apply for an Internet Content Provider \(ICP\) number for the domain name if the domain name is used in the production environment.

```
139.168.XX.XX    foo.bar.com
```

Access the **coffee** application.

-   Access the **coffee** application by using a browser.

    ![S6](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6735028161/p245248.png)

-   Access the **coffee** application by using the command line tool.

    ```
    curl -H "Host: foo.bar.com" http://139.168.XX.XX/coffee
    ```


Access the **tea** application.

-   Access the **tea** application by using a browser.

    ![S7](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6735028161/p245250.png)

-   Access the **tea** application by using the command line tool.

    ```
    curl -H "Host: foo.bar.com" http://139.168.XX.XX/tea
    ```


