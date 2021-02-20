---
keyword: [create Ingress, Ingress, ]
---

# Create an Ingress

The Container Service for Kubernetes \(ACK\) console is integrated with the Ingress service. You can create an Ingress in the console to manage external access to your ACK clusters. This topic describes how to create an Ingress in the ACK console.

-   An ACK cluster is created and an Ingress controller runs as expected in the cluster. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   You are connected to a master node of the cluster through SSH. For more information, see [Use SSH to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use SSH to connect to an ACK cluster.md).

## Step 1: Create a Deployment and a Service

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  Click the **Deployments** tab. In the upper-right corner of the Deployments tab, click **Create from Template**.

6.  Select the sample template or enter a custom template, and click **Create**.

    In this example, three NGINX applications are created: old-nginx, new-nginx, and domain-nginx. old-nginx is used as the NGINX application of the earlier version. new-nginx is used as the NGINX application of the latest version. domain-nginx is used to test access to the domain name of the cluster.

7.  Go to the details page of the cluster. In the left-side navigation pane, click **Services** to go to the Services page.

    On the Services page, You can find the related Services after they are created.

    ![Services](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5445359951/p10773.png)


## Step 2: Create an Ingress

1.  Go to the details page of the cluster. In the left-side navigation pane, click Ingresses.

2.  In the upper-right corner of the Ingresses page, click **Create**.

3.  In the Create dialog box, enter a name for the Ingress. In this example, the Ingress is named nginx-ingress.

4.  Configure Ingress rules.

    Ingress rules are used to manage external access to Services in the cluster. Ingress rules can be HTTP or HTTPS rules. You can configure the following items in rules: domain name \(virtual hostname\), URL path, Service name, port, and weight. For more information, see [Ingress configurations](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Ingress configurations.md).

    In this example, a complex rule is added to configure Services for the default domain name and virtual hostname of the cluster. Traffic routing is based on domain names.

    ![Ingress rules](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5445359951/p10776.png)

    -   Create a simple Ingress that uses the default domain name for external access.
        -   **Domain**: Enter the default domain name of the cluster. In this example, the default domain name is `test.[cluster-id].[region-id].alicontainer.com`.

            In the Create dialog box, the default domain name of the cluster is displayed in the format of `*.[cluster-id].[region-id].alicontainer.com`. You can also obtain the default domain name from the details page of the cluster.

        -   **Services**: Set the names of the backend Services and the path and port numbers that are used to access these Services.
            -   Path: You can enter the URL for accessing the backend Services. The default path is the root path /. In this example, the default path is used. Each path is associated with a backend Service. Server Load Balancer \(SLB\) forwards traffic to a backend Service only when inbound requests match the domain name and path.
            -   Services: Set the names of the backend Services and the path and port numbers that are used to access these Services. You can also set weights for these Services. You can configure multiple Services in the same path. The Ingress traffic is splitted and forwarded to the matched Services.
    -   Create a simple fanout Ingress that uses multiple domain names. In this example, a virtual hostname is used as the test domain name for external access. Route weights are specified for two backend Services and canary release settings are configured for one of the Services. In your production environment, you can use a domain name that has obtained an Internet Content Provider \(ICP\) number for external access.
        -   **Domain**: Enter the test domain name. In this example, the test domain name is `foo.bar.com`.

            You must add the following domain name mapping to the hosts file:

            ```
            118.178.XX.XX foo.bar.com       #The IP address of the Ingress.
            ```

        -   **Services**: Set the names of the backend Services and the path and port numbers that are used to access these Services. You can also set weights for these Services.
            -   Path: Enter the URL path of the backend Service. In this example, the default root path / is used.
            -   Name: Select the nginx-new and nginx-old Services.
            -   Port: In this example, port 80 is opened.
            -   Weight: Set a weight for each backend Service. The weight is a percentage value. The default value is 100. In this example, the weight for each backend Service is 50. This means that the two backend Services have the same weight.
5.  Configure canary release settings.

    ACK supports multiple traffic splitting methods. This allows you to select suitable solutions for specific scenarios, such as canary releases and A/B testing:

    1.  Traffic splitting based on request headers.
    2.  Traffic splitting based on cookies.
    3.  Traffic splitting based on query parameters.
    After canary release settings are configured, only requests that match the specified rules are routed to the new-nginx service. If the weight of new-nginx is lower than 100%, requests that match the specified rules are routed to the Service based on the weight.

    In this example, the rule is added to specify a request header that matches the regular expression `foo=^bar$`. Only requests with headers that match the regular expression can access new-nginx.

    ![Access new-nginx](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6445359951/p10777.png)

    -   **Services**: Select Services as the backend for the Ingress rule.
    -   **Type**: Select the type of the matching rule. Valid values: Header, Cookie, and Query.
    -   **Name and Match Value**: Specify custom request fields. The name and matching value comprise a key-value pair.
    -   **Matching Rule**: Regular expressions and exact matches are supported.
    **Note:** You can configure canary release settings for up to two Services.

6.  Configure annotations.

    Click **Add** on the right side of Annotations. In the **Type** drop-down list, you can select the type of the annotation based on the following description:

    -   **Custom Annotation**: Enter names and values as key-value pairs for the annotation. For more information, see [Annotations](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/).
    -   **Ingress-NGINX**: Select annotations by names.

        You can add an annotation to redirect inbound traffic. For example, `nginx.ingress.kubernetes.io/rewrite-target: /` specifies that /path is redirected to the root path /. The root path can be recognized by the backend Services.

        **Note:** In this example, no path is configured for the backend Services. Therefore, you do not need to configure rewrite annotations. Rewrite annotations allow the Ingress to forward traffic through root paths to the backend service. This avoids the error 404 that is caused by invalid paths.

7.  Configure TLS.

    Select **EnableTLS** to enable Transport Layer Security \(TLS\) and configure a secure Ingress. For more information, see [Configure an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Configure an Ingress.md).

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

8.  Add labels.

    Add labels to describe the Ingress.

    ![Add labels](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6445359951/p10783.png)

9.  Click **Create**. You are redirected to the Ingresses page.

    After the Ingress is created, you can find the nginx-ingress Ingress on the Ingresses page.

    ![The Ingresses page](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6445359951/p10784.png)


## Test the Ingress

1.  On the Ingresses page, click `test.[cluster-id].[region-id].alicontainer.com` and `foo.bar.com` in the Rules column. The NGINX welcome page appears.

    ![Access the domain name](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6445359951/p10786.png)

    Click the domain name that points to new-nginx, the old-nginx service page appears.

    **Note:** By default, when you enter the domain name in the browser, request headers do not match the `foo=^bar$` regular expression. Therefore, the requests are directed to old-nginx.

    ![The old-nginx application](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6445359951/p10791.png)

2.  Log on to a master node by using SSH. Run the following command to test access to different backend Services based on their weights:

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




