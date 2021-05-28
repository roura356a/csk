---
keyword: [deploy an Ingress, applications that provide services by using an Ingress]
---

# Deploy applications that provide services by using Ingresses

This topic describes how to deploy applications that provide services by using an Ingress on a virtual node of a Container Service for Kubernetes \(ACK\) cluster. This allows you to provide the applications with scalable and unlimited computing capacities without the need to create new nodes in the cluster. This also ensures the elasticity of the applications to withstand traffic fluctuations.

## Prerequisites

-   A virtual node is deployed in your ACK cluster. For more information, see [Deploy the virtual node controller and use it to create Elastic Container Instance-based pods](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy the virtual node controller and use it to create Elastic Container Instance-based pods.md).
-   The virtual-node-affinity-injection: enabled label is added to the vk namespace. For more information, see [Create a pod in a namespace with specified labels](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy the virtual node controller and use it to create Elastic Container Instance-based pods.md).

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  In the upper-right corner of the page, click **Create from Template**.

6.  Select a sample template or customize a template, and click **Create**.

    ![Sample template](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1365359951/p47324.png)

    You can use the following YAML template to create applications and an Ingress that is used to enable access to the applications:

    ```
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
      name: coffee
    spec:
      replicas: 2
      selector:
        matchLabels:
          app: coffee
      template:
        metadata:
          labels:
            app: coffee
        spec:
          containers:
          - name: coffee
            image: nginxdemos/hello:plain-text
            ports:
            - containerPort: 80
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: coffee-svc
    spec:
      ports:
      - port: 80
        targetPort: 80
        protocol: TCP
      selector:
        app: coffee
      clusterIP: None
    ---
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
      name: tea
    spec:
      replicas: 3
      selector:
        matchLabels:
          app: tea
      template:
        metadata:
          labels:
            app: tea
        spec:
          containers:
          - name: tea
            image: nginxdemos/hello:plain-text
            ports:
            - containerPort: 80
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: tea-svc
      labels:
    spec:
      ports:
      - port: 80
        targetPort: 80
        protocol: TCP
      selector:
        app: tea
      clusterIP: None
    ---
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: cafe-ingress
    spec:
      rules:
      - host: cafe.example.com
        http:
          paths:
          - path: /tea
            backend:
              serviceName: tea-svc
              servicePort: 80
          - path: /coffee
            backend:
              serviceName: coffee-svc
              servicePort: 80
    ```


## Verify the result

-   In the left-side navigation pane of the cluster details page, choose **Workloads** \> **Deployments**. You can find the newly created coffee and tea applications.
-   In the left-side navigation pane of the cluster details page, choose **Workloads** \> **Pods**. You can verify that the pods of the newly created applications run on virtual-kubelet nodes.
-   On the details page of the cluster, choose **Services and Ingresses** \> **Ingresses**. You can find the newly created Ingress.
-   Run the following command to query the Ingress. Then, test access to the Ingress.

    ```
    kubectl  get ing
    ```

    Expected output:

    ```
    NAME           HOSTS              ADDRESS          PORTS   AGE
    cafe-ingress   cafe.example.com   114.55.252.185   80      6m18s
    ```

    Run the following command to access `"Host:cafe.example.com" <EXTERNAL_IP>/tea` to test whether the tea application can be accessed:

    ```
    curl -H "Host:cafe.example.com" <EXTERNAL_IP>/tea
    ```

    Expected output:

    ```
    Server address: 192.168.xx.xx:80
    Server name: tea-658d56f6cc-cxxxx
    Date: 25/Sep/2020:12:36:50 +0000
    URI: /tea
    Request ID: b01d5bab9ae07abb8bc385377193xxxx
    ```

    Run the following command to access `"Host:cafe.example.com" <EXTERNAL_IP>/coffee` to test whether the coffee application can be accessed:

    ```
    curl -H "Host:cafe.example.com" <EXTERNAL_IP>/coffee
    ```

    Expected output:

    ```
    Server address: 192.168.xx.xx:80
    Server name: coffee-8c8ff9b4f-hxxxx
    Date: 25/Sep/2020:12:36:47 +0000
    URI: /coffee
    Request ID: 722fe41a65a7fb552613c56e0a9axxxx
    ```


