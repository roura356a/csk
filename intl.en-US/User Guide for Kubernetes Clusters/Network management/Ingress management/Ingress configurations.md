# Ingress configurations

Container Service for Kubernetes \(ACK\) provides reliable Ingress controller components. These components integrate with the Server Load Balancer \(SLB\) service and provide the flexible and reliable Ingress service for your ACK clusters.

The following example shows an Ingress orchestration template. When you configure an Ingress in the ACK console, you must set annotations. In some cases, you must create dependencies. For more information, see [Create an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Create an Ingress.md), [Configure an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Configure an Ingress.md), and [Kubernetes Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/). Ingresses support the Configmap configurations. For more information, see [Configmap](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/).

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/service-match: 'new-nginx: header("foo", /^bar$/)'           #Canary release rule. In this example, the request header is used.
    nginx.ingress.kubernetes.io/service-weight: 'new-nginx: 50,old-nginx: 50'                #The route weight.
  creationTimestamp: null
  generation: 1
  name: nginx-ingress
  selfLink: /apis/extensions/v1beta1/namespaces/default/ingresses/nginx-ingress
spec:
  rules:                                                        ##The Ingress rule.
  - host: foo.bar.com
    http:
      paths:
      - backend:
          serviceName: new-nginx
          servicePort: 80
        path: /
      - backend:
          serviceName: old-nginx
          servicePort: 80
        path: /
tls:            ##Enable TLS to configure a secure Ingress.
  - hosts:
    - *.xxxxxx.cn-hangzhou.alicontainer.com
    - foo.bar.com
    secretName: nginx-ingress-secret     ##The Secret name.
status:
  loadBalancer: {}
```

## Annotations

For each Ingress, you can configure its annotations, Ingress controller, and Ingress rules. For example, you can set the route weight, canary release rule, and rewrite rules. For more information about Ingress annotations, see [Annotations](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/).

For example, the following rewrite annotation, `nginx.ingress.kubernetes.io/rewrite-target: /`, indicates that /path is redirected to the root path /. The root path can be recognized by the backend service.

## Ingress rules

Ingress rules are used to manage external access to the services in the cluster. HTTP or HTTPS rules can be used. You can configure the following items in rules: domain name \(virtual hostname\), URL path, service name, and port.

For each rule, set the following parameters:

-   Domain: the test domain or virtual hostname of your service, such as `foo.bar.com`.
-   Path: the URL path of your service. Each path is associated with a backend service. SLB forwards traffic to a backend service only when the request matches the domain and path.
-   Service: Specify a service in the form of `service:port`. You must specify a route weight for each service. The Ingress routes traffic to the matching service based on the route weight.
    -   Name: the name of the backend service.
    -   Port: the port of the service.
    -   Weight: the route weight of the service in the service group.

        **Note:**

        1.  The weight is a percentage value. For example, you can set the same weight of 50% for two services.
        2.  A service group includes services that have the same domain and path defined in the Ingress configuration. If no weight is set for a service, the default value 100 is used.

## Canary releases

Container Service for Kubernetes supports multiple traffic splitting methods. This allows you to select more suitable solutions for specific scenarios, such as canary releases and A/B testing, including:

**Note:** Only Ingress controllers of 0.12.0-5 and later support traffic splitting.

1.  Traffic splitting based on request headers
2.  Traffic splitting based on cookies
3.  Traffic splitting based on query parameters

After a canary release is configured, only requests that match defined rules are routed to the specified service. If the weight of the service is lower than 100%, requests that match defined rules are routed to one of the services in the service group based on the weight.

## TLS

You can use a Secret that contains a TLS private key and certificate to encrypt the Ingress. This ensures secure routing. The TLS Secret must contain the certificate named tls.crt and private key named tls.key. For more information about how TLS works, see [TLS](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls). For more information about how to create a Secret, see [Configure an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Configure an Ingress.md).

## Labels

You can add labels to the Ingress to indicate the characteristics of the Ingress.

