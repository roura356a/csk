# 通过Ambassador暴露应用API

Ambassador Edge Stack（AES）是一个基于Envoy Proxy实现的高性能的Ingress Controller和API网关。 AES通过Custom Resource Definitions（CRD）使用Enovy提供的功能，集成了速率限制、身份认证、负载均衡和可观测性等功能。本文介绍如何通过Ambassador Edge Stack（AES）暴露部署在ACK集群中的应用API。

-   [创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)
-   [安装和设置Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## 步骤一：部署AES

1.  执行以下命令部署AES。

    ```
    kubectl apply -f https://www.getambassador.io/yaml/aes-crds.yaml
    kubectl wait --for condition=established --timeout=90s crd -lproduct=aes
    kubectl apply -f https://www.getambassador.io/yaml/aes.yaml
    kubectl -n ambassador wait --for condition=available --timeout=90s deploy -lproduct=aes
    ```

2.  执行以下命令确认AES部署成功。

    ```
    kubectl get pod -n ambassador
    ```

    预期输出：

    ```
    NAME                                READY   STATUS    RESTARTS   AGE
    ambassador-566d496b77-tg6ng         1/1     Running   0          2m
    ambassador-redis-5fbd59f4fb-88gm8   1/1     Running   0          2m
    ```

    部署Ambassador的Pod显示Running，表示AES部署成功。


## 步骤二：暴露应用API

1.  使用以下YAML示例创建一个测试应用及对应的Service。

    ```
    cat <<-EOF | kubectl apply -f -
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: quote
      namespace: ambassador
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
      namespace: ambassador
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
            image: docker.io/datawire/quote:0.4.1
            ports:
            - name: http
              containerPort: 8080
    EOF
                            
    ```

2.  确认应用及对应的Service创建成功。

    -   确认应用部署成功。

        ```
        kubectl get pod -n ambassador -l app=quote
        ```

        预期输出：

        ```
        NAME                    READY   STATUS    RESTARTS   AGE
        quote-d57b799b4-jdt7r   1/1     Running   0          6m29s
        ```

        部署应用的Pod显示running，表示应用部署成功。

    -   确认服务部署成功。

        ```
        kubectl get svc -n ambassador quote
        ```

        预期输出：

        ```
        NAME    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
        quote   ClusterIP   172.23.1*.***   <none>        80/TCP    5m45s
        ```

        返回结果中显示服务名称，表示服务部署成功。

3.  创建Mapping CRD，暴露应用API。

    ```
    cat <<-EOF | kubectl apply -f -
    apiVersion: getambassador.io/v2
    kind: Mapping
    metadata:
        name: backend
    spec:
        prefix: /backend/
        service: quote
    EOF
    ```

4.  确认Mapping CRD资源创建成功。

    ```
    kubectl get mappings -n ambassador
    ```

    预期输出：

    ```
    NAME                       PREFIX      SERVICE          STATE   REASON
    ambassador-devportal       /docs/      12*.*.*.*:8500
    ambassador-devportal-api   /openapi/   12*.*.*.*:8500
    quote-backend              /backend/   quote
    ```

    返回结果中显示Mapping CRD资源，表示Mapping CRD资源部署成功。

5.  获取AES服务的IP地址。

    ```
    kubectl get -n ambassador service ambassador -o "go-template={{range .status.loadBalancer.ingress}}{{or .ip .hostname}}{{end}}"
    ```

    预期输出：

    ```
    47.94.**.**
    ```

6.  访问暴露的应用API。

    ```
    curl -k https://{{IP}}/backend/
    {
      "server": "frosty-kiwi-ewe7vk96",
      "quote": "A principal idea is omnipresent, much like candy.",
      "time": "2020-08-01T08:41:37.054259819Z"
    }
    ```

    请将`${IP}`替换为步骤[5](#step_v3z_qrx_t7m)获取的AES服务IP地址。


**相关文档**  


[Ambassador Edge Stack](https://www.getambassador.io/docs/latest/tutorials/getting-started/)

[使用Ambassador Edge Stack管理Ingress资源](/cn.zh-CN/最佳实践/网络/使用Ambassador Edge Stack管理Ingress资源.md)

