# Ingress configurations {#concept_rmj_1yn_vdb .concept}

Alibaba Cloud Container Service provides the highly reliable Ingress controller components and integrates with Alibaba Cloud Server Load Balancer to provide the flexible and reliable Ingress service for your Kubernetes clusters.

See the following Ingress orchestration example. You must configure the annotation when creating an Ingress in the Container Service console. Some configurations must create dependencies. For more information, see [Create an Ingress in Container Service console](intl.en-US/User Guide/Kubernetes cluster/Ingress/Create an Ingress in Container Service console.md#), [Support for Ingress](intl.en-US/User Guide/Kubernetes cluster/Server Load Balancer/Support for Ingress.md#), and [Kubernetes Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/). Ingress also supports the configuration of configmap. For more information, see [https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/).

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/service-match: 'new-nginx: header("foo", /^bar$/)' #Grayscale publish rule, this example is request header
    Nginx. ingress. kubernetes. IO/service-weight: 'New-nginx: 50, old-nginx: 50' # FTraffic weight annotation
  creationTimestamp: null
  generation: 1
  name: nginx-ingress
  selfLink: /apis/extensions/v1beta1/namespaces/default/ingresses/nginx-ingress
spec:
  rules: ##The Ingress rule
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
tls: ## Enable TLS to configure the secure Ingress service
  - hosts:
    - *.xxxxxx.cn-hangzhou.alicontainer.com
    - foo.bar.com
    secretName: nginx-ingress-secret ##The name of the used secret.
status:
  loadBalancer: {}
```

## Annotation {#section_ojg_fyn_vdb .section}

You can configure an ingress annotation, specifying the ingress controller to use, rules for routing, such as routing weight rules, grayscale publish, and rewrite rules. For more information, see [https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/).

For example, a typical rewrite annotation `nginx.ingress.kubernetes.io/rewrite-target: /` redirects the path /path to the path /that can be recognized by the backend services.

## Rules {#section_pjg_fyn_vdb .section}

The rules indicate those that authorize the inbound access to the cluster and are generally the HTTP rules, including the domain name \(virtual hostname\), URL access path, service name, and port.

You must complete the following configurations for each HTTP rule:

-   Host: Enter the testing domain name of an Alibaba Cloud Kubernetes cluster or a virtual hostname, such as `foo.bar.com`.
-   Path: Specify the URL path of the service access.  Each path is associated with a backend service. Before Alibaba Cloud Server Load Balancer forwards the traffic to the backend, all inbound requests must match with the domain name and path.
-   Backend configuration: Service configuration that is a combination of `service:port` and traffic weight. The Ingress traffic is forwarded to the matched backend services based on the traffic weight.
    -   Name: The name of the backend service forwarded by Ingress.
    -   Port: The port exposed by the service.
    -   Weight: The weight rate of each service in a service group.

        **Note:** 

        1.  The service weight is calculated in relative values. For example, if both service weights are set to 50, the weight ratio of both services is 50%.
        2.  A service group \(a service with the same Host and Path in the same ingress yaml\) has a default weight value of 100 and the weight is not explicitly set.

## Grayscale publish {#section_t2h_hlf_l2b .section}

Container Service supports different traffic segmentation methods for grayscale publish and AB test scenarios.

**Note:** Currently, the Alibaba Cloud Container Service Kubernetes Ingress Controller requires 0.12.0-5 and above to support the traffic segmentation feature.

1.  Traffic segmentation based on the request header.
2.  Traffic segmentation based on cookie.
3.  Traffic segmentation based on query \(request\) parameters.

After the grayscale rule is configured, the request that matches the grayscale publish rule can be routed to the set service. If the service sets a weight rate of less than 100%, requests that match the grayscale publish rule continue to be routed to the corresponding service based on the weight rate.

## TLS {#section_rjg_fyn_vdb .section}

You can encrypt the Ingress by specifying a secret that contains the TLS private key and certificate to implement the secure Ingress access.  The TLS secret must contain the certificate named tls.crt and private key named tls.key. For more information about the TLS principles, see [TLS](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls). For how to create a secret, see [Configure a safe routing service](intl.en-US/User Guide/Kubernetes cluster/Server Load Balancer/Support for Ingress.md#section_j4d_jrs_vdb).

## Tag {#section_sjg_fyn_vdb .section}

You can add tags for Ingress to indicate the characteristics of the Ingress.

