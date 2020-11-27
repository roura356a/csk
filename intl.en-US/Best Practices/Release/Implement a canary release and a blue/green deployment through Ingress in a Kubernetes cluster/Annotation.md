# Annotation

This topic describes the annotations that are used to perform a canary release through the Ingress provided by Container Service for Kubernetes \(ACK\).

You can use the Ingress feature provided by ACK to perform a canary release for your applications by adding annotation `nginx.ingress.kubernetes.io/service-match` to specify Ingress rules and annotation `nginx.ingress.kubernetes.io/service-weight` to specify Service weights.

**Note:**

If you add both `nginx.ingress.kubernetes.io/service-match` and `nginx.ingress.kubernetes.io/service-weight`, ACK checks whether a request matches the specified Ingress rule `nginx.ingress.kubernetes.io/service-match` before the request is forwarded.

-   If the request does not match the Ingress rule, it is forwarded to the Service of the earlier version.
-   If the request matches the Ingress rule, it is forwarded based on the Service weight specified by `nginx.ingress.kubernetes.io/service-weight`.

## Annotation `nginx.ingress.kubernetes.io/service-match` for Ingress rules

This annotation is used to specify Ingress rules that route traffic to the Service of the latest version. You must specify the annotation in the following format:

```
nginx.ingress.kubernetes.io/service-match: | 
    <service-name>: <match-rule>
```

**Parameter descriptions**

service-name: the name of the Service. Requests that match the rules specified by match-rule are forwarded to the Service.

match-rule: the match rules for requests.

-   Match types:
    -   `header`: based on the request header. Regular expressions and exact match are supported.
    -   `cookie`: based on the cookie. Regular expressions and exact match are supported.
    -   `query`: based on the query string. Regular expressions and exact match are supported.
-   Match methods:
    -   Regular expression: `/{regular expression}/`
    -   Exact match: `"{exact expression}"`

**Examples**

```
# If the request header of a request matches exact match rule foo and regular expression ^bar$, the request is forwarded to the new-nginx Service.
new-nginx: header("foo", /^bar$/)

# If the request header of a request matches exact match rules foo and bar, the request is forwarded to the new-nginx Service.
new-nginx: header("foo", "bar")

# If the cookie of a request matches exact match rule foo and regular expression ^sticky-.+$, the request is forwarded to the new-nginx Service.
new-nginx: cookie("foo", /^sticky-.+$/)

# If the query string of a request matches exact match rules foo and bar, the request is forwarded to the new-nginx Service.
new-nginx: query("foo", "bar")
```

## Annotation `nginx.ingress.kubernetes.io/service-weight` for Service weights

This annotation is used to specify traffic weights for Services of different versions.

```
nginx.ingress.kubernetes.io/service-weight: | 
    <new-svc-name>:<new-svc-weight>, <old-svc-name>:<old-svc-weight>
```

**Parameter descriptions**

new-svc-name: the name of the Service of the latest version.

new-svc-weight: the traffic weight for the Service of the latest version.

old-svc-name: the name of the Service of the earlier version.

old-svc-weight: the traffic weight for the Service of the earlier version.

**Examples**

```
nginx.ingress.kubernetes.io/service-weight: | 
    new-nginx: 20, old-nginx: 60
```

**Note:**

-   The weight for each Service is a percentage value. In this example, the traffic weight for the Service of the latest version is 20 and the traffic weight for the Service of the earlier version is 60. This indicates that 25% of the requests that match the Ingress rule are forwarded to the Service of the latest version and the remaining 75% is forwarded to the Service of the earlier version.
-   If you do not set traffic weights for Services in the Service group of an Ingress rule, the default weight for each Service is 100.

