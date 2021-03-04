# Ingress FAQ

This topic provides answers to some frequently asked questions about Ingresses.

-   [Which Secure Sockets Layer \(SSL\) or Transport Layer Security \(TLS\) protocol versions are supported by Ingresses?](#section_gsi_4n0_lsd)
-   [Does Ingress pass Layer 7 request headers?](#section_i3e_5lj_47y)
-   [Can ingress-nginx forward requests to HTTPS Services at the backend?](#section_ghy_7bv_ute)
-   [Does Ingress pass client IP addresses at Layer 7?](#section_ril_3ys_j8u)
-   [Does nginx-ingress-controller support HTTP Strict Transport Security \(HSTS\)?](#section_ycn_qs2_rb2)
-   [Which rewrite rules are supported by ingress-nginx?](#section_l15_amm_2ht)
-   [How do I fix the issue that Log Service cannot parse logs as normal after ingress-nginx-controller is upgraded?](#section_10k_mmd_5e0)

## Which Secure Sockets Layer \(SSL\) or Transport Layer Security \(TLS\) protocol versions are supported by Ingresses?

By default, ingress-nginx supports only TLS 1.2. If the TLS protocol version that is used by a browser or mobile client is earlier than TLS 1.2, errors may occur during handshakes between the client and ingress-nginx.

If you want ingress-nginx to support more TLS protocol versions, run the following commands to add required configurations to the `nginx-configuration` ConfigMap in the kube-system namespace. For more information, see [TLS/HTTPS](https://kubernetes.github.io/ingress-nginx/user-guide/tls/#default-tls-version-and-ciphers).

```
ssl-ciphers: "ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA:ECDHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES256-SHA256:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:DES-CBC3-SHA"
ssl-protocols: "TLSv1 TLSv1.1 TLSv1.2 TLSv1.3"
```

## Does Ingress pass Layer 7 request headers?

By default, ingress-nginx passes Layer 7 request headers. However, request headers that do not conform to HTTP rules are filtered out before they are forwarded to the backend Services. For example, the Mobile Version request header is filtered out. If you do not want to filter out these request headers, run the following command to add the required configurations to the nginx-configuration ConfigMap. For more information, see [ConfigMap](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/#enable-underscores-in-headers).

```
enable-underscores-in-headers: true
```

## Can ingress-nginx forward requests to HTTPS Services at the backend?

To enable ingress-nginx to forward requests to HTTPS Services at the backend, run the following command to add the required annotations to the Ingress configuration:

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: xxxx
  annotations:
    # Note: You must specify the backend protocol as HTTPS to enable ingress-nginx to forward requests to HTTPS Services at the backend.
    nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"
```

## Does Ingress pass client IP addresses at Layer 7?

By default, ingress-nginx adds the X-Forward-For and X-Real-IP header fields to carry client IP address. However, if the X-Forward-For and X-Real-IP header fields are already added to the request by a client, the backend server cannot obtain the client IP address.

Run the following command to modify the `nginx-configuration` ConfigMap in the kube-system namespace. This allows ingress-nginx to pass client IP addresses at Layer 7.

```
compute-full-forwarded-for: "true"
forwarded-for-header: "X-Forwarded-For"
use-forwarded-headers: "true"
```

For more information, see [Configure an ACK Ingress to pass through client IP addresses](https://developer.aliyun.com/article/609162).

## Does nginx-ingress-controller support HTTP Strict Transport Security \(HSTS\)?

By default, HSTS is enabled for nginx-ingress-controller. When a browser sends a plain HTTP request for the first time, the response header from the backend server \(with HSTS enabled\) contains `Non-Authoritative-Reason: HSTS`. This indicates that the backend server supports HSTS. If the client also supports HSTS, the client will continue to send HTTPS requests after the first access attempt succeeds. The response body from the backend server contains the status code `307 Internal Redirect`, as shown in the following figure.

![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9511674161/p202703.png)

If you do not want the client requests to be forwarded to HTTPS Services at the backend, you can disable HSTS for nginx-ingress-controller. For more information, see [HSTS](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/#hsts).

**Note:** By default, the HSTS configuration is cached by browsers. You must manually delete the browser cache after you disable HSTS for nginx-ingress-controller.

## Which rewrite rules are supported by ingress-nginx?

Only simple rewrite rules are supported by ingress-nginx. For more information, see [Rewrite](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/#rewrite). If you want to configure complex rewrite rules, use the following methods:

-   configuration-snippet: Add this annotation to the location configuration of an Ingress. For more information, see [Configuration snippet](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/#configuration-snippet).
-   server-snippet: Add this annotation to the server configuration of an Ingress. For more information, see [Server snippet](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/#server-snippet).

You can use other code snippets to add global configurations, as shown in the following figure. For more information, see [main-snippet](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/#main-snippet).

![2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9511674161/p202783.png)

## How do I fix the issue that Log Service cannot parse logs as normal after ingress-nginx-controller is upgraded?

The ingress-nginx-controller component has two commonly used versions: ingress-nginx-controller 0.20 and 0.30. The default log format of ingress-nginx-controller 0.20 is different from that of ingress-nginx-controller 0.30. Therefore, after you upgrade from ingress-nginx-controller 0.20 to 0.30 on the **Add-ons** page in the console, the Ingress dashboard may not show the actual access statistics to the backend Services when you perform canary releases or blue-green releases for an Ingress.

To fix the issue, perform the following steps to update the `nginx-configuration` ConfigMap and the configuration of `k8s-nginx-ingress`.

1.  Update the `nginx-configuration` ConfigMap.

    -   If you have not modified the `nginx-configuration` ConfigMap, copy the following content to a file named `nginx-configuration.yaml` and run the `kubectl apply -f nginx-configuration.yaml` command to deploy the file.

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

    -   If you have modified the `nginx-configuration` ConfigMap, run the following command to update the configuration. This ensures that your previous modifications are not overwritten.

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
                Regex: ^(\S+)\s-\s\[([^]]+)]\s-\s(\S+)\s\[(\S+)\s\S+\s"(\w+)\s(\S+)\s([^"]+)"\s(\d+)\s(\d+)\s"([^"]*)"\s"([^"]*)"\s(\S+)\s(\S+)+\s\[([^]]*)]\s(\S+)\s(\S+)\s(\S+)\s(\S+)\s(\S+)\s*(\S*)\s*\[*([^]]*)\]*. *
                SourceKey: content
    ```


