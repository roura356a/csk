# Deploy an Ingress application on a virtual node {#concept_265366 .concept}

This topic describes how to deploy an Ingress application on a virtual node of a Kubernetes cluster. With the virtual node, the Kubernetes cluster can provide the application with greater computing capability without the need for a new node needs to be created for the cluster.

## Prerequisites {#section_3h9_anv_kch .section}

A virtual node is deployed in your target Kubernetes cluster. For more information, see [Deploy a virtual node](../../../../reseller.en-US/User Guide/Kubernetes cluster/Auto Scaling/Deploy a virtual node.md#).

The tag virtual-node-affinity-injection: enabled is added to the target namespace. For more information, see [Set a namespace tag to create a pod](../../../../reseller.en-US/User Guide/Kubernetes cluster/Auto Scaling/Deploy a virtual node.md#li_8tb_qv1_yws).

## Procedure {#section_qtq_7pj_ul2 .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
2.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
3.  In the left-side navigation pane under Container Service-Kubernetes, choose **Applications** \> **Deployments**.
4.  In the upper-right corner, click **Create by Template**.
5.  Select the target cluster and namespace, select an example template or customize a template, and then click **DEPLOY**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/218949/155952873347324_en-US.png)

    You can use the following template to customize a YAML template to create an Ingress application:

    ``` {#codeblock_33x_yof_xw5}
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


## Verify the results {#section_5pi_men_c6n .section}

-   To verify that the coffee and tea deployments are created, follow these steps:

    1.  In the left-side navigation pane under Container Service-Kubernetes, choose **Applications** \> **Deployments**.
    2.  Select the target cluster and namespace.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/218949/155952873347325_en-US.png)

-   To verify that all the pods of the Ingress application run on the virtual-kubelet node, follow these steps:

    1.  In the left-side navigation pane under Container Service-Kubernetes, choose **Applications** \> **Pods**.
    2.  Select the target cluster and namespace.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/218949/155952873347329_en-US.png)

-   To verify that the target Ingress is created, follow these steps:

    1.  In the left-side navigation pane under Container Service-Kubernetes, choose **Discovery and Load Balancing** \> **Ingresses**.
    2.  Select the target cluster and namespace.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/218949/155952873347330_en-US.png)

-   To verify that the created Ingress application can be accessed, run the following commands:

    ``` {#codeblock_21j_oba_8n6}
    kubectl -n vk get ing
    curl -H "Host:cafe.example.com" <EXTERNAL_IP>/tea
    curl -H "Host:cafe.example.com" <EXTERNAL_IP>/coffee
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/218949/155952873347334_en-US.png)


