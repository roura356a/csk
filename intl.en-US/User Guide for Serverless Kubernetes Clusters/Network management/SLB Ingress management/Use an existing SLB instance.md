---
keyword: existing SLB instance
---

# Use an existing SLB instance

This topic describes how to use an existing Server Load Balancer \(SLB\) instance with an Ingress to set up forwarding. You can use an annotation to specify an SLB instance by ID when you create an Ingress.

-   A guaranteed-performance SLB instance is created in the virtual private cloud \(VPC\) where the ASK cluster is deployed. Guaranteed-performance SLB instances support elastic network interfaces \(ENIs\).
    -   If you have an SLB instance in the VPC where the ASK cluster is deployed, you can log on to the [SLB console](https://slb.console.aliyun.com/slb/cn-hangzhou/slbs) and obtain the ID of the SLB instance on the **Instances** page.
    -   If you have no SLB instance, you must create a guaranteed-performance SLB instance in the VPC where the ASK cluster is deployed. For example, you can select the slb.s2.small instance type. The SLB instance can be internal-facing or Internet-facing. For more information, see [Create a CLB instance](/intl.en-US/Classic Load Balancer/User Guide/Instance/Create a CLB instance.md).
    -   In this example, an Internet-facing SLB instance is used.

## Procedure

**Note:** The Ingress controller automatically opens port 80 and 443 on the SLB instance. Make sure that port 80 and 443 are not used by other services.

**Step 1: Deploy an application**

1.  Create the tomcat-service.yml file, copy the following code to the file, and run the `kubectl apply -f tomcat-service.yml` command to deploy a **tomcat** application for testing:

    ```
    apiVersion: apps/v1 
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

    The following output is returned:

    ```
    deployment "tomcat" created
    service "tomcat" created
    ```

2.  Run the following command to view the application status:

    ```
    kubectl get svc,deploy tomcat
    ```

    The following output is returned:

    ```
    NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
    svc/tomcat   ClusterIP   <none>       <none>        8080/TCP   1m
    
    NAME            DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
    deploy/tomcat   1         1         1            1           1m
    ```


**Step 2: Configure an Ingress**

1.  Create the tomcat-ingress.yml file, copy the following code to the file, and run the `kubectl apply -f tomcat-ingress.yml` command to configure an Ingress:

    ```
    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
      name: tomcat-ingress
      annotations:
        # Specify the ID of the existing SLB instance.
        service.beta.kubernetes.io/alicloud-loadbalancer-id: lb-xxxxxxxxxx            ##Replace lb-xxxxxxxxxx with the ID of your SLB instance.
        service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners: "true"
    spec:
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

    The following output is returned:

    ```
    ingress "tomcat-ingress" created
    ```

2.  Run the following command to obtain the IP address of the SLB instance:

    ```
    kubectl get ing tomcat-ingress
    ```

    The following output is returned:

    ```
    NAME             HOSTS         ADDRESS        PORTS     AGE
    tomcat-ingress   bar.foo.com   47.168.XX.XX   80, 443   1m
    ```


**Step 3: Access the application**

**Note:** You must resolve the domain name to the IP address of the SLB instance.

In this example, the following DNS rule is created for the domain name to enable access to the test application. We recommend that you apply for an Internet Content Provider \(ICP\) number for the domain name if the domain name is used in the production environment.

```
47.168.XX.XX    bar.foo.com
```

-   Access the **tomcat** application by using a browser.

    ![S10](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2835028161/p245328.png)

-   Access the **tomcat** application by using the command line tool.

    ```
    curl -k -H "Host: bar.foo.com" https://47.168.XX.XX
    ```


