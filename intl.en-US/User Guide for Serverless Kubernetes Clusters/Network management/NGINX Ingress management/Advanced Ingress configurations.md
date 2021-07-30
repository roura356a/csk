---
keyword: [create an Ingress to secure data transmission, rewrite rules, domain names, mutual TLS authentication, canary releases, apply for TLS certificates]
---

# Advanced Ingress configurations

An Ingress is an API object that provides Layer 7 load balancing to manage external access to Services in a Kubernetes cluster. Container Service for Kubernetes allows you to use the advanced features of Ingresses to configure specific URLs to allow external access, set rewrite rules, configure HTTPS, and implement canary releases. This topic describes how to configure secure data transmission, set mutual TLS authentication, use regular expressions to specify domain names, use wildcard domain names, apply for free TLS certificates, and customize other related features.

-   An ASK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   An Ingress controller is running as normal in the cluster.
-   A kubectl client is connected to the cluster. For more information, see [t16645.dita\#task\_ubf\_lhg\_vdb](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md).
-   A Deployment and a Service are created. For more information, see [Manage Ingresses by using kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Create an Ingress.md).

## Configurations

The configuration methods of the ingress-nginx-controller component in ASK are fully compatible with the open source version of the component. For more information about the configuration methods, see [NGINX Configuration](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/).

The following configuration methods are supported:

-   Annotation: You can modify annotations in the YAML template of each Ingress. The annotations take effect on individual Ingresses. For more information, see [Annotations](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/).
-   ConfigMap: You can modify the kube-system/nginx-configuration ConfigMap to set a global configuration for all Ingresses. For more information, see [ConfigMaps](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/).
-   Custom NGINX template: You can customize the NGINX template of an Ingress controller if the preceding methods cannot meet your requirements. For more information, see [Custom NGINX template](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/custom-template/).

## Configure routing rules to redirect traffic from specific URLs

Run the following command to create an Ingress. The Ingress redirects traffic that is destined for the /svc path to the / path. The / path can be recognized by backend Services.

1.  The following template shows the configuration of the Ingress:

    ```
    cat <<-EOF | kubectl apply -f -
    apiVersion: networking.k8s.io/v1beta1
    kind: Ingress
    metadata:
      name: rewrite-test-ingress
      namespace: default
      annotations:
        # URL redirection
        nginx.ingress.kubernetes.io/rewrite-target: /$1
    spec:
      rules:
      - host: rewrite-test-ingress.com
        http:
          paths:
          - path: /svc/(.*)
            backend:
              serviceName: web1-service
              servicePort: 80
    EOF
    ```

2.  Run the following command to access the NGINX application.

    Replace **IP\_ADDRESS** with the IP address of the created Ingress. You can obtain the IP address by running the `kubectl get ing` command.

    ```
    curl -k -H "Host: rewrite-test-ingress.com"  http://{IP_ADDRESS}/svc/foo
    ```

    Expected output:

    ```
    web1: /foo
    ```


## Configure rewrite rules

The `inginx.ingress.kubernetes.io/rewrite-target` annotation can be used to configure basic rewrite rules. To configure advanced rewrite rules, use the following annotations:

-   `nginx.ingress.kubernetes.io/server-snippet`: This annotation adds custom configurations to the Server configuration block.
-   `nginx.ingress.kubernetes.io/configuration-snippet`: This annotation adds custom configurations to the Location configuration block.

Examples:

```
annotations:
     nginx.ingress.kubernetes.io/server-snippet: |
         rewrite ^/v4/(.*)/card/query http://m.maizuo.com/v5/#!/card/query permanent;
     nginx.ingress.kubernetes.io/configuration-snippet: |
         rewrite ^/v6/(.*)/card/query http://m.maizuo.com/v7/#!/card/query permanent;
```

The following example shows the configuration of the nginx.conf file:

```
## start server foo.bar.com
    server {
        server_name foo.bar.com ;
        listen 80;
        listen [::]:80;
        set $proxy_upstream_name "-";
    ### Configuration of server-snippet. 
        rewrite ^/v4/(.*)/card/query http://m.maizuo.com/v5/#!/card/query permanent;
        ...
    ### Configuration of configuration-snippet. 
      rewrite ^/v6/(.*)/card/query http://m.maizuo.com/v7/#!/card/query permanent;
      ...
    }
    ## end server foo.bar.com
```

Snippet also supports some global configurations. For more information, see [server-snippet](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/#main-snippet).

## Create an Ingress to secure data transmission

ACK allows you to use multiple types of certificate to reinforce the security of your services.

1.  Prepare your certificate.

    If you do not have a certificate, perform the following steps to generate a test certificate:

    **Note:** The domain name must be the same as the one that is specified in the Ingress configuration.

    1.  Run the following command to generate a certificate file named tls.crt and a private key file named tls.key:

        ```
        openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=foo.bar.com/O=foo.bar.com"
        ```

    2.  Run the following command to create a Kubernetes Secret.

        Use the certificate and private key to create a Secret named tls-test-ingress. The Secret is referenced when you create the Ingress.

        ```
        kubectl create secret tls tls-test-ingress --key tls.key --cert tls.crt
        ```

2.  Run the following command to create an Ingress to secure data transmission:

    ```
    cat <<EOF | kubectl create -f - 
    apiVersion: networking.k8s.io/v1beta1
    kind: Ingress
    metadata:
      name: test-test-ingress
    spec:
        #Secure data transmission
      tls:
      - hosts:
        - tls-test-ingress.com
        secretName: tls-test-ingress
      rules:
      - host: tls-test-ingress.com
        http:
          paths:
          - path: /foo
            backend:
              serviceName: web1-svc
              servicePort: 80
    EOF
    ```

3.  Run the following command to query the Ingress:

    ```
    kubectl get ing
    ```

    Expected output:

    ```
    NAME                   HOSTS         ADDRESS          PORTS     AGE
    test-test-ingress      *             101.37.XX.XX     80        11s
    ```

4.  After the Ingress is created, you must modify the `hosts` file or specify a domain name before you can use TLS to secure data transmission.

    You can access the `web1-svc` service through `http://tls-test-ingress.com/foo`.


## Configure mutual TLS authentication

Mutual TLS authentication is required in some scenarios. This feature is supported by Ingress-NGINX. Perform the following steps to configure mutual TLS authentication:

1.  Run the following command to create a self-signed Certificate Authority \(CA\) certificate:

    ```
    openssl req -x509 -sha256 -newkey rsa:4096 -keyout ca.key -out ca.crt -days 356 -nodes -subj '/CN=Fern Cert Authority'
    ```

    Expected output:

    ```
    Generating a 4096 bit RSA private key
    .............................................................................................................++
    .....................................................................................++
    writing new private key to 'ca.key'
    ```

2.  Create a server certificate.

    1.  Run the following command to generate a file that is used to request a server certificate:

        ```
        openssl req -new -newkey rsa:4096 -keyout server.key -out server.csr -nodes -subj '/CN=test.nginx.ingress.com'
        ```

        Expected output:

        ```
        Generating a 4096 bit RSA private key
        ................................................................................................................................++
        .................................................................++
        writing new private key to 'server.key'
        ```

    2.  Run the following command to use the root certificate to sign the file and generate a server certificate:

        ```
        openssl x509 -req -sha256 -days 365 -in server.csr -CA ca.crt -CAkey ca.key -set_serial 01 -out server.crt
        ```

        Expected output:

        ```
        Signature ok
        subject=/CN=test.nginx.ingress.com
        Getting CA Private Key
        ```

3.  Create a client certificate.

    1.  Run the following command to generate a file that is used to request a client certificate:

        ```
        openssl req -new -newkey rsa:4096 -keyout client.key -out client.csr -nodes -subj '/CN=Fern'
        ```

        Expected output:

        ```
        Generating a 4096 bit RSA private key
        .......................................................................................................................................................................................++
        ..............................................++
        writing new private key to 'client.key'
        -----
        ```

    2.  Run the following command to use the root certificate to sign the file and generate a client certificate:

        ```
        openssl x509 -req -sha256 -days 365 -in client.csr -CA ca.crt -CAkey ca.key -set_serial 02 -out client.crt
        ```

        Expected output:

        ```
        Signature ok
        subject=/CN=Fern
        Getting CA Private Key
        ```

4.  Run the following command to query the created certificate files:

    ```
    ls
    ```

    Expected output:

    ```
    ca.crt  ca.key  client.crt  client.csr  client.key  server.crt  server.csr  server.key
    ```

5.  Run the following command to create a Secret based on the created CA certificate:

    ```
    kubectl create secret generic ca-secret --from-file=ca.crt=ca.crt
    ```

    Expected output:

    ```
    secret/ca-secret created
    ```

6.  Run the following command to create a Secret based on the created server certificate:

    ```
    kubectl create secret generic tls-secret --from-file=tls.crt=server.crt --from-file=tls.key=server.key
    ```

    Expected output:

    ```
    secret/tls-secret created
    ```

7.  Run the following command to create an Ingress for testing purposes:

    ```
    cat <<-EOF | kubectl apply -f -
    apiVersion: networking.k8s.io/v1beta1
    kind: Ingress
    metadata:
      annotations:
        nginx.ingress.kubernetes.io/auth-tls-verify-client: "on"
        nginx.ingress.kubernetes.io/auth-tls-secret: "default/ca-secret"
        nginx.ingress.kubernetes.io/auth-tls-verify-depth: "1"
        nginx.ingress.kubernetes.io/auth-tls-pass-certificate-to-upstream: "true"
      name: nginx-test
      namespace: default
    spec:
      rules:
      - host: test.nginx.ingress.com
        http:
          paths:
          - backend:
              serviceName: http-svc
              servicePort: 80
            path: /
      tls:
      - hosts:
        - test.nginx.ingrss.com
        secretName: tls-secret
    EOF
    ```

    Expected output:

    ```
    ingress.networking.k8s.io/nginx-test configured
    ```

8.  Run the following command to obtain the IP address of the created Ingress:

    ```
    kubectl get ing
    ```

    The IP address in the ADDRESS field is the IP address of the created Ingress, as shown in the following output:

    ```
    NAME         HOSTS                    ADDRESS         PORTS     AGE
    nginx-test   test.nginx.ingress.com   39.102.xx.xx   80, 443   4h42m
    ```

9.  Run the following command to replace the IP address in the hosts file with the obtained IP address:

    ```
    sudo echo "39.102.xx.xx  test.nginx.ingress.com" >> /etc/hosts
    ```

    **Verify the configuration:**

    -   The client does not provide the client certificate when it accesses the server

        ```
        curl --cacert ./ca.crt  https://test.nginx.ingress.com
        ```

        Expected output:

        ```
        <html>
        <head><title>400 No required SSL certificate was sent</title></head>
        <body>
        <center><h1>400 Bad Request</h1></center>
        <center>No required SSL certificate was sent</center>
        <hr><center>nginx/1.19.0</center>
        </body>
        </html>
        ```

    -   The client provides the client certificate when it accesses the server

        ```
        curl --cacert ./ca.crt --cert ./client.crt --key ./client.key https://test.nginx.ingress.com
        ```

        Expected output:

        ```
        <!DOCTYPE html>
        <html>
        <head>
        <title>Welcome to nginx!</title>
        <style>
            body {
                width: 35em;
                margin: 0 auto;
                font-family: Tahoma, Verdana, Arial, sans-serif;
            }
        </style>
        </head>
        <body>
        <h1>Welcome to nginx!</h1>
        <p>If you see this page, the nginx web server is successfully installed and
        working. Further configuration is required.</p>
        
        <p>For online documentation and support please refer to
        <a href="http://nginx.org/">nginx.org</a>.<br/>
        Commercial support is available at
        <a href="http://nginx.com/">nginx.com</a>.</p>
        
        <p><em>Thank you for using nginx.</em></p>
        </body>
        </html>
        ```


## Forward HTTPS requests from Services to backend containers

You can enable an Ingress controller to forward HTTPS requests from HTTPS Services to backend containers by adding the `nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"` annotation.

The following template shows the configuration of the Ingress:

```
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: backend-https
  annotations:
    # You must specify an HTTPS Service. 
    nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"
spec:
  tls:
  - hosts:
    - your-host-name
    secretName: your-sercret-cert-name
  rules:
  - host: your-host-name
    http:
      paths:
      - path: /
        backend:
          serviceName: your-service-name
          servicePort: your-service-port
```

## Use regular expressions to specify domain names

By default, you cannot use regular expressions to specify domain names when you configure Ingresses for Kubernetes clusters. However, you can enable Ingresses to support regular expressions by adding the `nginx.ingress.kubernetes.io/server-alias` annotation.

1.  Create an Ingress. In the following example, the domain name is set to the regular expression `~^www\.\d+\.example\.com`.

    ```
    cat <<-EOF | kubectl apply -f -
    apiVersion: networking.k8s.io/v1beta1
    kind: Ingress
    metadata:
      name: ingress-regex
      namespace: default
      annotations:
        nginx.ingress.kubernetes.io/server-alias: '~^www\.\d+\.example\.com$, abc.example.com'
    spec:
      rules:
      - host: foo.bar.com
        http:
          paths:
          - path: /foo
            backend:
              serviceName: http-svc1
              servicePort: 80
     EOF
    ```

2.  Query the configuration of the NGINX Ingress controller.

    1.  Run the following command to query the pods that are provisioned for the NGINX Ingress controller:

        ```
        kubectl get pods -n kube-system | grep nginx-ingress-controller
        ```

        Expected output:

        ```
        nginx-ingress-controller-77cd987c4c-cp87g         1/1     Running   0          1h
        nginx-ingress-controller-77cd987c4c-xvpt5         1/1     Running   0          1h
        ```

    2.  Run the following command to query the configuration of the NGINX Ingress controller. The output shows the effective configurations, such as the server\_name field in this example.

        ```
        kubectl exec -n kube-system nginx-ingress-controller-77cd987c4c-cp87g cat /etc/nginx/nginx.conf | grep -C3 "regex.ingress.test.com"
        ```

        Expected output:

        ```
          ## start server foo.bar.com
          server {
        --
          server {
            server_name foo.bar.com abc.example.com ~^www\.\d+\.example\.com$ ;
            listen 80  ;
            listen 443  ssl http2 ;
        --
        --
            }
          }
          ## end server foo.bar.com
        ```

3.  Run the following command to obtain the IP address of the Ingress:

    ```
    kubectl get ing
    ```

    Expected output:

    ```
    NAME            HOSTS         ADDRESS          PORTS     AGE
    ingress-regex   foo.bar.com   101.37.XX.XX     80        11s
    ```

4.  Run the following commands to access a Service by using different Ingress rules.

    Replace **IP\_ADDRESS** with the IP address obtained in the previous step.

    -   Run the following command to access the Service through `Host: foo.bar.com`:

        ```
        curl -H "Host: foo.bar.com" {IP_ADDRESS}/foo
        ```

        Expected output:

        ```
        /foo
        ```

    -   Run the following command to access the Service through `Host: www.123.example.com`:

        ```
        curl -H "Host: www.123.example.com" {IP_ADDRESS}/foo
        ```

        Expected output:

        ```
        /foo
        ```

    -   Run the following command to access the Service through `Host: www.321.example.com`:

        ```
        curl -H "Host: www.321.example.com" {IP_ADDRESS}/foo
        ```

        Expected output:

        ```
        /foo
        ```


## Specify wildcard domain names

In Kubernetes clusters, Ingresses support wildcard domain names. For example, you can specify the wildcard domain name `*. ingress-regex.com` for an Ingress.

1.  The following template shows the configuration of an Ingress that uses a wildcard domain name:

    ```
    $ cat <<-EOF | kubectl apply -f -
    apiVersion: networking.k8s.io/v1beta1
    kind: Ingress
    metadata:
      name: ingress-regex
      namespace: default
    spec:
      rules:
      - host: '*.ingress-regex.com'
        http:
          paths:
          - path: /foo
            backend:
              serviceName: http-svc1
              servicePort: 80
     EOF
    ```

2.  Run the following command to query the configuration of the NGINX Ingress controller. The output shows the effective configurations, such as the server\_name field in this example.

    ```
    kubectl exec -n kube-system {ningx-ingress-pod-name} cat /etc/nginx/nginx.conf | grep -C3 "ingress-regex.com"
    ```

    **Note:** Replace ningx-ingress-pod-name with the name of the pod that is provisioned for the NGINX Ingress controller.

    Expected output:

    ```
    ## start server *.bar.com
      server {
        server_name *.ingress-regex.com ;
        listen 80;
        listen [::]:80;
    ...
      }
      ## end server *.bar.com
    ```

3.  Run the following command to obtain the IP address of the Ingress:

    ```
    kubectl get ing
    ```

    Expected output:

    ```
    NAME            HOSTS         ADDRESS          PORTS     AGE
    ingress-regex   *.bar.com    101.37.XX.XX      80        11s
    ```

4.  Run the following commands to access a Service by using different Ingress rules.

    Replace **IP\_ADDRESS** with the IP address obtained in the previous step.

    -   Run the following command to access the Service through `Host: abc.ingress-regex.com`:

        ```
        curl -H "Host: abc.ingress-regex.com" {IP_ADDRESS}/foo
        ```

        Expected output:

        ```
        /foo
        ```

    -   Run the following command to access the Service through `Host: 123.ingress-regex.com`:

        ```
        curl -H "Host: 123.ingress-regex.com" {IP_ADDRESS}/foo
        ```

        Expected output:

        ```
        /foo
        ```

    -   Run the following command to access the Service through `Host: a1b1.ingress-regex.com`:

        ```
        curl -H "Host: a1b1.ingress-regex.com" {IP_ADDRESS}/foo
        ```

        Expected output:

        ```
        /foo
        ```


## Use annotations to implement canary releases

You can implement canary releases by adding annotations to Ingresses. To enable canary releases, you must add the `nginx.ingress.kubernetes.io/canary: "true"` annotation. This section describes how to use different annotations to implement canary releases.

-   `nginx.ingress.kubernetes.io/canary-weight`: This annotation allows you to set the percentage of requests that are sent to the specified Service. You can enter an integer from 1 to 100.
-   `nginx.ingress.kubernetes.io/canary-by-header`: This annotation enables traffic splitting based on the request header. If the `header` value is always, requests are distributed to new Services. If the `header` value is never, requests are not distributed to new Services. If the header value is neither always nor never, requests are distributed to new Services based on other matching rules in descending order of priority.
-   `nginx.ingress.kubernetes.io/canary-by-header-value` and `nginx.ingress.kubernetes.io/canary-by-header-value`: When the values of `header` and `header-value` in the requests match the specified values in the annotations, requests are distributed to new Services. Otherwise, requests are distributed to new Services based on other matching rules in descending order of priority.
-   `nginx.ingress.kubernetes.io/canary-by-cookie`: This annotation enables cookie-based traffic splitting. If the `cookie` value is always, requests are distributed to new Services. If the `cookie` value is never, requests are not distributed to new Services.

Examples of different annotations:

-   Weight-based canary release: Set the weight of Services to 20%.

    ```
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      annotations:
        kubernetes.io/ingress.class: nginx
        nginx.ingress.kubernetes.io/canary: "true"
        nginx.ingress.kubernetes.io/canary-weight: "20"
    ```

-   Header-based canary release: When the request header is `ack:always`, requests are forwarded to new Services. When the request header is `ack:never`, requests are not distributed to new Services. If the header is neither ack:always nor ack:never, requests are distributed to new Services based on weights.

    ```
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      annotations:
        kubernetes.io/ingress.class: nginx
        nginx.ingress.kubernetes.io/canary: "true"
        nginx.ingress.kubernetes.io/canary-weight: "50"
        nginx.ingress.kubernetes.io/canary-by-header: "ack"
    ```

-   Header-based canary release with custom header values: If the request header is `ack:alibaba`, requests are distributed to new Services. Otherwise, requests are distributed to new Services based on weights.

    ```
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      annotations:
        kubernetes.io/ingress.class: nginx
        nginx.ingress.kubernetes.io/canary: "true"
        nginx.ingress.kubernetes.io/canary-weight: "20"
        nginx.ingress.kubernetes.io/canary-by-header: "ack"
        nginx.ingress.kubernetes.io/canary-by-header-value: "alibaba"
    ```

-   Cookie-based canary release: If the request header is not matched and the request cookie is `hangzhou_region=always`, requests are distributed to new Services.

    ```
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      annotations:
        kubernetes.io/ingress.class: nginx
        nginx.ingress.kubernetes.io/canary: "true"
        nginx.ingress.kubernetes.io/canary-weight: "20"
        nginx.ingress.kubernetes.io/canary-by-header: "ack"
        nginx.ingress.kubernetes.io/canary-by-header-value: "alibaba"
        nginx.ingress.kubernetes.io/canary-by-cookie: "hangzhou_region"
    ```


**Note:**

-   Cookie-based canary release does not support custom settings. The cookie value must be always or never.
-   Canary releases that use different rules take effect in the following order: header-based \> cookie-based \> weight-based.

## Use cert-manager to apply for a free TLS certificate

cert-manager is an open source tool used to manage cloud-native certificates. You can use cert-manager in Kubernetes clusters to apply for TLS certificates and enable auto-renewal for the certificates. This section describes how to apply for a free certificate and enable auto-renewal for the certificate by using cert-manager.

1.  Run the following command to deploy cert-manager:

    ```
    kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v1.1.0/cert-manager.yaml
    ```

2.  Run the following command to query the status of the pods that are provisioned for cert-manager:

    ```
    kubectl get pods -n cert-manager
    ```

    Expected output:

    ```
    NAME                     READY   STATUS    RESTARTS   AGE
    cert-manager-1           1/1     Running   0          2m11s
    cert-manager-cainjector  1/1     Running   0          2m11s
    cert-manager-webhook     1/1     Running   0          2m10s
    ```

3.  Run the following command to create a ClusterIssuer:

    ```
    cat <<EOF | kubectl apply -f -
    apiVersion: cert-manager.io/v1
    kind: ClusterIssuer
    metadata:
      name: letsencrypt-prod-http01
    spec:
      acme:
        server: https://acme-v02.api.letsencrypt.org/directory
        email: your_email_name@gmail.com  #Replace the value with your email address. 
        privateKeySecretRef:
          name: letsencrypt-http01
        solvers:
        - http01: 
            ingress:
              class: nginx
    EOF
    ```

4.  Run the following command to query the created ClusterIssuer:

    ```
    kubectl get clusterissuer
    ```

    Expected output:

    ```
    NAME                         READY   AGE
    letsencrypt-prod-http01      True    17s
    ```

5.  Run the following command to create an Ingress:

    ```
    cat <<EOF | kubectl apply -f -
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: ingress-tls
      annotations:
        kubernetes.io/ingress.class: "nginx"
        cert-manager.io/cluster-issuer: "letsencrypt-prod-http01"
    spec:
      tls:
      - hosts:
        -your_domain_name # Replace the value with your domain name. 
        secretName: ingress-tls   
      rules:
      -Host: your_domain_name # Replace the value with your domain name. 
        http:
          paths:
          - path: /
            backend:
              serviceName: your_service_name # Replace the value with the Service name. 
              servicePort: your_service_port # Replace the value with the Service port. 
    EOF
    ```

    **Note:** The domain name that you use to replace your\_domain\_name in the template must meet the following conditions:

    -   The domain name cannot exceed 64 characters in length.
    -   The domain name cannot be a wildcard domain name.
    -   The domain name is accessible from the Internet over HTTP.
6.  Run the following command to query the certificate:

    ```
    kubectl get cert
    ```

    Expected output:

    ```
    NAME          READY   SECRET     AGE
    ingress-tls   True    ingress-tls   52m
    ```

    **Note:** If the value in the **READY** field is not **True**, you can query the status of the certificate by running the `kubect describe cert ingress-tls` command.

7.  Run the following command to query the Secret of the certificate:

    ```
    kubectl get secret  ingress-tls
    ```

    Expected output:

    ```
    NAME          TYPE                DATA   AGE
    ingress-tls   kubernetes.io/tls   2      2m
    ```

8.  You can enter `https:[website domain name]` into the address bar of your browser to access the specified domain name.


