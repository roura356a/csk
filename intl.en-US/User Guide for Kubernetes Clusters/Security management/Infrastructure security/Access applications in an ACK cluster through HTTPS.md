---
keyword: [secure HTTPS access, HTTPS access configuration]
---

# Access applications in an ACK cluster through HTTPS

You can access applications in a Container Service for Kubernetes \(ACK\) cluster by using multiple methods. For example, you can request `<SLB-Instance-IP>:<Port>`, `<NodeIP>:<NodePort>`, or the domain name of the application. By default, you cannot access a Kubernetes cluster through HTTPS. You can enable secure HTTPS access by using ACK and Server Load Balancer \(SLB\). This topic describes how to enable HTTPS access to an ACK cluster by using a certificate.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   A server certificate is created for the cluster. The server certificate includes a public key certificate and a private key.
    -   You can run the following command to create a server certificate:

        ```
        openssl genrsa -out tls.key 2048
        ```

        The following output is returned:

        ```
        Generating RSA private key, 2048 bit long modulus
        ................................................................ +++
        ........................................................................................+++
        e is 65537 (0x10001)
        ```

        ```
        ls
        ```

        The following output is returned:

        ```
        You are about to be asked to enter information that will be incorporated
        ...
        -----
        Country Name (2 letter code) [XX]:CN
        State or Province Name (full name) []:zhejiang
        Locality Name (eg, city) [Default City]:hangzhou
        Organization Name (eg, company) [Default Company Ltd]:alibaba
        Organizational Unit Name (eg, section) []:test
        Common Name (eg, your name or your server's hostname) []:foo.bar.com           # You must specify a valid domain name.
        Email Address []:a@alibaba.com
        ```

    -   You can also purchase a server certificate that is issued by Alibaba Cloud. For more information, see [Select a certificate from Alibaba Cloud SSL Certificates Service](/intl.en-US/Classic Load Balancer/User Guide/Certificate management/Create a certificate/Select a certificate from Alibaba Cloud SSL Certificates Service.md).

You can select the following methods to configure the certificate based on how you want to access the cluster.

-   Specify the certificate information in a frontend SLB instance.
-   Specify the certificate information in an Ingress.

## Specify the certificate information in a frontend SLB instance

If you use this method, take note of the following items:

-   Advantage: A frontend SLB instance is configured with the certificate information to accept external access. Access from within the cluster is still based on HTTP.
-   Disadvantage: You must maintain the mappings between a large number of domain names and IP addresses.
-   Scenario: Your application is accessed through LoadBalancer type Services rather than Ingresses.

Preparations

An NGINX application is deployed in the cluster. You can access the application through a LoadBalancer type Service. For more information, see [Deploy a stateless application from an image](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Deploy a stateless application from an image.md).

Examples:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Services and Ingresses** \> **Services**.

5.  Select the namespace where the related Service is deployed and click the external endpoint to access the application. The endpoint is in the `<SLB IP>:<Port>` format.

    ![nginx](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1104316161/p241037.png)

6.  Log on to the [SLB console](https://slb.console.aliyun.com/).

7.  Configure an **SSL Certificate**.

    -   If you have created a server certificate by running commands as described in the prerequisites, you need to upload the created certificate, including the public key certificate and the private key, to Alibaba Cloud. For more information, see [Upload a third-party certificate](/intl.en-US/Classic Load Balancer/User Guide/Certificate management/Create a certificate/Upload a third-party certificate.md).
    -   If you have purchased a server certificate that is issued by Alibaba Cloud, skip this step. For more information about how to purchase a server certificate that is issued by Alibaba Cloud, see [Select a certificate from Alibaba Cloud SSL Certificates Service](/intl.en-US/Classic Load Balancer/User Guide/Certificate management/Create a certificate/Select a certificate from Alibaba Cloud SSL Certificates Service.md).
    Find the certificate that you want to use from the certificate list.

8.  On the Services page of the ACK console, find the created Service and click **Update** in the **Actions** column.

9.  In the **Update Service** dialog box, enter the annotations as shown in the following figure.

    ![Annotations](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1104316161/p241100.png)

    |Annotation|Name|Value|
    |----------|----|-----|
    |Annotation 1|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-protocol-port|https:443|
    |Annotation 2|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-cert-id|$\{YOUR\_CERT\_ID\}|

    **Note:** Replace $\{YOUR\_CERT\_ID\} with the ID of the certificate that is configured in [Step 7](#step_0js_m4a_04m).

    You can also add the annotations by using YAML files. The following YAML file is an example:

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-protocol-port: "https:443"
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-cert-id: "${YOUR_CERT_ID}"
      name: nginx
      namespace: default
    spec:
      ports:
      - name: https
        port: 443
        protocol: TCP
        targetPort: 80
      - name: http
        port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      type: LoadBalancer
    ```

    **Note:** Set targetPort to 80. This way, requests to HTTPS port 443 are redirected to HTTP port 80.

10. Enter `https://<slb-instance-ip>` into the address bar of your browser to access the NGINX application through HTTPS.

    ![https](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1104316161/p241101.png)


## Specify the certificate information in an Ingress

If you use this method, take note of the following items:

-   Advantage: You do not need to modify the SLB configurations. You can separately manage the certificate of each application by using Ingresses.
-   Scenario: Each application in your cluster requires a separate certificate, or an application in the cluster can be accessed only by using a certificate.

Preparations

A Tomcat application is created in your cluster. You can access the application through a ClusterIP type Service. In this example, an Ingress is used to enable external access through HTTPS. For more information, see [Deploy a stateless application from an image](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Deploy a stateless application from an image.md).

Examples:

1.  Run the following command to create a Secret by using the certificate that is created in the prerequisites:

    **Note:** You must specify a valid domain name. Otherwise, an error occurs when you access the application through HTTPS.

    ```
    kubectl create secret tls secret-https --key tls.key --cert tls.crt      
    ```

2.  Log on to the [ACK console](https://cs.console.aliyun.com).

3.  In the left-side navigation pane of the ACK console, click **Clusters**.

4.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

5.  In the left-side navigation pane of the details page, click **Namespaces and Quotas**. In the upper-right corner of the Namespace page, click **Create**.

6.  In the dialog box that appears, configure an Ingress that can be accessed through HTTPS and click **Create**.

    For more information, see [Create an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Basic operations of an Ingress.md). In this example, set the following parameters:

    -   **Name**: Enter a name for the Ingress.
    -   **Domain**: Enter the domain name that is specified in the preceding steps. This domain name must be the same as the domain name that is specified in the SSL certificate.
    -   **Service**: Select the Service that is related to the Tomcat application. The port number is 8080.
    -   **EnableTLS**: After you select EnableTLS, select the created Secret.
    You can also create an Ingress by using YAML files. The following YAML template is an example:

    ```
    apiVersion: extensions/v1beta1
    
    kind: Ingress
    
    metadata:
    
       name: tomcat-https
    
    spec:
    
      tls:
    
      - hosts:
    
        - foo.bar.com
    
        secretName: secret-https
    
      rules:
    
      - host: foo.bar.com
    
        http:
    
          paths:
    
          - path: /
    
            backend:
    
              serviceName: tomcat-svc
    
              servicePort: 8080
    ```

7.  Return to the Ingresses page to view the newly created Ingress. The endpoint and domain name of the Ingress also appear on the page. In this example, the domain name is `foo.bar.com`. You can also view these details on the details page of the Ingress.

    **Note:** In this example, `foo.bar.com` is used as a test domain name. You must add a mapping to the hosts file.

    ```
    47.110.119.203  foo.bar.com                   # The IP address is the endpoint of the Ingress.
    ```

8.  Enter `https://foo.bar.com` into the address bar of your browser to access the Tomcat application.

    **Note:** A TLS certificate is created and configured for the Ingress. Therefore, you must access the domain name through HTTPS. In this example, `foo.bar.com` is resolved on a local DNS server. You must use a domain name that has obtained an Internet Content Provider \(ICP\) number.

    ![Use the Ingress to access the application](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4255359951/p13837.png)


