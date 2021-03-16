---
keyword: [secure HTTPS access, HTTPS access configuration]
---

# Access applications in an ACK cluster over HTTPS

You can access applications in an Alibaba Cloud Container Service for Kubernetes \(ACK\) cluster by using multiple methods. For example, you can request `SLB instance IP address:Port`, `NodeIP:NodePort`, or the domain name of the application. By default, you cannot access an open source Kubernetes cluster over HTTPS. You can enable secure HTTPS access by performing operations in the ACK console and the SLB console. This topic describes how to configure a certificate to enable HTTPS access to an ACK cluster.

-   A managed Kubernetes cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   You are connected to the cluster by using SSH. For more information, see [Use SSH to connect to a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use SSH to connect to an ACK cluster.md).
-   A server certificate is created for the cluster after you log on to the master node. The server certificate includes a public key certificate and a private key. You can run the following commands to create a server certificate:

    ```
    openssl genrsa -out tls.key 2048
    ```

    Output:

    ```
    Generating RSA private key, 2048 bit long modulus
    ................................................................ +++
    ........................................................................................+++
    e is 65537 (0x10001)
    ```

    ```
    openssl req -sha256 -new -x509 -days 365 -key tls.key -out tls.crt
    ```

    Output:

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


You can use one of the following methods to configure the certificate based on the method of accessing the cluster:

-   Configure the certificate on the frontend SLB instance.
-   Configure the certificate on the ingress.

## Configure the HTTPS certificate on the frontend SLB instance

This method has the following features:

-   Advantage: The certificate is configured on the frontend SLB instance to allow requests from outside the cluster. Requests from within the cluster are still based on HTTP.
-   Disadvantage: You must maintain the mappings between a large number of domain names and IP addresses.
-   Scenario: The applications in your cluster can be accessed through only services of the LoadBalancer type.

Before you configure HTTPS on the frontend SLB instance, a Tomcat application is created in the ACK cluster. You can access the application through a LoadBalancer service. For more information, see [Create a service](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Manage Services.md).

Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com)[ACK console](https://partners-intl.console.aliyun.com/#/cs).

2.  In the left-side navigation pane, choose **Discovery and Load Balancing** \> **Services**. Then, select the cluster and the namespace to view the Tomcat application. The Tomcat application name is tomcat and the service name is tomcat-svc, as shown in the following figure. The service type of the application is LoadBalancer, and the service port is 8080.

    ![Ingresses and Load Balancing](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3255359951/p13803.png)

3.  In the left-side navigation pane of the ACK console, click **Clusters**.

4.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

5.  In the left-side navigation pane of the details page, choose **Services and Ingresses** \> **Services**.

6.  Select the namespace and service of the cluster, and click the public endpoint to access the application. The endpoint is in the `IP:Port` format.

    ![Public endpoint](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3255359951/p13806.png)

7.  Log on to the [SLB console](https://slb.console.aliyun.com/).

8.  Log on to the [SLB console](https://partners-intl.console.aliyun.com/#/slb).

9.  In the left-side navigation pane, choose **Instances** \> **Instances**. Find the SLB instance that is associated with the external endpoint of tomcat-svc based on the IP address. Click **Configure Listener** in the Actions column.

10. Select a listening protocol. Select **HTTPS**, set Listening Port to **443**, and then click **Next**.

11. Configure **SSL Certificates**.

    1.  Click **Create Server Certificate**.

    2.  In the panel that appears, select a certificate source. In this example, select **Upload Third-party Certificate**.****

    3.  Specify the certificate name, enter the public key certificate and the private key, select the region in which you want to deploy the certificate, and then click **Create**.********

    4.  Select the newly created server certificate from the **Select Server Certificate** drop-down list.

    5.  Click **Next**.

12. Configure **Backend Servers**. By default, a server is added to the group. Configure a backend server **port** that the tomcat-svc service listens on, and then click **Next**.

    **Note:** You can find the NodePort of tomcat-svc in the Container Service for Kubernetes console. Add it to the Port field in the Backend Servers step.

13. Configure **Health Check**, and click **Next**. In this example, the default settings are used.

14. In the **Confirm** step, check the configurations and click **Submit**.

15. After the configuration is complete, click **OK**.

16. Return to the Instances page. Check whether the `HTTPS:443` listening rule is generated for the instance.

17. Access the Tomcat application over HTTPS by entering `https://slb_ip`in the address bar of a browser.

    **Note:** If the authenticated domain name is listed in the certificate, you can access the application by using the domain name. `tcp:8080` is not deleted. Therefore, you can also access the application through `slb_ip:8080`.

    ![Instance management](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3255359951/p13820.png)


## Configure the certificate on the ingress

This method has the following features:

-   Advantage: You do not need to modify the SLB configuration. You can manage the certificate of each application on the ingress.
-   Scenario: Each application requires a separate certificate, or an application in the cluster can be accessed only by using a certificate.

Preparations

A Tomcat application is created in the ACK cluster. You can access the application through a ClusterIP service. In this example, an ingress is used to establish HTTPS connections.

Example:

1.  Log on to the master node of the cluster and create a secret based on the prepared certificate.

    **Note:** You must specify a valid domain name. Otherwise, an error occurs when you access the application over HTTPS.

    ```
    kubectl create secret tls secret-https --key tls.key --cert tls.crt      
    ```

2.  Log on to the [ACK console](https://cs.console.aliyun.com)[ACK console](https://partners-intl.console.aliyun.com/#/cs).

3.  In the left-side navigation pane, choose **Ingresses and Load Balancing** \> **Ingresses**. Select a cluster and a namespace, and click **Create** in the upper-right corner.

4.  In the left-side navigation pane of the ACK console, click **Clusters**.

5.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

6.  Select the namespace, and click **Create** in the upper-right corner.

7.  In the dialog box that appears, configure an ingress that can be accessed through HTTPS, and then click **Create**.

    For more information, see [Create an ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Basic operations of an Ingress.md). In this example, configure the following parameters:

    -   **Name**: Specify a name for the ingress.
    -   **Domain**: Enter the domain name that is specified in the preceding steps. This domain name must be the same as the domain name in the SSL certificate.
    -   **Service**: Select the service related to the Tomcat application. The port number is 8080.
    -   **EnableTLS**: After you select EnableTLS, select an existing secret.
    You can also use a YAML file to create an ingress. The following YAML file is an example:

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

8.  Return to the Ingresses page. The newly created ingress, its endpoint, and its domain name appear in the list. In this example, the domain name is `foo.bar.com`. You can also view the ingress on the details page.

    **Note:** In this example, `foo.bar.com` is used as a test domain name. You must create a record in the hosts file.

    ```
    47.110.119.203 foo.bar.com#                           The IP address is the ingress endpoint.
    ```

    ![domain](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9957005061/p180533.png)

9.  Enter `https://foo.bar.com` in the address bar of a browser.

    **Note:** You must access the domain name through HTTPS because you have created a TLS certificate. In this example, `foo.bar.com` is resolved on a local DNS server. You must use a domain name that has obtained an Internet Content Provider \(ICP\) license.

    ![Use the ingress to access applications](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4255359951/p13837.png)


