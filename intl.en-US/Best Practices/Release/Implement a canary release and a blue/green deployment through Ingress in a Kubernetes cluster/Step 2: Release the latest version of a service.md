# Step 2: Release the latest version of a service

This topic describes how to release the latest version of a service by using the Ingress function provided by Alibaba Cloud Container Service for Kubernetes.

-   [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md)
-   [Use kubectl to connect to a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Manage and access clusters/Use kubectl to connect to a cluster.md)

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Workload**.

5.  Select the **Deployments** tab.****

6.  Click **Create from Template** in the upper-right corner of the Deployments tab.

7.  Select a sample template or use an existing template, and click **Create**.

    Deploy an Nginx application of the latest version that contains the deployment, the service, and an Ingress. The following orchestration template contains the deployment and the service:

    ```
    apiVersion: extensions/v1beta1
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
          - image: registry.cn-hangzhou.aliyuncs.com/xianlu/new-nginx
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

    The following Ingress orchestration templates apply to different annotation settings:

    **Note:** If you do not set service-match or service-weight in the annotations field of an Ingress template, the Ingress controller forwards client requests evenly to the latest and earlier versions of the service in a random manner.

    -   Ingress template used to specify that only the client requests that meet the requirement of the regular expression foo=bar can be routed to the latest version of the service

        ```
        apiVersion: extensions/v1beta1
        kind: Ingress
        metadata:
          name: gray-release
          annotations:
              nginx.ingress.kubernetes.io/service-match: |# Only requests whose request headers meet the requirement of the regular expression foo=bar can be routed to the latest version of the service new-nginx.
                  new-nginx: header("foo", /^bar$/)
        spec:
          rules:
          - host: www.example.com
            http:
              paths:
              # Earlier version of the service
              - path: /
                backend:
                  serviceName: old-nginx
                  servicePort: 80
              # Latest version of the service
              - path: /
                backend:
                  serviceName: new-nginx
                  servicePort: 80
        ```

    -   Ingress template used to specify the proportion of requests that can be routed to the latest version of the service

**Note:** In this example, the latest version of the service and the earlier version of the service are weighted at 50% each.

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: gray-release
  annotations:
      nginx.ingress.kubernetes.io/service-weight: |# Allows 50% of the traffic to be routed to the latest version of the service new-nginx.
          new-nginx: 50, old-nginx: 50
spec:
  rules:
  - host: www.example.com
    http:
      paths:
      # Earlier version of the service
      - path: /
        backend:
          serviceName: old-nginx
          servicePort: 80
      # Latest version of the service
      - path: /
        backend:
          serviceName: new-nginx
          servicePort: 80
```

    -   Ingress template used to specify that only 50% of the client requests that meet the requirement of foo=bar can be routed to the latest version of the service

        ```
        apiVersion: extensions/v1beta1
        kind: Ingress
        metadata:
          name: gray-release
          annotations:
            nginx.ingress.kubernetes.io/service-match: |# Only requests whose request headers meet the requirement of the regular expression foo=bar can be routed to the latest version of the service new-nginx.
                new-nginx: header("foo", /^bar$/)
            nginx.ingress.kubernetes.io/service-weight: |# Only 50% of the requests whose request headers meet the requirement of the regular expression foo=bar can be routed to the latest version of the service new-nginx.
                new-nginx: 50, old-nginx: 50
        spec:
          rules:
          - host: www.example.com
            http:
              paths:
              # Earlier version of the service
              - path: /
                backend:
                  serviceName: old-nginx
                  servicePort: 80
              # Latest version of the service
              - path: /
                backend:
                  serviceName: new-nginx
                  servicePort: 80
        ```

8.  In the left-side navigation pane, click **Ingresses**.

    You can see that the virtual host name points to old-nginx.

9.  Log on to a master node of the cluster. Then, use curl to run the following command to send requests to the ingress:

    -   Only the client requests that meet the requirement of the regular expression foo=bar can be routed to the latest version of the service.

        ```
        # curl -H "Host: www.example.com" -H "foo: bar" http://<EXTERNAL_IP> 
        ```

        ![nodes](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0555359951/p31016.png)

    -   The specified proportion of requests can be routed to the latest version of the service.

        ```
        # curl -H "Host: www.example.com" http://<EXTERNAL_IP> 
        ```

        ![nodes](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0555359951/p31017.png)

    -   Only 50% of the client requests that meet the requirement of foo=bar can be routed to the latest version of the service.

        ```
        # curl -H "Host: www.example.com" -H "foo: bar" http://<EXTERNAL_IP> 
        ```

        ![nodes](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0555359951/p31018.png)


