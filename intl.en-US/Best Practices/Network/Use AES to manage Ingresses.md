---
keyword: [Ambassador Edge Stack, CRD, Envoy Proxy]
---

# Use AES to manage Ingresses

Ambassador Edge Stack \(AES\) is built on Envoy Proxy. It can function as a high-performance API gateway and Ingress controller for Kubernetes clusters. AES functions as a control plane that translates Kubernetes custom resources into Envoy configurations for Envoy to handle requests. AES is integrated with rate limiting, request identification, load balancing, and observability. This topic describes how to use AES to manage Ingresses.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   [Install and set up kubectl](https://kubernetes.io/docs/tasks/kubectl/install/?spm=a2c4g.11186623.2.11.1c924c9f2tNE6y).

## Deploy AES in your Kubernetes cluster

By default, Container Service for Kubernetes does not deploy AES in Kubernetes clusters. You must manually deploy AES in Kubernetes clusters. The following example shows how to deploy AES in a Kubernetes clusters by using a YAML file. For more information about deployment methods, see the [AES documents](https://www.getambassador.io/).

1.  Run the following commands to deploy AES in a Kubernetes cluster:

    ```
    kubectl apply -f https://www.getambassador.io/yaml/aes-crds.yaml && \
    kubectl wait --for condition=established --timeout=90s crd -lproduct=aes && \
    kubectl apply -f https://www.getambassador.io/yaml/aes.yaml && \
    kubectl -n ambassador wait --for condition=available --timeout=90s deploy -lproduct=aes
    ```

    The following output indicates that AES is deployed:

    ```
    customresourcedefinition.apiextensions.k8s.io/authservices.getambassador.io created
    customresourcedefinition.apiextensions.k8s.io/consulresolvers.getambassador.io created
    customresourcedefinition.apiextensions.k8s.io/hosts.getambassador.io created
    customresourcedefinition.apiextensions.k8s.io/kubernetesendpointresolvers.getambassador.io created
    customresourcedefinition.apiextensions.k8s.io/kubernetesserviceresolvers.getambassador.io created
    customresourcedefinition.apiextensions.k8s.io/logservices.getambassador.io created
    customresourcedefinition.apiextensions.k8s.io/mappings.getambassador.io created
    customresourcedefinition.apiextensions.k8s.io/modules.getambassador.io created
    customresourcedefinition.apiextensions.k8s.io/ratelimitservices.getambassador.io created
    customresourcedefinition.apiextensions.k8s.io/tcpmappings.getambassador.io created
    customresourcedefinition.apiextensions.k8s.io/tlscontexts.getambassador.io created
    customresourcedefinition.apiextensions.k8s.io/tracingservices.getambassador.io created
    customresourcedefinition.apiextensions.k8s.io/filterpolicies.getambassador.io created
    customresourcedefinition.apiextensions.k8s.io/filters.getambassador.io created
    customresourcedefinition.apiextensions.k8s.io/ratelimits.getambassador.io created
    customresourcedefinition.apiextensions.k8s.io/projectcontrollers.getambassador.io created
    customresourcedefinition.apiextensions.k8s.io/projects.getambassador.io created
    customresourcedefinition.apiextensions.k8s.io/projectrevisions.getambassador.io created
    customresourcedefinition.apiextensions.k8s.io/authservices.getambassador.io condition met
    customresourcedefinition.apiextensions.k8s.io/consulresolvers.getambassador.io condition met
    customresourcedefinition.apiextensions.k8s.io/filterpolicies.getambassador.io condition met
    customresourcedefinition.apiextensions.k8s.io/filters.getambassador.io condition met
    customresourcedefinition.apiextensions.k8s.io/hosts.getambassador.io condition met
    customresourcedefinition.apiextensions.k8s.io/kubernetesendpointresolvers.getambassador.io condition met
    customresourcedefinition.apiextensions.k8s.io/kubernetesserviceresolvers.getambassador.io condition met
    customresourcedefinition.apiextensions.k8s.io/logservices.getambassador.io condition met
    customresourcedefinition.apiextensions.k8s.io/mappings.getambassador.io condition met
    customresourcedefinition.apiextensions.k8s.io/modules.getambassador.io condition met
    customresourcedefinition.apiextensions.k8s.io/projectcontrollers.getambassador.io condition met
    customresourcedefinition.apiextensions.k8s.io/projectrevisions.getambassador.io condition met
    customresourcedefinition.apiextensions.k8s.io/projects.getambassador.io condition met
    customresourcedefinition.apiextensions.k8s.io/ratelimits.getambassador.io condition met
    customresourcedefinition.apiextensions.k8s.io/ratelimitservices.getambassador.io condition met
    customresourcedefinition.apiextensions.k8s.io/tcpmappings.getambassador.io condition met
    customresourcedefinition.apiextensions.k8s.io/tlscontexts.getambassador.io condition met
    customresourcedefinition.apiextensions.k8s.io/tracingservices.getambassador.io condition met
    Warning: kubectl apply should be used on resource created by either kubectl create --save-config or kubectl apply
    namespace/ambassador configured
    serviceaccount/ambassador created
    clusterrole.rbac.authorization.k8s.io/ambassador created
    clusterrolebinding.rbac.authorization.k8s.io/ambassador created
    clusterrole.rbac.authorization.k8s.io/ambassador-projects created
    clusterrolebinding.rbac.authorization.k8s.io/ambassador-projects created
    service/ambassador-redis created
    deployment.apps/ambassador-redis created
    ratelimitservice.getambassador.io/ambassador-edge-stack-ratelimit created
    authservice.getambassador.io/ambassador-edge-stack-auth created
    secret/ambassador-edge-stack created
    mapping.getambassador.io/ambassador-devportal created
    mapping.getambassador.io/ambassador-devportal-api created
    service/ambassador created
    service/ambassador-admin created
    deployment.apps/ambassador created
    deployment.extensions/ambassador condition met
    deployment.extensions/ambassador-redis condition met
    ```


## Verify that AES functions as an Ingress controller

To verify that AES functions as an Ingress controller, you must create a Deployment.

1.  Use the following template to create a Deployment:

    ```
    cat <<-EOF | kubectl apply -f -
    apiVersion: v1
    kind: Service
      metadata:
        name: quote
    spec:
      ports:
      - name: http
        port: 80
        targetPort: 8080
      selector:
        app: quote
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: quote
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: quote
      strategy:
        type: RollingUpdate
      template:
        metadata:
          labels:
            app: quote
        spec:
          containers:
          - name: backend
            image: quay.io/datawire/quote:0.3.0
            ports:
            - name: http
              containerPort: 8080
    EOF
    ```

2.  Use the following template to create an Ingress:

    ```
    cat <<-EOF | kubectl apply -f - 
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      annotations:
        kubernetes.io/ingress.class: ambassador
      name: test-ingress
    spec:
      rules:
      - http:
          paths:
          - path: /backend/
            backend:
              serviceName: quote
              servicePort: 80
    EOF
    ```

    The following Mapping resource is equivalent to the preceding Ingress resource. For more information about how to use custom resource definitions \(CRDs\) in AES, see the [AES documents](https://www.getambassador.io/docs/latest/topics/running/ingress-controller/).

    ```
    cat > quote-backend.yaml <<-EOF
    apiVersion: getambassador.io/v2
    kind: Mapping
    metadata:
        name: backend
    spec:
        prefix: /backend/
        service: quote
    EOF
    ```

3.  Run the following command to query the IP address of the cluster:

    ```
    kubectl get -n ambassador service ambassador -o "go-template={{range .status.loadBalancer.ingress}}{{or .ip .hostname}}{{end}}"
    ```

4.  Run the following command to verify that AES functions as an Ingress controller:

    ```
    curl -k https://{{AMBASSADOR_IP}}/backend/    # Replace the value of AMBASSADOR_IP with the cluster IP address that you have obtained. 
    ```

    The following output indicates that AES functions as an Ingress controller:

    ```
      {
        "server": "icky-grapefruit-xar5if66",
        "quote": "A small mercy is nothing at all?",
        "time": "2020-07-17T09:00:57.646315605Z"
      }
    ```


