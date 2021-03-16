---
keyword: [create Ingress, update Ingress, delete Ingress]
---

# Basic operations of an Ingress

An Ingress is a Kubernetes resource object that is used to enable external access to Services in a Kubernetes cluster. You can use Ingresses to configure multiple forwarding rules for handling requests to pods in a Kubernetes cluster. This topic describes how to create, view, update, and delete an Ingress in the Container Service for Kubernetes \(ACK\) console or by using kubectl.

[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)

## How to perform basic operations on an Ingress in the ACK console

**Create an Ingress**

1.  Log on to the [ACK console](https://cs.console.aliyun.com)[ACK console](https://partners-intl.console.aliyun.com/#/cs).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Services and Ingresses** \> **Ingresses**.

5.  On the Ingresses page, click **Create**. In the **Create** dialog box, set the name of the Ingress. In this example, the Ingress is named nginx-ingress.

6.  Configure Ingress rules.

    Ingress rules are used to manage external access to Services in the cluster. Ingress rules can be HTTP or HTTPS rules. You can configure the following items in Ingress rules: domain name \(virtual hostname\), URL path, Service name, port, and weight.

    In this example, a complex rule is added to configure Services for the default domain name and virtual hostname of the cluster. Traffic routing is based on domain names.

    ![Ingress rules](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5445359951/p10776.png)

    -   Create a simple Ingress that uses the default domain name for external access.
        -   **Domain**: Enter the default domain name of the cluster. In this example, the default domain name is `test.[cluster-id].[region-id].alicontainer.com`.

            In the Create dialog box, the default domain name of the cluster is displayed in the format of `*.[cluster-id].[region-id].alicontainer.com`. You can also obtain the default domain name from the details page of the cluster.

        -   **Services**: Set the names of the backend Services, and the path and port numbers that are used to access these Services.
            -   Path: You can enter the URL for accessing the backend Services. The default path is the root path /. In this example, the default path is used. Each path is associated with a backend Service. Server Load Balancer \(SLB\) forwards traffic to a backend Service only when inbound requests match the domain name and path.
            -   Services: Specify the names of the backend Services and the path and port numbers that are used to access these Services. You can also set weights for these Services. You can configure multiple Services in the same path. The Ingress traffic is split and forwarded to the matched Services.
    -   Create a simple fanout Ingress that uses multiple domain names. In this example, a virtual hostname is used as the test domain name for external access. Route weights are specified for two backend Services, and canary release settings are configured for one of the Services. In your production environment, you can use a domain name that has obtained an Internet Content Provider \(ICP\) number for external access.
        -   **Domain**: Enter the test domain name. In this example, the test domain name is `foo.bar.com`.

            You must add the following domain name mapping to the hosts file:

            ```
            118.178.XX.XX foo.bar.com       #The IP address of the Ingress.
            ```

        -   **Services**: Specify the names of the backend Services, and the path and port numbers that are used to access these Services. You can also set weights for these Services.
            -   Path: Enter the URL path of the backend Service. In this example, the default root path / is used.
            -   Name: In this example, select the nginx-new and nginx-old Services.
            -   Port: In this example, port 80 is opened.
            -   Weight: Set a weight for each backend Service. The weight is a percentage value. The default value is 100. In this example, the weight for each backend Service is 50. This means that the two backend Services have the same weight.
7.  Configure Transport Layer Security \(TLS\).

    Select **EnableTLS** to enable TLS and configure a secure Ingress. For more information, see [Configure an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Configure an Ingress.md).

    -   You can select an existing Secret.
        1.  Log on to a master node. Create a file named tls.key and another file named tls.crt.

            ```
            openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=foo.bar.com/O=foo.bar.com"
            ```

        2.  Create a Secret.

            ```
            kubectl create secret tls foo.bar --key tls.key --cert tls.crt
            ```

        3.  Run the `kubectl get secret` command to check whether the Secret is created. Then, you can select the newly created foo.bar Secret.

            ![Configure TLS](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6445359951/p10779.png)

    -   You can also use the TLS private key and certificate to create a Secret.
        1.  Log on to a master node. Create a file named tls.key and another file named tls.crt.

            ```
            openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=foo.bar.com/O=foo.bar.com"
            ```

        2.  Run the `vim tls.key` and `vim tls.crt` commands to obtain the private key and certificate that are generated.
        3.  Copy the certificate to the Cert field and the private key to the Key field.

            ![Create a Secret](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6445359951/p10781.png)

8.  Configure canary release settings.

    ACK supports multiple traffic splitting methods. This allows you to select suitable solutions for specific scenarios, such as canary releases and A/B testing.

    1.  Traffic splitting based on request headers.
    2.  Traffic splitting based on cookies.
    3.  Traffic splitting based on query parameters.
    After canary release settings are configured, only requests that match the specified rules are routed to the new-nginx Service. If the weight of new-nginx is lower than 100%, requests that match the specified rules are routed to the Service based on the Service weight.

    In this example, the rule is added to specify a request header that matches the regular expression `foo=^bar$`. Only requests with headers that match the regular expression can access new-nginx.

    ![Access new-nginx](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6445359951/p10777.png)

    -   **Services**: Select Services as the backend for the Ingress rule.
    -   **Type**: Select the type of the matching rule. Valid values: Header, Cookie, and Query.
    -   **Name and Match Value**: Specify custom request fields. The name and matching value comprise a key-value pair.
    -   **Matching Rule**: Regular expressions and exact matches are supported.
    **Note:** You can configure canary release settings for up to two Services.

9.  Configure annotations.

    Click **Add** on the right side of Annotations. In the **Type** drop-down list, you can select the type of annotation based on the following description:

    -   **Custom Annotation**: Enter names and values as key-value pairs for the annotation. For more information, see [Annotations](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/).
    -   **Ingress-NGINX**: Select annotations by names.

        You can add an annotation to redirect inbound traffic. For example, `nginx.ingress.kubernetes.io/rewrite-target: /` specifies that /path is redirected to the root path /. The root path can be recognized by the backend Services.

        **Note:** In this example, no path is configured for the backend Services. Therefore, you do not need to configure rewrite annotations. Rewrite annotations allow the Ingress to forward traffic through root paths to the backend Services. This avoids 404 errors that are caused by invalid paths.

10. Add labels.

    Add labels to describe the Ingress.

    ![Add labels](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6445359951/p10783.png)

11. Click **Create**. You are redirected to the Ingresses page.

    After the Ingress is created, you can find the nginx-ingress Ingress on the Ingresses page.


**View an Ingress**

1.  Log on to the [ACK console](https://cs.console.aliyun.com)[ACK console](https://partners-intl.console.aliyun.com/#/cs).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Services and Ingresses** \> **Ingresses**.

5.  On the Ingresses page, find the Ingress that you want to view and click **Details** in the **Actions** column.

    On the details page, you can view detailed information about the Ingress.


**Update an Ingress**

1.  Log on to the [ACK console](https://cs.console.aliyun.com)[ACK console](https://partners-intl.console.aliyun.com/#/cs).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Services and Ingresses** \> **Ingresses**.

5.  On the Ingresses page, find the Ingress that you want to update and click **Details** in the **Actions** column.

6.  In the **Update** dialog box, modify the parameters based on your requirements and click **Update**.


**Delete an Ingress**

1.  Log on to the [ACK console](https://cs.console.aliyun.com)[ACK console](https://partners-intl.console.aliyun.com/#/cs).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Services and Ingresses** \> **Ingresses**.

5.  On the Ingresses page, find the Ingress that you want to delete and choose **More** \> **Delete** in the **Actions** column.

6.  In the **Note** dialog box, click **Confirm**.


## How to perform basic operations on an Ingress by using kubectl

**Create an Ingress**

1.  Create a Deployment and a Service.

    You must create a Service for external access before you can create an Ingress.

    1.  Create a file named test-deployment-service.yaml and copy the following content into the file.

        The following YAML template can be used to create a Deployment named test-web1 and a Service named web1-service.

        ```
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: test-web1
          labels:
            app: test-web1
        spec:
          replicas: 1
          selector:
            matchLabels:
              app: test-web1
          template:
            metadata:
              labels:
                app: test-web1
            spec:
              containers:
              - name: test-web1
                imagePullPolicy: IfNotPresent
                image: registry.cn-hangzhou.aliyuncs.com/yilong/ingress-test:web1
                ports:
                - containerPort: 8080
        --- 
        apiVersion: v1
        kind: Service
        metadata:
          name: web1-service
        spec:
          type: ClusterIP
          selector:
            app: test-web1
          ports:
            - port: 8080
              targetPort: 8080
        ```

    2.  Run the following command to create the Deployment and Service:

        ```
        kubectl apply -f test-deployment-service.yaml
        ```

2.  Create an Ingress.

    1.  Create a file named test-ingress.yaml and copy the following content into the file.

        ```
        apiVersion: networking.k8s.io/v1beta1
        kind: Ingress
        metadata:
          name: test-ingress
          namespace: default
        spec:
          rules:
          - host: test-ingress.com
            http:
              paths:
              - path: /foo
                backend:
                  serviceName: web1-service
                  servicePort: 8080
              - path: /bar
                backend:
                  serviceName: web1-service
                  servicePort: 8080
        ```

        -   `name`: the name of the Ingress. In this example, the name is set to test-ingress.
        -   `host`: the domain name for external access to the backend Service.
        -   `path`: the URL path for external access. Server Load Balancer \(SLB\) forwards traffic to the `backend` Service only when inbound requests match the `host` and `path` settings.
        -   `backend`: you need to set a Service and Service port.
            -   Service name: the name of the `backend` Service of the Ingress.
            -   Service port: the open port of the Service.
    2.  Run the following command to create the Ingress:

        ```
        kubectl apply -f test-ingress.yaml
        ```


**View Ingresses**

Run the following command to view Ingresses:

```
kubectl get ingress
```

**Update an Ingress**

Run the following command to update an Ingress:

```
kubectl edit ingress <Ingress name>
```

**Delete an Ingress**

Run the following command to delete an Ingress:

```
kubectl delete ingress <Ingress name>
```

