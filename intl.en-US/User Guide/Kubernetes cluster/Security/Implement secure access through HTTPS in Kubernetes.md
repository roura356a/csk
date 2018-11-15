# Implement secure access through HTTPS in Kubernetes {#concept_pxh_1yh_mfb .concept}

A Container Service Kubernetes cluster supports multiple application access methods. The most common methods include `SLB:Port` access, `NodeIP:NodePort` access, and domain name access. By default, a Kubernetes cluster does not support HTTPS access. To access applications through HTTPS, you can use the secure HTTPS access method provided by Container Service and Alibaba Cloud Server Load Balancer \(SLB\) service. This document explains how to configure a certificate in Container Service Kubernetes by using HTTPS access configuration as an example.

Depending on different access methods, your certificate can be configured with the following two methods:

-   Configure the certificate on the frontend SLB.
-   Configure the certificate on Ingress.

## Prerequisites {#section_mkm_lzh_mfb .section}

-   You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   You have connected to the Master node through SSH. For more information, see [Access Kubernetes clusters by using SSH](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Access Kubernetes clusters by using SSH.md#).
-   After connecting to the Master node, you have created the server certificates for the cluster, including the public key certificate and the private key certificate by running the following commands :

    ```
    $  openssl genrsa -out tls.key 2048
    
    Generating RSA private key, 2048 bit long modulus
    ................................................................ +++
    ........................................................................................+++
    e is 65537 (0x10001)
    
    $  openssl req -sha256 -new -x509 -days 365 -key tls.key -out tls.crt
    
    You are about to be asked to enter information that will be incorporated
    ...
    -----
    Country Name (2 letter code) [XX]:CN
    State or Province Name (full name) []:zhejiang
    Locality Name (eg, city) [Default City]:hangzhou
    Organization Name (eg, company) [Default Company Ltd]:alibaba
    Organizational Unit Name (eg, section) []:test
    Common Name (eg, your name or your server's hostname) []:foo.bar.com           #you must configure the domain name correctly
    Email Address []:a@alibaba.com
    ```


## Method 1: Configure the HTTPS certificate on SLB {#section_lgr_lyh_mfb .section}

This method has the following advantages and disadvantages:

-   **Advantages**: The certificate is configured on SLB and it is the external access portal of applications. The access to applications in the cluster still uses the HTTP access method.
-   **Disadvantages**: You need to maintain many associations between domain names and their corresponding IP addresses.
-   **Scenarios**: This method is applicable to applications that use LoadBalancer service rather than Ingress to expose access methods.

**Preparations**

You have created a Tomcat application in the Kubernetes cluster. The application provides external access by using the LoadBalancer service. For more information, see [Create a service](reseller.en-US/User Guide/Kubernetes cluster/Applications/Create a service.md#).

**Example**

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane, click **Application** \> **Service**, and select the cluster and the namespace to view the pre-created Tomcat application. As shown in the following figure, the created Tomcat application is named tomcat and the service name is tomcat-svc. The service type of the application is LoadBalancer, and the service port exposed by the application is 8080.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23807/154226658313803_en-US.png)

3.  By clicking the external endpoint, you can access the Tomcat application through `IP:Port`.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23807/154226658313806_en-US.png)

4.  Log on to the [SLB console](https://partners-intl.console.aliyun.com/#/slb).
5.  By default, the Server Load Balancer page is displayed. In the IP address column, find the server load balancer that corresponds to the external endpoint of the tomcat-svc service, and click **Configure Listener** in the actions column.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23807/154226658313808_en-US.png)

6.  Configure the server load balancer. Select a listener protocol first. Select **HTTPS**, set the listening port to **443**, and then click **Next**.
7.  Configure the **SSL certificate**.
    1.  Click **Create Server Certificate**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23807/154226658413809_en-US.png)

    2.  On the displayed page, select a certificate source. In this example, select **Upload Third-Party Certificate**, and then click **Next**.
    3.  On the uploading third-party certificate page, set the certificate name and select the region in which the certificate is deployed. In the **Certificate Content** and the **Private Key** columns, enter the server public key certificate and private key created in [Prerequisites](#section_mkm_lzh_mfb), and then click **OK**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23807/154226658413812_en-US.png)

    4.  From the **Select Server Certificate** drop-down list, select the created server certificate.
    5.  Click **Next**.
8.  Configure **Backend Servers**. By default, servers are added. You need to configure a **port** for each backend server to listen to the tomcat-svc service, and then click **Next**.

    **Note:** You need to find the NodePort number of this service in the Container Service Web interface, and configure the number as the port number of each backend server.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23807/154226658413815_en-US.png)

9.  Configure **Health Check**, and then click **Next**. In this example, use the default settings.
10. Confirm the **Submit** tab. When you make sure that all configurations are correct, click **Submit**.
11. After completing the configuration, click **OK**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23807/154226658413816_en-US.png)

12. Return to the Server Load Balancer page to view the instance. The listening rule of `HTTPS:443`is generated.
13. Access the Tomcat application through HTTPS. In the address bar of the browser, enter `https://slb_ip` to access the application.

    **Note:** If the domain name authentication is included in the certificate, you can access the application by using the domain name. You can also access the application through `slb_ip:8080` because `tcp:8080` is not deleted.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23807/154226658413820_en-US.png)


## Method 2: Configure the certificate on Ingress {#section_w3l_knn_mfb .section}

This method has the following advantages and disadvantages:

-   **Advantages**: You do not need to modify the SLB configuration. All applications can manage their own certificates through Ingress without interfering with each other.
-   **Disadvantages**: Each application can be accessed by using a separate certificate or the cluster has applications that can be accessed by only using a certificate.

**Preparations**

You have created a Tomcat application in the Kubernetes cluster. The service of the application provides access through ClusterIP. In this example, use Ingress to provide the HTTPS access service.

**Example**

1.  Log on to the Master node of the Kubernetes cluster and create a secret according to the prepared certificate.

    **Note:** You must set the domain name properly. Otherwise, you will encounter exceptions when accessing the application through HTTPS.

    ```
    kubectl create secret tls secret-https --key tls.key --cert tls.crt      
    ```

2.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
3.  In the left-side navigation pane, click **Application** \> **Ingress**, select a cluster and namespace, and click **Create** in the upper-right corner.
4.  In the displayed dialog box, configure the Ingress to make it accessible through HTTPS, and then click **OK**.

    For more information about Ingress configuration, see [Create an Ingress in Container Service console](reseller.en-US/User Guide/Kubernetes cluster/Server Load Balancer and Ingress/Create an Ingress in Container Service console.md#). The configuration in this example is as follows:

    -   **Name**: Enter an Ingress name.
    -   **Domain**: Enter the domain name set in the preceding steps. It must be the same as that configured in the SSL certificate.
    -   **Service**: Select the service corresponding to the tomcat application.The service port is 8080.
    -   **Enable TLS**: After enabling TLS, select the existing secret.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23807/154226658413832_en-US.png)

    You can also use a YAML file to create an Ingress. In this example, the YAML sample file is as follows:

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

5.  Return to the Ingress list to view the created Ingress, the endpoint, and the domain name. In this example, the domain name is `foo.bar.com`. You can also enter the Ingress detail page to view the Ingress.

    **Note:** In this example, `foo.bar.com` is used as a testing domain name, and you need to create a record in the hosts file.

    ```
    47.110.119.203  foo.bar.com                   #where, the IP address is the Ingress endpoint.
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23807/154226658413836_en-US.png)

6.  In the browser, access `https://foo.bar.com`.

    **Note:** You need to access the domain name by using HTTPS because you have created a TLS access certificate. This example uses `foo.bar.com` as a sample domain name to be parsed locally. In your specific configuration scenarios, you need to use the registered domain names.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23807/154226658413837_en-US.png)


