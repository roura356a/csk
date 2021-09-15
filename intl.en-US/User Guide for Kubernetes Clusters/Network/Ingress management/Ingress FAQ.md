# Ingress FAQ

This topic provides answers to some frequently asked questions about Ingresses.

-   [Which Secure Sockets Layer \(SSL\) or Transport Layer Security \(TLS\) protocol versions are supported by Ingresses?](#section_gsi_4n0_lsd)
-   [Do Ingresses pass Layer 7 request headers?](#section_i3e_5lj_47y)
-   [Can ingress-nginx forward requests to HTTPS Services at the backend?](#section_ghy_7bv_ute)
-   [Do Ingresses pass client IP addresses at Layer 7?](#section_ril_3ys_j8u)
-   [Does nginx-ingress-controller support HTTP Strict Transport Security \(HSTS\)?](#section_ycn_qs2_rb2)
-   [Which rewrite rules are supported by ingress-nginx?](#section_l15_amm_2ht)
-   [How do I fix the issue that Log Service cannot parse logs as expected after ingress-nginx-controller is upgraded?](#section_10k_mmd_5e0)
-   [t1860692.dita\#task\_2401024](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Configure an Ingress controller to use an internal-facing SLB instance.md)
-   [What are the system updates after I upgrade the NGINX Ingress controller on the Add-ons page in the console?](#section_99r_gia_5ax)
-   [How do I change Layer 4 listeners to Layer 7 listeners for ingress-nginx?](#section_ian_qic_135)
-   [How do I configure ingress-nginx to use an existing SLB instance?](#section_pat_gf4_ozj)
-   [Why does the "certificate signed by unknown authority" error occur when I create an Ingress?](#section_4fn_bfn_vgw)

## Which Secure Sockets Layer \(SSL\) or Transport Layer Security \(TLS\) protocol versions are supported by Ingresses?

By default, ingress-nginx supports only TLS 1.2. If the TLS protocol version that is used by a browser or mobile client is earlier than TLS 1.2, errors may occur during handshakes between the client and ingress-nginx.

If you want ingress-nginx to support more TLS protocol versions, run the following commands to add required configurations to the `nginx-configuration` ConfigMap in the kube-system namespace. For more information, see [TLS/HTTPS](https://kubernetes.github.io/ingress-nginx/user-guide/tls/#default-tls-version-and-ciphers).

```
ssl-ciphers: "ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA:ECDHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES256-SHA256:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:DES-CBC3-SHA"
ssl-protocols: "TLSv1 TLSv1.1 TLSv1.2 TLSv1.3"
```

## Do Ingresses pass Layer 7 request headers?

By default, ingress-nginx passes Layer 7 request headers. However, request headers that do not conform to HTTP rules are filtered out before requests are forwarded to the backend servers. For example, the Mobile Version request header is filtered out. To retain these request headers, run the `kubectl edit cm -n kube-system nginx-configuration` command to add the following line to the configuration of the nginx-configuration ConfigMap. For more information, see [ConfigMap](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/#enable-underscores-in-headers).

```
enable-underscores-in-headers: true
```

## Can ingress-nginx forward requests to HTTPS Services at the backend?

To enable ingress-nginx to forward requests to HTTPS Services at the backend, run the following command to add the required annotations to the Ingress configuration:

```
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: xxxx
  annotations:
    # Note: You must set the backend protocol to HTTPS. 
    nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"
```

## Do Ingresses pass client IP addresses at Layer 7?

By default, ingress-nginx adds the X-Forward-For and X-Real-IP header fields to carry client IP addresses. However, if the X-Forward-For and X-Real-IP header fields are already added to the request by a client, the backend server cannot obtain the client IP address.

Add the following lines to the configuration of the `nginx-configuration` ConfigMap in the kube-system namespace. This allows ingress-nginx to pass client IP addresses at Layer 7.

```
compute-full-forwarded-for: "true"
forwarded-for-header: "X-Forwarded-For"
use-forwarded-headers: "true"
```

For more information, see [Configure an ACK Ingress to pass client IP addresses](https://developer.aliyun.com/article/609162).

## Does nginx-ingress-controller support HTTP Strict Transport Security \(HSTS\)?

By default, nginx-ingress-controller is enabled for nginx-ingress-controller. When a browser sends a plain HTTP request for the first time, the response header from the backend server \(with HSTS enabled\) contains `Non-Authoritative-Reason: HSTS`. This indicates that the backend server supports HSTS. If the client also supports HSTS, the client directly sends HTTPS requests after the first successful access. The response body from the backend server contains the status code `307 Internal Redirect`, as shown in the following figure.

![1](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9511674161/p202703.png)

If you do not want the client requests to be forwarded to HTTPS Services at the backend, you can disable HSTS for nginx-ingress-controller. For more information, see [HSTS](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/#hsts).

**Note:** By default, the HSTS configuration is cached by browsers. You must manually delete the browser cache after you disable HSTS for nginx-ingress-controller.

## Which rewrite rules are supported by ingress-nginx?

ingress-nginx supports only simple rewrite rules. For more information, see [Rewrite](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/#rewrite). If you want to configure complex rewrite rules, use the following methods:

-   configuration-snippet: Add this annotation to the location configuration of an Ingress. For more information, see [Configuration snippet](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/#configuration-snippet).
-   server-snippet: Add this annotation to the server configuration of an Ingress. For more information, see [Server snippet](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/#server-snippet).

You can use other snippets to add global configurations, as shown in the following figure. For more information, see [main-snippet](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/#main-snippet).

![2](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9511674161/p202783.png)

## How do I fix the issue that Log Service cannot parse logs as expected after ingress-nginx-controller is upgraded?

The ingress-nginx-controller component has two commonly used versions: ingress-nginx-controller 0.20 and 0.30. The default log format of ingress-nginx-controller 0.20 is different from that of ingress-nginx-controller 0.30. Therefore, after you upgrade ingress-nginx-controller from 0.20 to 0.30 on the **Add-ons** page in the console, the Ingress dashboard may not show the actual statistics of requests to the backend servers when you perform canary releases or blue-green releases with an Ingress.

To fix the issue, perform the following steps to update the `nginx-configuration` ConfigMap and the configuration of `k8s-nginx-ingress`:

1.  Update the `nginx-configuration` ConfigMap.

    -   If you never modified the `nginx-configuration` ConfigMap before, copy the following content to a file named `nginx-configuration.yaml` and run the `kubectl apply -f nginx-configuration.yaml` command to deploy the file.

        ```
        apiVersion: v1
        kind: ConfigMap
        data:
          allow-backend-server-header: "true"
          enable-underscores-in-headers: "true"
          generate-request-id: "true"
          ignore-invalid-headers: "true"
          log-format-upstream: $remote_addr - [$remote_addr] - $remote_user [$time_local] "$request" $status $body_bytes_sent "$http_referer" "$http_user_agent" $request_length $request_time [$proxy_upstream_name] $upstream_addr $upstream_response_length $upstream_response_time $upstream_status $req_id $host [$proxy_alternative_upstream_name]
          max-worker-connections: "65536"
          proxy-body-size: 20m
          proxy-connect-timeout: "10"
          reuse-port: "true"
          server-tokens: "false"
          ssl-redirect: "false"
          upstream-keepalive-timeout: "900"
          worker-cpu-affinity: auto
        metadata:
          labels:
            app: ingress-nginx
          name: nginx-configuration
          namespace: kube-system
        ```

    -   If you modified the `nginx-configuration` ConfigMap before, run the following command to update the configuration. This ensures that your previous modifications are not overwritten.

        ```
        kubectl edit configmap nginx-configuration -n kube-system
        ```

    Add `[$proxy_alternative_upstream_name]` to the end of the `log-format-upstream` field, save the changes, and then exit.

2.  Update the configuration of `k8s-nginx-ingress`.

    Copy the following content to a file named `k8s-nginx-ingress.yaml` and run the `kubectl apply -f k8s-nginx-ingress.yaml` command to deploy the file.

    ```
    apiVersion: log.alibabacloud.com/v1alpha1
    kind: AliyunLogConfig
    metadata:
        namespace: kube-system
      # your config name, must be unique in you k8s cluster
      name: k8s-nginx-ingress
    spec:
      # logstore name to upload log
      logstore: nginx-ingress
      # product code, only for k8s nginx ingress
      productCode: k8s-nginx-ingress
      # logtail config detail
      logtailConfig:
        inputType: plugin
        # logtail config name, should be same with [metadata.name]
        configName: k8s-nginx-ingress
        inputDetail:
          plugin:
            inputs:
            - type: service_docker_stdout
              detail:
                IncludeLabel:
                  io.kubernetes.container.name: nginx-ingress-controller
                Stderr: false
                Stdout: true
            processors:
            - type: processor_regex
              detail:
                KeepSource: false
                Keys:
                - client_ip
                - x_forward_for
                - remote_user
                - time
                - method
                - url
                - version
                - status
                - body_bytes_sent
                - http_referer
                - http_user_agent
                - request_length
                - request_time
                - proxy_upstream_name
                - upstream_addr
                - upstream_response_length
                - upstream_response_time
                - upstream_status
                - req_id
                - host
                - proxy_alternative_upstream_name
                NoKeyError: true
                NoMatchError: true
                Regex: ^(\S+)\s-\s\[([^]]+)]\s-\s(\S+)\s\[(\S+)\s\S+\s"(\w+)\s(\S+)\s([^"]+)"\s(\d+)\s(\d+)\s"([^"]*)"\s"([^"]*)"\s(\S+)\s(\S+)+\s\[([^]]*)]\s(\S+)\s(\S+)\s(\S+)\s(\S+)\s(\S+)\s*(\S*)\s*\[*([^]]*)\]*.*
                SourceKey: content
    ```


## What are the system updates after I upgrade the NGINX Ingress controller on the Add-ons page in the console?

If the version of the NGINX Ingress controller is earlier than 0.44, the component includes the following resources:

-   serviceaccount/ingress-nginx
-   configmap/nginx-configuration
-   configmap/tcp-service
-   configmap/udp-services
-   clusterrole.rbac.authorization.k8s.io/ingress-nginx
-   clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx
-   role.rbac.authorization.k8s.io/ingress-nginx
-   rolebinding.rbac.authorization.k8s.io/ingress-nginx
-   service/nginx-ingress-lb
-   deployment.apps/nginx-ingress-controller

If the version of the NGINX Ingress controller is 0.44 or later, the component includes the following resources in addition to the previously mentioned resources:

-   validatingwebhookconfiguration.admissionregistration.k8s.io/ingress-nginx-admission
-   service/ingress-nginx-controller-admission
-   serviceaccount/ingress-nginx-admission
-   clusterrole.rbac.authorization.k8s.io/ingress-nginx-admission
-   clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx-admission
-   role.rbac.authorization.k8s.io/ingress-nginx-admission
-   rolebinding.rbac.authorization.k8s.io/ingress-nginx-admission
-   job.batch/ingress-nginx-admission-create
-   job.batch/ingress-nginx-admission-patch

When you upgrade the NGINX Ingress controller on the Add-ons page in the console, the configurations of the following resources are unchanged:

-   configmap/nginx-configuration
-   configmap/tcp-services
-   configmap/udp-services
-   service/nginx-ingress-lb

The configurations of other resources are overwritten to default values. For example, the default value of the replicas parameter of `deployment.apps/nginx-ingress-controller` is 2. If you set the replicas parameter to 5 before you upgrade the NGINX Ingress controller, the replicas parameter is set to the default value 2 after you upgrade the component on the Add-ons page.

## How do I change Layer 4 listeners to Layer 7 listeners for ingress-nginx?

By default, the server load balancer \(SLB\) instance of ingress-nginx listens on TCP ports 443 and 80 to check for requests at Layer 4. You can create an SLB instance and configure HTTPS or HTTP listeners to check for requests at Layer 7.

**Note:** Your service will be temporarily interrupted when you change listeners. We recommend that you change listeners during off-peak hours.

1.  Create a certificate and record cert-id. For more information, see [Use a certificate from Alibaba Cloud SSL Certificates Service](/intl.en-US/Classic Load Balancer/User Guide/Certificate management/Create a certificate/Use a certificate from Alibaba Cloud SSL Certificates Service.md).

2.  Use annotations to change Layer 4 listeners to Layer 7 listeners for ingress-nginx.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  In the left-side navigation pane of the details page, choose **Network** \> **Services**.

    5.  At the top of the **Services** page, set **Namespace** to kube-system, select the ingress-nginx-lb Service, and click **View in YAML** in the **Actions** column.

    6.  In the Edit YAML dialog box, add the following lines to the annotations parameter and click **Update**.

        ```
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-protocol-port: "http:80,https:443"
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-cert-id: "${YOUR_CERT_ID}"
        ```

3.  Verify that Layer 4 listeners are updated to Layer 7 listeners

    1.  On the **Services** page, select the ingress-nginx-lb Service and click **Monitoring information** in the Type column.

    2.  On the **Listener** tab, you can find that HTTP:80 and HTTPS:443 are displayed in the **Frontend Protocol** column. This indicates that Layer 4 listeners are updated to Layer 7 listeners.

        ![Listener](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2580761361/p305337.png)


## How do I configure ingress-nginx to use an existing SLB instance?

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the **App Catalog** page, search for ack-ingress-nginx and click ack-ingress-nginx in the search result.

4.  In the **Deploy** panel, select **Cluster** and **Namespace**.

5.  Click the **Parameters** tab and set the parameters.

    1.  Delete the lines in the annotations parameter of the service section.

        ![ingress-nginx](../images/p306175.png)

    2.  Add the following line to the annotations parameter of the service section:

        ```
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id: "${YOUR_LOADBALACER_ID}
        ```

6.  In the **Deploy** panel, click **Create**.


## Why does the "certificate signed by unknown authority" error occur when I create an Ingress?

![Ingress](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2580761361/p311787.png)

If multiple Ingresses are deployed in the cluster and the Ingresses use the same resources, such as Secrets, Services, or webhook configurations, the preceding error occurs because different SSL certificates are used to communicate with backend servers when webhooks are triggered.

Solution: Redeploy two Ingresses and make sure that the Ingresses use different resources. For more information about the resources used by Ingresses, see [What are the system updates after I upgrade the NGINX Ingress controller on the Add-ons page in the console?](#section_99r_gia_5ax).

