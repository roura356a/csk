---
keyword: [create an Ingress, Ingress, ]
---

# Create an Ingress in the ACK console

The Container Service for Kubernetes \(ACK\) console is integrated with the Ingress service. You can create an Ingress in the ACK console and manage inbound traffic that is forwarded to different services to meet your business requirements. This topic describes how to create an Ingress in the ACK console.

-   An ACK cluster is created and an Ingress controller runs as expected in the ACK cluster. For more information, see [Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md).
-   You can use SSH to connect to a master node of the ACK cluster. For more information, see [Use SSH to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use SSH to connect to an ACK cluster.md).

## Step 1: Create a deployment and a service

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Workload**.

5.  On the **Deployments** page, click **Create from Template**.

6.  Set Sample Template to Custom or select a sample template from the Sample Template drop-down list, and click **Create**.

    In this example, three NGINX applications are created, including old-nginx, new-nginx, and domain-nginx. The domain-nginx application is used to test the domain name of the cluster.

7.  In the left-side navigation pane, click **Services** to go to the Services page.

    After the services are created, you can find these services on the Services page.

    ![Services](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5445359951/p10773.png)


## Step 2: Create an Ingress

1.  In the left-side navigation pane, click Ingresses.

2.  On the Ingresses page, Click **Create**.

3.  In the dialog box that appears, enter the Ingress name. In this example, nginx-ingress is used.

4.  Configure Ingress rules.

    Ingress rules allow you to authorize inbound access to the ACK cluster. HTTP and HTTPS rules are supported. The configurations of an Ingress rule include the domain name \(or virtual hostname\), path, service name, port, and weight. For more information, see [Ingress configurations](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Ingress configurations.md).

    In this example, a rule is added to specify two services for the default domain name and virtual hostname of the ACK cluster. Traffic routing is based on domain names.

    ![Ingress rule](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5445359951/p10776.png)

    -   Basic Ingress rule based on the default domain name: uses the default domain name of the cluster to provide external access.
        -   **Domain**: Enter the default domain name of the ACK cluster. In this example, the default domain name is `test.[cluster-id].[region-id].alicontainer.com`.

            The default domain name of the ACK cluster appears. This domain name is in the `*.[cluster-id].[region-id].alicontainer.com` format. You can also obtain the default domain name from the basic information page of the ACK cluster.

        -   **Services**: Configure the path, name, port, and weight of the domain-nginx service.
            -   Path: Enter the URL that is used to access the service. The default path is the root path /. In this example, the default path is used. Each path is associated with a backend service. Server Load Balancer \(SLB\) forwards traffic to a backend service only when inbound requests match the domain name and path.
            -   Service configuration: Configure the service name, port, and weight. You can configure multiple services in the same path. The Ingress traffic is split and forwarded to the matched services.
    -   Basic fanout Ingress rule based on the test domain name: uses a virtual hostname as the test domain name of the cluster to provide external access. Configure weights for the old-nginx and new-nginx services and configure canary release for the new-nginx service. In your production environment, you can use a domain name that has obtained an Internet Content Provider \(ICP\) number to provide services.
        -   **Domain**: Enter the test domain name. In this example, `foo.bar.com` is used.

            You must modify the hosts file to add a domain name mapping rule.

            ```
            118.178.XX.XX foo.bar.com       #The IP address is that of the Ingress.
            ```

        -   **Services**: Set the service path, name, port, and weight.
            -   Path: Enter the URL that is used to access the services. In this example, the default root path / is used.
            -   Name: In this example, specify both services, old-nginx and new-nginx.
            -   Port: In this example, port 80 is opened.
            -   Weight: Set the weight for each service. The weight is a percentage value. Default value: 100. In this example, the same weight 50 is set for each service.
5.  Configure a canary release.

    ACK supports multiple traffic splitting methods. This allows you to select more suitable solutions for specific scenarios, such as canary releases and A/B testing. These scenarios include:

    1.  Traffic splitting based on request headers
    2.  Traffic splitting based on cookies
    3.  Traffic splitting based on query parameters
    After a canary release is configured, requests that match only the specified rules are routed to the new-nginx service. If the weight of new-nginx is lower than 100%, requests that match the specified rules are routed to this service based on the weight.

    In this example, the rule is added to specify a request header that matches the regular expression `foo=^bar$`. Only requests that contain this header can access new-nginx.

    ![Access the NGINX service](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6445359951/p10777.png)

    -   **Services**: the service to be accessed.
    -   **Type**: the type of the matching rule, such as Header, Cookie, or Query.
    -   **Name and Match Value**: custom request fields. The name and matching value comprise a key-value pair.
    -   **Matching Rule**: Regular expressions and exact matches are supported.
    **Note:** The rules of a canary release support only two services.

6.  Configure annotations.

    Click **Rewrite Annotation** to add a rewrite annotation for the Ingress. For example, `nginx.ingress.kubernetes.io/rewrite-target: /` specifies that /path is redirected to the root path /. The root path can be recognized by the backend service.

    **Note:** In this example, no path is configured for the service. You do not need to configure rewrite annotations. Rewrite annotations allow the Ingress to forward traffic through root paths to the backend service. This avoids the error 404 that is caused by invalid paths.

    You can also click **Add** and enter a name and a value to create a key-value pair for the annotation. For more information, see [Annotations](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/).

    ![Add annotations](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6445359951/p10778.png)

7.  Configure Transport Layer Security \(TLS\).

    Select **EnableTLS** to enable TLS and configure secure routing. For more information, see [Configure an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Configure an Ingress.md).

    -   You can select Existing Secret.
        1.  Log on to a master node and create the tls.key and tls.crt files.

            ```
            openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=foo.bar.com/O=foo.bar.com"
            ```

        2.  Create a Secret.

            ```
            kubectl create secret tls foo.bar --key tls.key --cert tls.crt
            ```

        3.  In the command-line interface \(CLI\) of kubectl, enter `kubectl get secret` and verify that the Secret has been created. You can then select the foo.bar Secret in the ACK console.

            ![Configure TLS](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6445359951/p10779.png)

    -   You can also use the TLS private key and certificate to create a Secret.
        1.  Log on to a master node and create the tls.key and tls.crt files.

            ```
            openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=foo.bar.com/O=foo.bar.com"
            ```

        2.  In the CLI of kubectl, enter `vim tls.key` and `vim tls.crt` to obtain the private key and certificate that are generated.
        3.  Copy the certificate to the Cert field and copy the private key to the Key field.

            ![Create a Secret](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6445359951/p10781.png)

8.  Add labels.

    Add labels to describe the features of the Ingress.

    ![Add labels](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6445359951/p10783.png)

9.  Click **Create**.

    You can find the nginx-ingress Ingress on the Ingresses page.

    ![Ingresses](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6445359951/p10784.png)


## Check the effect of the Ingress

1.  On the Ingresses page, click `test.[cluster-id].[region-id].alicontainer.com and foo.bar.com` and `foo.bar.com` in the Ingress that is used to access the services. When you click the domain names, the NGINX welcome page appears.

    ![Domain names of the services](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6445359951/p10786.png)

    When you click the domain name that points to new-nginx, the old-nginx service page appears.

    **Note:** By default, when you enter the route address in the browser, requests with headers that do not contain `foo=^bar$` are directed to old-nginx.

    ![old-nginx](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6445359951/p10791.png)

2.  Log on to the master node by using SSH. Run the following commands to simulate requests with specific headers and check the results:

    ```
    for i in `seq 10`; do curl -H "Host: foo.bar.com" http://47.107.XX.XX; done
    ```

    ```
    old
    new
    old
    new
    old
    new
    old
    new
    old
    new
    ```




