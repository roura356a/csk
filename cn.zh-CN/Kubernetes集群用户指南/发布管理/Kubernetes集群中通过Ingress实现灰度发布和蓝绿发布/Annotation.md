# Annotation

本文介绍阿里云容器服务的Ingress功能实现灰度发布使用的Annotation。

阿里云容器服务Kubernetes的Ingress功能通过Annotation：路由规则`nginx.ingress.kubernetes.io/service-match`及服务权重`nginx.ingress.kubernetes.io/service-weight`来支持应用服务的灰度发布。

**说明：**

如果用户同时配置了：路由规则`nginx.ingress.kubernetes.io/service-match`及服务权重`nginx.ingress.kubernetes.io/service-weight`，当收到一个请求时，先判断是否满足已配置的路由规则`nginx.ingress.kubernetes.io/service-match`：

-   若不满足，则会将请求转发到老版本中。
-   若满足，则会按照配置的服务权重`nginx.ingress.kubernetes.io/service-weight`转发请求。

## 路由规则`nginx.ingress.kubernetes.io/service-match`

该Annotation用来配置新版本服务的路由匹配规则，格式如下：

```
nginx.ingress.kubernetes.io/service-match: | 
    <service-name>: <match-rule>
```

**参数解释**

service-name：服务名称，满足match-rule的请求会被路由到该服务中。

match-rule：路由匹配规则。

-   匹配类型：
    -   `header`：基于请求头，支持正则匹配和完整匹配。
    -   `cookie`：基于cookie，支持正则匹配和完整匹配。
    -   `query`：基于请求参数，支持正则匹配和完整匹配。
-   匹配方式：
    -   正则匹配格式：`/{regular expression}/`
    -   完整匹配格式：`"{exact expression}"`

**配置示例**

```
# 请求头中满足foo正则匹配^bar$的请求被转发到新版本服务new-nginx中
new-nginx: header("foo", /^bar$/)

# 请求头中满足foo完整匹配bar的请求被转发到新版本服务new-nginx中
new-nginx: header("foo", "bar")

# cookie中满足foo正则匹配^sticky-.+$的请求被转发到新版本服务new-nginx中
new-nginx: cookie("foo", /^sticky-.+$/)

# query param中满足foo完整匹配bar的请求被转发到新版本服务new-nginx中
new-nginx: query("foo", "bar")
```

## 服务权重`nginx.ingress.kubernetes.io/service-weight`

该Annotation用来配置新老版本服务的流量权重，格式如下：

```
nginx.ingress.kubernetes.io/service-weight: | 
    <new-svc-name>:<new-svc-weight>, <old-svc-name>:<old-svc-weight>
```

**参数解释**

new-svc-name：新版本服务名称。

new-svc-weight：新版本服务权重。

old-svc-name：老版本服务名称。

old-svc-weight：老版本服务权重。

**配置示例**

```
nginx.ingress.kubernetes.io/service-weight: | 
    new-nginx: 20, old-nginx: 60
```

**说明：**

-   服务权重采用相对值计算方式。如配置示例中的新版服务权重设置为20，旧版服务权重设置为60，则：新版服务的权重百分比为25%，旧版服务的权重百分比为75%。
-   一个服务组（同一个Ingress yaml中具有相同Host和Path的服务）中未明确设置权重的服务默认权重值为100。

