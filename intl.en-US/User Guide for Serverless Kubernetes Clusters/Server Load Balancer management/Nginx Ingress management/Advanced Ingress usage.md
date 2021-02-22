---
keyword: [create Ingresses, Ingresses for multiple domain names, secure Ingresses]
---

# Advanced Ingress usage

In Kubernetes clusters, an Ingress is a set of routing rules that authorize external access to cluster services. You can use an Ingress to enable Layer-7 load balancing. You can configure an Ingress to provide Kubernetes services with URLs, Server Load Balancing \(SLB\) instances, Secure Sockets Layer \(SSL\) connections, and name-based Web Hosting instances that provide external access to services. This topic describes how to create the following types of Ingress: a simple Ingress, a simple fanout Ingress that uses multiple domain names, a simple Ingress that uses the default domain name, and a secure Ingress.

To test a complex Ingress, an NGINX application that consists of multiple services is created in this example. You need to create an NGINX deployment and then create multiple services. Replace the following service names with the actual ones:

```
kubectl run nginx --image=registry.cn-hangzhou.aliyuncs.com/acs/netdia:latest

kubectl expose deploy nginx --name=http-svc --port=80 --target-port=80
kubectl expose deploy nginx --name=http-svc1 --port=80 --target-port=80
kubectl expose deploy nginx --name=http-svc2 --port=80 --target-port=80
kubectl expose deploy nginx --name=http-svc3 --port=80 --target-port=80
```

## Create a simple Ingress

Run the following command to create a simple Ingress. The Ingress redirects traffic that is destined for the /svc URI to an http-svc service. In the following code block, `nginx.ingress.kubernetes.io/rewrite-target: /` indicates that traffic destined for /svc is redirected to a URI in the / context. The URI can be recognized by the backend service.

1.  The following example shows the configuration of a simple Ingress:

    ```
    cat <<EOF | kubectl create -f -
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: simple
      annotations:
        nginx.ingress.kubernetes.io/rewrite-target: /$2
    spec:
      rules:
      - http:
          paths:
          - path: /svc(/|$)(.*)
            backend:
              serviceName: http-svc
              servicePort: 80
    EOF
    ```

    Run the following command to query the created Ingress.

    ```
    kubectl get ing
    ```

    ```
    NAME            HOSTS         ADDRESS          PORTS     AGE
    simple          *             101.37.XX.XX     80        11s
    ```

2.  Visit `http://101.37.XX.XX/svc` to access the NGINX service.


## Create a simple fanout Ingress that uses multiple domain names

You can create a simple fanout Ingress to route traffic from an external IP address to multiple services with different domain names. The following example shows the configuration of a simple fanout Ingress:

```
cat <<EOF | kubectl create -f - 
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: simple-fanout
spec:
  rules:
  - host: foo.bar.com
    http:
      paths:
      - path: /foo
        backend:
          serviceName: http-svc1
          servicePort: 80
      - path: /bar
        backend:
          serviceName: http-svc2
          servicePort: 80
  - host: foo.example.com
    http:
      paths:
      - path: /film
        backend:
          serviceName: http-svc3
          servicePort: 80    
EOF
```

```
kubectl get ing
```

```
NAME            HOSTS         ADDRESS          PORTS     AGE
simple-fanout   *             101.37.XX.XX     80        11s
```

After the preceding configuration is implemented, you can visit `http://foo.bar.com/foo` to access `http-svc1`, visit `http://foo.bar.com/bar` to access `http-svc2`, and visit `http://foo.example.com/film` to access `http-svc3`.

**Note:**

-   In a production environment, you must point the preceding domains to `101.37.XX.XX`.
-   In a test environment, you must point the preceding domains to `hosts` file:

    ```
    101.37.XX.XX foo.bar.com
    101.37.XX.XX foo.example.com
    ```


## Create a simple Ingress that uses the default domain name

If you do not have a service domain name, you can create a simple ingress that uses the default domain name provided by your cluster of Alibaba Cloud Container Service for Kubernetes \(ACK\). Then, you can use the default domain name to access the service. The default domain name is in the following format: `*.[cluster-id].[region-id].alicontainer.com`. You can find the default domain name in the basic information of the ACK cluster in the ACK console.

The following example shows the configuration of a simple Ingress that allows external access to the service through the default domain name:

```
cat <<EOF | kubectl create -f - 
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: shared-dns
spec:
  rules:
  - host: foo.[cluster-id].[region-id].alicontainer.com  ## Replace the value with the default domain name of the ACK cluster in which the services run.
     http:
      paths:
      - path: /
        backend:
          serviceName: http-svc1
          servicePort: 80
  - host: bar.[cluster-id].[region-id].alicontainer.com  ## Replace the value with the default domain name of the ACK cluster.
     http:
      paths:
      - path: /
        backend:
          serviceName: http-svc2
          servicePort: 80    
EOF
```

```
kubectl get ing
```

```
NAME            HOSTS         ADDRESS          PORTS     AGE
shared-dns   foo.[cluster-id].[region-id].alicontainer.com,bar.[cluster-id].[region-id].alicontainer.com             47.95.160.171   80        40m
```

After the Ingress is created, you can visit `http://foo.[cluster-id].[region-id].alicontainer.com/` to access `http-svc1` and visit `http://bar.[cluster-id].[region-id].alicontainer.com` to access `http-svc2`.

## Create a secure Ingress

ACK allows you to use multiple types of certificate to reinforce the security of your services.

1.  Prepare your certificate.

    If you do not have a certificate, perform the following steps to generate a test certificate:

    **Note:** The domain name must be the same as the one specified in your Ingress configuration.

    1.  Run the following command to generate a certificate file tls.crt and a private key file tls.key:

        ```
        openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=foo.bar.com/O=foo.bar.com"
        ```

    2.  Run the following command to create a Kubernetes Secret:

        Use the certificate and private key to create a Kubernetes Secret that is named foo.bar. You must refer to the Secret when you create the Ingress.

        ```
        kubectl create secret tls tls-test-ingress --key tls.key --cert tls.crt
        ```

2.  Create a secure Ingress.

    ```
    cat <<EOF | kubectl create -f - 
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: tls-fanout
    spec:
      tls:
      - hosts:
        - foo.bar.com
        secretName: foo.bar
      rules:
      - host: foo.bar.com
        http:
          paths:
          - path: /foo
            backend:
              serviceName: http-svc1
              servicePort: 80
          - path: /bar
            backend:
              serviceName: http-svc2
              servicePort: 80
    EOF
    ```

    ```
    kubectl get ing
    ```

    ```
    NAME            HOSTS         ADDRESS          PORTS     AGE
    tls-fanout      *             101.37.XX.XX     80        11s
    ```

3.  After the secure Ingress is created, you must point the domain name to the IP address of the service or add mapping rules to the `hosts` file. For more information, see [\#section\_2en\_rfc\_98m](#section_2en_rfc_98m).

    You can visit `http://foo.bar.com/foo` to access `http-svc1` and visit `http://foo.bar.com/bar` to access `http-svc2`.


## Configure mutual TLS authentication

HTTPS two-way authentication is required in some service scenarios. This feature is supported by Ingress-NGINX. This section describes how to configure mutual TLS authentication.

1.  Run the following command to create a self-signed certificate.

    ```
    openssl req -x509 -sha256 -newkey rsa:4096 -keyout ca.key -out ca.crt -days 356 -nodes -subj '/CN=Fern Cert Authority'
    ```

    The expected output:

    ```
    Generating a 4096 bit RSA private key
    ............................................................................................................. ++
    .....................................................................................++
    writing new private key to 'ca.key'
    ```

2.  Run the following command to create a server certificate:

    ```
    openssl req -new -newkey rsa:4096 -keyout server.key -out server.csr -nodes -subj '/CN=test.nginx.ingress.com'
    ```

    The expected output:

    ```
    Generating a 4096 bit RSA private key
    ................................................................................................................................ ++
    .................................................................++
    writing new private key to 'server.key'
    ```

    ```
    openssl x509 -req -sha256 -days 365 -in server.csr -CA ca.crt -CAkey ca.key -set_serial 01 -out server.crt
    ```

    The expected output:

    ```
    Signature ok
    subject=/CN=test.nginx.ingress.com
    Getting CA Private Key
    ```

3.  Run the following command to create a client certificate:

    ```
    openssl req -new -newkey rsa:4096 -keyout client.key -out client.csr -nodes -subj '/CN=Fern'
    ```

    The expected output:

    ```
    Generating a 4096 bit RSA private key
    ....................................................................................................................................................................................... ++
    ..............................................++
    writing new private key to 'client.key'
    -----
    ```

    ```
    openssl x509 -req -sha256 -days 365 -in client.csr -CA ca.crt -CAkey ca.key -set_serial 02 -out client.crt
    ```

    The expected output:

    ```
    Signature ok
    subject=/CN=Fern
    Getting CA Private Key
    ```

4.  Run the following command to query the created certificates:

    ```
    ls
    ```

    The expected output:

    ```
    ca.crt  ca.key  client.crt  client.csr  client.key  server.crt  server.csr  server.key
    ```

5.  Run the following command to create a Secret for the created CA certificate.

    ```
    kubectl create secret generic ca-secret --from-file=ca.crt=ca.crt
    ```

    The expected output:

    ```
    secret/ca-secret created
    ```

6.  Run the following command to create a Secret for the created server certificate:

    ```
    kubectl create secret generic tls-secret --from-file=tls.crt=server.crt --from-file=tls.key=server.key
    ```

    The expected output:

    ```
    secret/tls-secret created
    ```

7.  Use the following template to create a test Ingress:

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

    The expected output:

    ```
    ingress.networking.k8s.io/nginx-test configured
    ```

8.  Run the following command to obtain the IP address of the created Ingress:

    ```
    kubectl get ing
    ```

    The IP address in the ADDRESS column is the IP address of the created Ingress, as shown in the following output:

    ```
    NAME         HOSTS                    ADDRESS         PORTS     AGE
    nginx-test   test.nginx.ingress.com   39.102.xx.xx   80, 443   4h42m
    ```

9.  Run the following command to update the hosts file. Replace the value of IP with the obtained IP address.

    ```
    sudo echo "39.102.xx.xx  test.nginx.ingress.com" >> /etc/hosts
    ```

    **Verify the configuration**

    -   The client does not send the client certificate when it accesses the server.

        ```
        curl --cacert ./ca.crt  https://test.nginx.ingress.com
        ```

        The expected output:

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

    -   The client sends the client certificate when it accesses the server.

        ```
        curl --cacert ./ca.crt --cert ./client.crt --key ./client.key https://test.nginx.ingress.com
        ```

        The expected output:

        ```
        <! DOCTYPE html>
        <html>
        <head>
        <title>Welcome to nginx! </title>
        <style>
            body {
                width: 35em;
                margin: 0 auto;
                font-family: Tahoma, Verdana, Arial, sans-serif;
            }
        </style>
        </head>
        <body>
        <h1>Welcome to nginx! </h1>
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


## Deploy Ingresses on Kubernetes Dashboard

1.  Create an `nginx-ingress.yml` file and copy the following content into the file:

    ```
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: simple
    spec:
      rules:
      - http:
          paths:
          - path: /svc
            backend:
              serviceName: http-svc
              servicePort: 80
    ```

2.  Log on to the [ACK console](https://cs.console.aliyun.com).

3.  In the left-side navigation pane of the ACK console, click **Clusters**.

4.  On the Clusters page, select **Dashboard** from the **More** drop-down list in the **Actions** column. The Kubernetes Dashboard page appears.

5.  Click **CREATE** in the upper-right corner of the page.

6.  Click the **CREATE FROM FILE** tab. Select the `nginx-ingress.yml` file.

7.  Click **UPLOAD**.

    Then, an Ingress that routes Layer-7 traffic to the `http-svc` service is created.

8.  In the left-side navigation pane, choose **Ingresses and Load Balancing** \> **Ingresses** to view the newly created Ingress and the domain name.

9.  You can use your browser to access the `http-svc` service through this domain name.


## Use regular expressions to specify domain names in Ingresses

In Kubernetes clusters, Ingresses do not support domain names that are specified in regular expressions. However, you can add the `nginx.ingress.kubernetes.io/server-alias` annotation to enable Ingresses to support regular expressions.

1.  Run the following command to create a test Ingress. The `~^www\.\d+\.example\.com` regular expression is used in this example.

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

2.  Run the following command to verify that the configuration takes effect. The output shows that the domain name is specified in regular expression, as shown in the server\_name field:

    ```
    kubectl get pods -n kube-system | grep ingress
    ```

    The expected output:

    ```
    nginx-ingress-controller-77cd987c4c-cp87g         1/1     Running   0          1h
    nginx-ingress-controller-77cd987c4c-xvpt5         1/1     Running   0          1h
    ```

    ```
    kubectl exec -n kube-system nginx-ingress-controller-77cd987c4c-cp87g cat /etc/nginx/nginx.conf | grep -C3 "regex.ingress.test.com"
    ```

    The expected output:

    ```
    }
      ## end server _
    
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
    
      # backend for when default-backend-service is not configured or it does not have endpoints
      server {
    ```

3.  Run the following command to obtain the IP address to which the Ingress routes traffic:

    ```
    kubectl get ing
    ```

    The expected output:

    ```
    NAME            HOSTS         ADDRESS          PORTS     AGE
    ingress-regex   foo.bar.com   101.37.XX.XX     80        11s
    ```

4.  Run the following curl command to test access to the service. Replace the value of IP with the actual IP address.

    ```
    curl -H "Host: foo.bar.com" $IP/foo
    ```

    The expected output:

    ```
    /foo
    ```

    ```
    curl -H "Host: www.123.example.com" $IP/foo
    ```

    The expected output:

    ```
    /foo
    ```

    ```
    curl -H "Host: www.321.example.com" $IP/foo
    ```

    The expected output:

    ```
    /foo
    ```


