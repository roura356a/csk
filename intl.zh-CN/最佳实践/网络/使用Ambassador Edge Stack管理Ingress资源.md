---
keyword: [Ambassador Edge Stack, CRD, Envoy Proxy]
---

# 使用Ambassador Edge Stack管理Ingress资源

Ambassador Edge Stack（AES）是一个基于Envoy Proxy实现的高性能的Ingress Controller和API网关。AES通过Custom Resource Definitions（CRD）使用Enovy提供的功能，集成了速率限制、身份认证、负载均衡和可观测性等功能。本文将介绍如何使用AES管理K8s Ingress资源。

-   [创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   [安装和设置kubectl](https://kubernetes.io/docs/tasks/kubectl/install/?spm=a2c4g.11186623.2.11.1c924c9f2tNE6y)。

## 安装部署AES

ACK默认不支持部署AES，您可以自行根据需要进行部署。下文以YAML方式为例介绍如何安装部署AES，更多其他安装部署方式请参见[AES官方文档](https://www.getambassador.io/)。

1.  执行以下命令部署AES。

    ```
    kubectl apply -f https://www.getambassador.io/yaml/aes-crds.yaml && \
    kubectl wait --for condition=established --timeout=90s crd -lproduct=aes && \
    kubectl apply -f https://www.getambassador.io/yaml/aes.yaml && \
    kubectl -n ambassador wait --for condition=available --timeout=90s deploy -lproduct=aes
    ```

    输出以下内容即表示部署成功。

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


## 使用AES测试Ingress Controller功能

为了测试AES的Ingress Controller功能，您需要部署一个测试用的Deployment。

1.  执行以下命令，创建Deployment部署配置。

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

2.  执行以下命令，创建Ingress配置。

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

    AES通过CRD提供了更多功能，如下面的配置CRD完全等同如上Ingress的配置。更多信息，请参见[官方文档](https://www.getambassador.io/docs/latest/topics/running/ingress-controller/)。

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

3.  执行以下命令，获取IP地址。

    ```
    kubectl get -n ambassador service ambassador -o "go-template={{range .status.loadBalancer.ingress}}{{or .ip .hostname}}{{end}}"
    ```

4.  执行以下命令，测试AES的Ingress Controller功能。

    ```
    curl -k https://{{AMBASSADOR_IP}}/backend/    #替换AMBASSADOR_IP为上面获取的IP地址。
    ```

    输出类似以下代码即表示成功。

    ```
      {
        "server": "icky-grapefruit-xar5if66",
        "quote": "A small mercy is nothing at all?",
        "time": "2020-07-17T09:00:57.646315605Z"
      }
    ```


