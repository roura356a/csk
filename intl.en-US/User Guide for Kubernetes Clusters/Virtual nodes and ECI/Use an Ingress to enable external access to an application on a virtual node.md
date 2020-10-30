# Use an Ingress to enable external access to an application on a virtual node

This topic describes how to use an Ingress to enable external access to an application that is deployed on a virtual node of a cluster of Container Service for Kubernetes \(ACK\). This allows you to provide the application with scalable and unlimited computing capacity without the need to create new nodes in the ACK cluster. This also meets the elasticity requirements of your business during peak hours and off-peak hours.

## Prerequisites

A virtual node is deployed in your ACK cluster. For more information, see [Deploy ack-virtual-node in an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy ack-virtual-node in an ACK cluster.md).

The virtual-node-affinity-injection: enabled label is attached to the namespace where the application pods are deployed. For more information, see [Create pods in a namespace with specified labels](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy ack-virtual-node in an ACK cluster.md).

## Procedure

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Workload**.

5.  On the **Workload** page, click the **Deployments** tab. In the upper-right corner of the Deployments tab, click **Create from Template**.

6.  Select the sample template or a custom template, and click **Create**.

    ![Sample template](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1365359951/p47324.png)

    You can use the following YAML template to create an application and required resource objects for external access:

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

    ![Sample application](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1365359951/p47325.png)

-   Go to the **Workload** page and click the **Pods** tab. On the Pods tab, you can find that all pods of the created Deployments run on the virtual-kubelet node.

    ![Virtual-Kubelet](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1365359951/p47329.png)

-   In the left-side navigation pane of the details page of the cluster, click **Ingresses**. On the Ingresses page, you can find the newly created Ingress.

    ![Ingress](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1365359951/p47330.png)

-   To check whether the created Ingress can be accessed, run the following commands:

    ```
    kubectl -n vk get ing
    ```

    ```
    curl -H "Host:cafe.example.com" <EXTERNAL_IP>/tea
    ```

    ```
    curl -H "Host:cafe.example.com" <EXTERNAL_IP>/coffee
    ```

    ![Ingress](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1365359951/p47334.png)


