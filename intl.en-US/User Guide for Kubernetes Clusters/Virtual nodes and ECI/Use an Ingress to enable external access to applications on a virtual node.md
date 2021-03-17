# Use an Ingress to enable external access to applications on a virtual node

This topic describes how to use an Ingress to enable external access to applications that are deployed on a virtual node of a Container Service for Kubernetes \(ACK\) cluster. This allows you to provide the applications with scalable and unlimited computing capacity without the need to create new nodes in the ACK cluster. This also ensures the elasticity of your workloads to withstand traffic fluctuations.

## Prerequisites

A virtual node is deployed in your ACK cluster. For more information, see [Deploy ack-virtual-node in an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy ack-virtual-node in an ACK cluster.md).

The virtual-node-affinity-injection: enabled label is added to the namespace vk. For more information, see [Create a pod in a namespace with specified labels](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy ack-virtual-node in an ACK cluster.md).

## Procedure

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  Log on to the[ACK console](https://partners-intl.console.aliyun.com/#/cs).

3.  In the left-side navigation pane, choose **Applications** \> **Deployments**. In the upper-right corner of the Deployments page, click **Create from Template**.

4.  In the left-side navigation pane of the ACK console, click **Clusters**.

5.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

6.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

7.  On the **Workload** page, click the **Deployments** tab. In the upper-right corner of the Deployments tab, click **Create from Template**.

8.  Select a sample template or customize a template, and click **Create**.

    ![Sample template](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1365359951/p47324.png)

    You can use the following YAML template to create applications and required resource objects for external access:

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


## Verify the results

-   Go to the **Workload** page and click the **Deployments** tab. On the Deployments tab, you can find the newly created Deployments that are named coffee and tea.

    ![Sample applications](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1365359951/p47325.png)

-   Go to the **Workload** page and click the **Pods** tab. On the Pods tab, you can find that all pods of the created Deployments run on the virtual-kubelet node.

    ![Virtual-Kubelet](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1365359951/p47329.png)

-   On the details page of the cluster, click **Ingresses** in the left-side navigation pane. On the Ingresses page, you can find the newly created Ingress.

    ![Ingress](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1365359951/p47330.png)

-   Run the following command to check whether you can access the applications through the Ingress:

    ```
    kubectl  get ing
    ```

    Expected output:

    ```
    NAME           HOSTS              ADDRESS          PORTS   AGE
    cafe-ingress   cafe.example.com   114.55.252.185   80      6m18s
    ```

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


