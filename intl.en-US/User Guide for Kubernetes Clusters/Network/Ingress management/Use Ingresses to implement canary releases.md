---
keyword: [Ingress controller, canary release, blue-green release, A/B testing]
---

# Use Ingresses to implement canary releases

When you upgrade your application versions, you can implement rolling updates, phased releases, blue-green releases, and canary releases. This topic describes how to implement canary releases for applications in a Container Service for Kubernetes \(ACK\) cluster by using Ingress controllers.

[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)

You can implement a canary release or a blue-green release to publish two identical production environments for an earlier application version and a new application version. In this case, when users send requests, ACK routes some requests to the new version based on specific rules. If the new version runs as normal for a period of time, you can switch all traffic from the earlier version to the new version.

A/B testing is a way of implementing canary releases. In A/B testing, some users use the earlier version, and requests from the other users are forwarded to the new version. If the new version runs as normal for a period of time, you can gradually switch all traffic to the new version.

## Scenarios

**Traffic splitting based on requests**

Assume that you have already created Service A for your production environment to provide Layer 7 access for users. When new features are available, you need to create Service A' for the new application version. If you want to keep Service A for external access, you can forward requests whose values of the foo parameters in the request headers match `bar` or whose values of the foo parameters in the cookie match `bar` to Service A'. If the new version stably runs for a period of time, you can switch all traffic from Service A to Service A'. Then, you can delete Service A.

![Scenario 1](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5905514161/p9950.png)

**Traffic splitting based on Service weights**

Assume that you have already created Service B in your production environment to provide Layer 7 access for users. When some issues are fixed, you need to create Service B' for the new application version. If you want to keep Service B for external access, you can switch 20% of traffic to Service B'. If the new version stably runs for a period of time, you can switch all traffic from Service B to Service B'. Then, you can delete Service B.

![Scenario 2](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5905514161/p9951.png)

Ingress controllers of ACK provide the following traffic splitting methods to support the preceding requirements of application releases.

-   Traffic splitting based on request headers. This method applies to scenarios where canary releases or A/B testing is required.
-   Traffic splitting based on cookie. This method applies to scenarios where canary releases or A/B testing is required.
-   Traffic splitting based on query parameters. This method applies to scenarios where canary releases or A/B testing is required.
-   Traffic splitting based on Service weights. This method applies to scenarios where blue-green releases are required.

## Annotations

Ingress controllers use the following annotations to implement canary releases of an application.

-   nginx.ingress.kubernetes.io/service-match

    This annotation is used to configure match rules for requests to the new application version.

    ```
    nginx.ingress.kubernetes.io/service-match: | 
        <service-name>: <match-rule>
    # Parameters
    # service-name: the name of a Service. Requests that match the rules specified by match-rule are forwarded to the Service.
    # match-rule: the match rules for requests.
    #
    # Match rules:
    # 1. Supported match types
    # - header: based on the request header. Regular expressions and exact match rules are supported.
    # - cookie: based on the cookie. Regular expressions and exact match rules are supported.
    # - query: based on the query parameters. Regular expressions and exact match rules are supported.
    #
    # 2. Match methods
    # - Regular expressions: /{regular expression}/. A regular expression is enclosed within forward slashes (/).
    # - Exact match rules:"{exact expression}". An exact match rule is enclosed within quotation marks (").
    ```

    Examples of match rules:

    ```
    # If the value of the foo parameter in the request header matches the regular expression ^bar$, the request is forwarded to the new-nginx Service.
    new-nginx: header("foo", /^bar$/)
    # If the value of the foo parameter in the request header exactly matches the value bar, the request is forwarded to the new-nginx Service.
    new-nginx: header("foo", "bar")
    # If the value of the foo parameter in the cookie matches the regular expression ^sticky-.+$, the request is forwarded to the new-nginx Service.
    new-nginx: cookie("foo", /^sticky-.+$/)
    # If the value of the foo parameter in the query parameters exactly matches the value bar, the request is forwarded to the new-nginx Service.
    new-nginx: query("foo", "bar")
    ```

-   nginx.ingress.kubernetes.io/service-weight

    This annotation is used to set the weights of the Services for the earlier and new application versions.

    ```
    nginx.ingress.kubernetes.io/service-weight: | 
        <new-svc-name>:<new-svc-weight>, <old-svc-name>:<old-svc-weight>
    Parameters:
    new-svc-name: the name of the Service for the new application version.
    new-svc-weight: the traffic weight of the Service for the new application version.
    old-svc-name: the name of the Service for the earlier application version.
    old-svc-weight: the traffic weight of the Service for the earlier application version.
    ```

    Example of Service weight configurations:

    ```
    nginx.ingress.kubernetes.io/service-weight: | 
        new-nginx: 20, old-nginx: 60
    ```


## Step 1: Create an application

Create an NGINX application and deploy an Ingress controller to enable Layer 7 access to the application by using domain names.

1.  Create a Deployment and a Service.

    1.  Create a file named nginx.yaml.

        ```
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: old-nginx
        spec:
          replicas: 2
          selector:
            matchLabels:
              run: old-nginx
          template:
            metadata:
              labels:
                run: old-nginx
            spec:
              containers:
              - image: registry.cn-hangzhou.aliyuncs.com/acs-sample/old-nginx
                imagePullPolicy: Always
                name: old-nginx
                ports:
                - containerPort: 80
                  protocol: TCP
              restartPolicy: Always
        ---
        apiVersion: v1
        kind: Service
        metadata:
          name: old-nginx
        spec:
          ports:
          - port: 80
            protocol: TCP
            targetPort: 80
          selector:
            run: old-nginx
          sessionAffinity: None
          type: NodePort
        ```

    2.  Run the following command to create the Deployment and Service:

        ```
        kubectl apply -f nginx.yaml
        ```

2.  Create an Ingress.

    1.  Create a file named ingress.yaml.

        ```
        apiVersion: networking.k8s.io/v1beta1
        kind: Ingress
        metadata:
          name: gray-release
        spec:
          rules:
          - host: www.example.com
            http:
              paths:
              # Configure a Service for the earlier application version.
              - path: /
                backend:
                  serviceName: old-nginx
                  servicePort: 80
        ```

    2.  Run the following command to create the Ingress:

        ```
        kubectl apply -f ingress.yaml
        ```

3.  Test access to the Ingress.

    1.  Run the following command to query the IP address of the Ingress for external access:

        ```
        kubectl get ingress
        ```

    2.  Run the following command to access the Ingress:

        ```
        curl -H "Host: www.example.com"  http://<EXTERNAL_IP>
        ```

        The following output is returned:

        ```
        old
        ```


## Step 2: Implement a canary release of the application

Release a new NGINX application version and configure Ingress rules.

1.  Create a Deployment and a Service for the new application version.

    1.  Create a file named nginx1.yaml.

        ```
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: new-nginx
        spec:
          replicas: 1
          selector:
            matchLabels:
              run: new-nginx
          template:
            metadata:
              labels:
                run: new-nginx
            spec:
              containers:
              - image: registry.cn-hangzhou.aliyuncs.com/acs-sample/new-nginx
                imagePullPolicy: Always
                name: new-nginx
                ports:
                - containerPort: 80
                  protocol: TCP
              restartPolicy: Always
        ---
        apiVersion: v1
        kind: Service
        metadata:
          name: new-nginx
        spec:
          ports:
          - port: 80
            protocol: TCP
            targetPort: 80
          selector:
            run: new-nginx
          sessionAffinity: None
          type: NodePort
        ```

    2.  Run the following command to create the Deployment and Service:

        ```
        kubectl apply -f nginx1.yaml
        ```

2.  Configure Ingress rules for the new application version.

    ACK provides the following types of Ingress rules. Select a type of Ingress rule based on your requirements.

    -   Configure Ingress rules to forward requests that match the rules to the new application version. In the following example, only requests whose values of the foo parameters in the request headers match the regular expression `bar` are forwarded to the new application version.
        1.  Modify the Ingress that is created in [2](#step_ee9_ta0_d84) based on the following content.

            ```
            apiVersion: networking.k8s.io/v1beta1
            kind: Ingress
            metadata:
              name: gray-release
              annotations:
                # Only requests whose values of the foo parameters in the request headers match the regular expression bar are forwarded to the new-nginx Service.
                nginx.ingress.kubernetes.io/service-match: | 
                  new-nginx: header("foo", /^bar$/)
            spec:
              rules:
              - host: www.example.com
                http:
                  paths:
                  # Configure a Service for the earlier application version.
                  - path: /
                    backend:
                      serviceName: old-nginx
                      servicePort: 80
                  # Configure a Service for the new application version.
                  - path: /
                    backend:
                      serviceName: new-nginx
                      servicePort: 80
            ```

        2.  Test access to the Ingress.

            -   Run the following command to access the NGINX application:

                ```
                curl -H "Host: www.example.com"  http://<EXTERNAL_IP>
                ```

                The following output is returned:

                ```
                old
                ```

            -   Run the following command to access the NGINX application by using a request whose value of the foo parameter in the request header matches the regular expression `bar`:

                ```
                curl -H "Host: www.example.com" -H "foo: bar" http://<EXTERNAL_IP>
                ```

                The following output is returned:

                ```
                new
                ```

            You can run the preceding commands again to test the access. The result is that only requests whose values of the foo parameters in the request headers match the regular expression `bar` are forwarded to the new application version.

    -   Configure Ingress rules to forward a specific proportion of requests that match the rules to the new application version. In the following example, only 50% of the requests whose values of the foo parameters in the request headers match the regular expression `bar` are forwarded to the new version.
        1.  Modify the Ingress that is created in [2](#step_ee9_ta0_d84) based on the following content.

            ```
            apiVersion: networking.k8s.io/v1beta1
            kind: Ingress
            metadata:
              name: gray-release
              annotations:
                # Only requests whose values of the foo parameters in the request headers match the regular expression bar are forwarded to the new-nginx Service.
                nginx.ingress.kubernetes.io/service-match: |
                    new-nginx: header("foo", /^bar$/)
                # Only 50% of the requests that match the preceding rule are forwarded to the new-nginx Service.
                nginx.ingress.kubernetes.io/service-weight: |
                    new-nginx: 50, old-nginx: 50
            spec:
              rules:
              - host: www.example.com
                http:
                  paths:
                  # Configure a Service for the earlier application version.
                  - path: /
                    backend:
                      serviceName: old-nginx
                      servicePort: 80
                  # Configure a Service for the new application version.
                  - path: /
                    backend:
                      serviceName: new-nginx
                      servicePort: 80
            ```

        2.  Test the access to the Ingress.

            -   Run the following command to access the NGINX application:

                ```
                curl -H "Host: www.example.com"  http://<EXTERNAL_IP>
                ```

                The following output is returned:

                ```
                old
                ```

            -   Run the following command to access the NGINX application by using a request whose value of the foo parameter in the request header matches the regular expression `bar`:

                ```
                curl -H "Host: www.example.com" -H "foo: bar" http://<EXTERNAL_IP>
                ```

                The following output is returned:

                ```
                new
                ```

            You can run the preceding commands again to test the access. The result is that only 50% of the requests whose values of the foo parameters in the request headers match the regular expression `bar` are forwarded to the new application version.

    -   Configure Ingress rules to forward a specific proportion of requests to the new NGINX application. In the following example, only 50% of requests are forwarded to the new NGINX application.
        1.  Modify the Ingress that is created in [2](#step_ee9_ta0_d84) based on the following content.

            ```
            apiVersion: networking.k8s.io/v1beta1
            kind: Ingress
            metadata:
              name: gray-release
              annotations:
                  # 50% of requests are forwarded to the new-nginx Service.
                  nginx.ingress.kubernetes.io/service-weight: |
                      new-nginx: 50, old-nginx: 50
            spec:
              rules:
              - host: www.example.com
                http:
                  paths:
                  # Configure a Service for the earlier application version.
                  - path: /
                    backend:
                      serviceName: old-nginx
                      servicePort: 80
                  # Configure a Service for the new application version.
                  - path: /
                    backend:
                      serviceName: new-nginx
                      servicePort: 80
            ```

        2.  Run the following command to access the Ingress:

            ```
            curl -H "Host: www.example.com" http://<EXTERNAL_IP>
            ```

            You can run the preceding command again to test the access. The result is that only 50% of the requests are forwarded to the new NGINX application.


## Step 3: Delete the earlier application version and the related Service

If the new NGINX application runs as expected for a period of time, you need to bring the earlier application version offline and provide only the new application version for access.

1.  Modify the Ingress that is created in [2](#step_ee9_ta0_d84) based on the following content.

    ```
    apiVersion: networking.k8s.io/v1beta1
    kind: Ingress
    metadata:
      name: gray-release
    spec:
      rules:
      - host: www.example.com
        http:
          paths:
          # Configure a Service for the new application version.
          - path: /
            backend:
              serviceName: new-nginx
              servicePort: 80
    ```

2.  Run the following command to access the Ingress:

    ```
    curl -H "Host: www.example.com" http://<EXTERNAL_IP>
    ```

    The following output is returned:

    ```
    new
    ```

    You can run the preceding command again to test the access. The result is that all the requests are forwarded to the new NGINX application.

3.  Delete the Deployment and Service for the earlier NGINX application.

    1.  Run the following command to delete the Deployment for the earlier NGINX application:

        ```
        kubectl delete deploy <Deployment name>
        ```

    2.  Run the following command to delete the Service for the earlier NGINX application:

        ```
        kubectl delete svc <Service name>
        ```


