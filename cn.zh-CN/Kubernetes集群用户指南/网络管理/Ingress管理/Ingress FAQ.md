# Ingress FAQ

本文主要为您介绍Ingress常见问题的处理方法。

-   [Ingress支持哪些SSL/TLS版本？](#section_gsi_4n0_lsd)
-   [Ingress L7请求头默认是透传的吗？](#section_i3e_5lj_47y)
-   [后端服务为HTTPS服务访问时是否可以通过Ingress-Nginx转发？](#section_ghy_7bv_ute)
-   [Ingress L7透传客户端IP吗？](#section_ril_3ys_j8u)
-   [nginx-ingress-controller组件支持HSTS吗？](#section_ycn_qs2_rb2)
-   [Ingress-Nginx支持哪些Rewrite配置？](#section_l15_amm_2ht)
-   [当版本升级后SLS解析日志不正常怎样修复？](#section_10k_mmd_5e0)

## Ingress支持哪些SSL/TLS版本？

Ingress-Nginx默认仅支持TLS V1.2版本，对于部分旧版本的浏览器，或者移动客户端TLS版本低于1.2时，会导致客户端在与Ingress-Nginx服务SSL版本协商时报错。

执行以下命令，修改`kube-system/nginx-configuration configmap`添加以下配置，为Ingress-Nginx开启支持更多TLS版本的功能。具体操作，请参见[TLS/HTTPS](https://kubernetes.github.io/ingress-nginx/user-guide/tls/#default-tls-version-and-ciphers)。

```
ssl-ciphers: "ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA:ECDHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES256-SHA256:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:DES-CBC3-SHA"
ssl-protocols: "TLSv1 TLSv1.1 TLSv1.2 TLSv1.3"
```

## Ingress L7请求头默认是透传的吗？

Ingress-Nginx默认透传客户端的请求头，有些不符合HTTP规则的请求头（比如Mobile Version），在转发到后端服务前会被过滤掉。为了不过滤掉这类请求头，您可以执行以下命令在ConfigMap中添加配置。更多信息，请参见[ConfigMap](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/#enable-underscores-in-headers)。

```
enable-underscores-in-headers：true
```

## 后端服务为HTTPS服务访问时是否可以通过Ingress-Nginx转发？

对于后端业务是HTTPS服务，但同样希望可以通过Ingress-Nginx转发时，执行以下命令，在Ingress资源配置中添加以下注解即可。

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: xxxx
  annotations:
    # 注意这里：必须指定后端服务为HTTPS服务。
    nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"
```

## Ingress L7透传客户端IP吗？

Ingress-Nginx默认会通过X-Forward-For和X-Real-IP来透传客户端IP，但是当客户端主动在请求头里指定了X-Forward-For和X-Real-IP，会导致服务端获取不到真实的客户端IP。

执行以下命令，开启`kube-system/nginx-configuration configmap`的以下配置可实现Ingress L7透传客户端IP。

```
compute-full-forwarded-for: "true"
forwarded-for-header: "X-Forwarded-For"
use-forwarded-headers: "true"
```

更多相关信息，请参见[阿里云容器服务Ingress设置原IP透传](https://developer.aliyun.com/article/609162)。

## nginx-ingress-controller组件支持HSTS吗？

nginx-ingress-controller组件默认是开启HSTS的，有些浏览器第一次基于PLAIN HTTP访问时，服务端（开启HSTS）会在返回给客户端的响应头里携带`Non-Authoritative-Reason: HSTS`字段，说明服务端支持HSTS，当客户端也支持的情况下下次会直接以HTTPS方式访问服务端。服务端返回的响应头消息体中包含有`307 Internal Redirect`状态码，具体如下图所示。

![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4244178061/p202703.png)

当客户端不希望支持自动转到HTTPS访问服务端时，您可以关闭nginx-ingress-controller组件的HSTS。具体操作，请参见[HSTS](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/#hsts)。

**说明：** 在浏览器端HSTS默认是有缓存的，当关闭nginx-ingress-controller组件的HSTS后，请您清理缓存。

## Ingress-Nginx支持哪些Rewrite配置？

目前Ingress-Nginx支持一些简单的Rewrite配置，具体请参见[Rewrite](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/#rewrite)。但是，对于一些高级的特别的Rewrite需求，您可以通过下面方式来配置。

-   configuration-snippet：请参见[Configuration snippet](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/#configuration-snippet)，扩展一些配置到Location章节中。
-   server-snippet：请参见[Server snippet](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/#server-snippet)，扩展一些配置到Server章节中。

同时，snippet也支持一些全局配置，具体如下图所示。更多相关信息，请参见[main-snippet](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/#main-snippet)。

![2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4244178061/p202783.png)

## 当版本升级后SLS解析日志不正常怎样修复？

ingress-nginx-controller组件当前主要有0.20和0.30两个版本，当通过控制台的**组件管理**从0.20升级到0.30版本后 ，由于0.20和0.30默认输出格式不同，在使用Ingress的灰度或蓝绿发布功能时，Ingress Dashboard会出现无法正确展示实际后端服务访问的情况。

执行以下操作步骤进行修复，更新`nginx-configuration configmap`和`k8s-nginx-ingress`配置。

1.  更新`nginx-configuration configmap`。

    -   如果您没有修改过`nginx-configuration configmap`，保存以下内容为`nginx-configuration.yaml`, 然后执行`kubectl apply -f nginx-configuration.yaml`命令进行部署。

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

    -   如果您修改过`nginx-configuration configmap`，为了避免覆盖您的配置，执行以下命令进行修复：

        ```
        kubectl edit configmap nginx-configuration -n kube-system
        ```

    在`log-format-upstream`字段末尾，添加`[$proxy_alternative_upstream_name]`， 然后保存退出。

2.  更新`k8s-nginx-ingress`配置。

    将以下内容保存为`k8s-nginx-ingress.yaml`文件，然后执行`kubectl apply -f k8s-nginx-ingress.yaml`命令进行部署。

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


