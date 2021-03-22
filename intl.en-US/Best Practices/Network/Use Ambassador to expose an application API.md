# Use Ambassador to expose an application API

Ambassador Edge Stack \(AES\) includes a high-performance Ingress controller and a high-performance API gateway that are built on top of Envoy Proxy. AES allows you to use various features of Envoy with Custom Resource Definitions \(CRDs\). These features include rate limiting, identity verification, load balancing, and observability. This topic describes how to use AES to expose the API of an application that is deployed in a Container Service for Kubernetes \(ACK\) cluster.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Install and set up kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## Step 1: Deploy AES

1.  Deploy AES.

    ```
    kubectl apply -f https://www.getambassador.io/yaml/aes-crds.yaml
    kubectl wait --for condition=established --timeout=90s crd -lproduct=aes
    kubectl apply -f https://www.getambassador.io/yaml/aes.yaml
    kubectl -n ambassador wait --for condition=available --timeout=90s deploy -lproduct=aes
    ```

2.  Verify that AES is deployed.

    ```
    kubectl get pod -n ambassador
    ```

    Expected output:

    ```
    NAME                                READY   STATUS    RESTARTS   AGE
    ambassador-566d496b77-tg6ng         1/1     Running   0          2m
    ambassador-redis-5fbd59f4fb-88gm8   1/1     Running   0          2m
    ```

    If the pod where Ambassador is deployed is in the Running state, AES is deployed.


## Step 2: Expose the API of an application

1.  Create an application and a Service.

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

2.  Verify that the application and Service are created.

    -   Verify that the application is deployed.

        ```
        kubectl get pod -n ambassador -l app=quote
        ```

        Expected output:

        ```
        NAME                    READY   STATUS    RESTARTS   AGE
        quote-d57b799b4-jdt7r   1/1     Running   0          6m29s
        ```

        If the pod where the application is deployed is in the Running state, the application is deployed.

    -   Verify that the Service is deployed.

        ```
        kubectl get svc -n ambassador quote
        ```

        Expected output:

        ```
        NAME    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
        quote   ClusterIP   172.23.1*.***   <none>        80/TCP    5m45s
        ```

        If the output displays the name of the Service, the Service is deployed.

3.  Create a Mapping CRD to expose the application API.

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

4.  Verify that the Mapping CRD is created.

    ```
    kubectl get mappings -n ambassador
    ```

    Expected output:

    ```
    NAME                       PREFIX      SERVICE          STATE   REASON
    ambassador-devportal       /docs/      12*. *. *.*:8500
    ambassador-devportal-api   /openapi/   12*. *. *.*:8500
    quote-backend              /backend/   quote
    ```

    If the output displays the Mapping CRD, the Mapping CRD is deployed.

5.  Obtain the service IP address of AES.

    ```
    kubectl get -n ambassador service ambassador -o "go-template={{range .status.loadBalancer.ingress}}{{or .ip .hostname}}{{end}}"
    ```

    Expected output:

    ```
    47.94.**. **
    ```

6.  Call the exposed application API.

    ```
    curl -k https://{{IP}}/backend/
    {
      "server": "frosty-kiwi-ewe7vk96",
      "quote": "A principal idea is omnipresent, much like candy.",
      "time": "2020-08-01T08:41:37.054259819Z"
    }
    ```

    Replace `${IP}` with the service IP address of AES that is obtained in Step [5](#step_v3z_qrx_t7m).


**Related topics**  


[Ambassador Edge Stack](https://www.getambassador.io/docs/latest/tutorials/getting-started/)

[Use AES to manage Ingresses](/intl.en-US/User Guide for Kubernetes Clusters/Network/Best practices/Use AES to manage Ingresses.md)

