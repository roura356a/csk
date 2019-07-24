# Step 1: Deploy a service {#task_v5r_rpy_sfb .task}

This topic describes how to deploy a service.

-   You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   You have connected to the Kubernetes cluster by using kubectl. For more information, see [Connect to a Kubernetes cluster by using kubectl](../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#).

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Applications** \> **Deployments**.
3.  In the upper-right corner, click **Create by Template**.![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/61479/156393239630940_en-US.png)


4.  Select the target cluster and namespace, select a sample template or customize a template, and then click **DEPLOY**.![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/61479/156393239630949_en-US.png)

 

    In this example, a template is orchestrated to deploy an Nginx application that contains the required deployment, the target service, and an Ingress. The deployment exposes its port through NodePort. The Ingress provides externally accessible services. The orchestration template is as follows:

    ``` {#codeblock_hyx_gty_60r}
    apiVersion: extensions/v1beta1
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
          - image: registry.cn-hangzhou.aliyuncs.com/xianlu/old-nginx
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
      sessionAffity: None
      type: NodePort
    ---
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
        name: gray-release
    spec:
      rules:
      - host: www.example.com
        http:
          paths:
          # The service of an earlier version.
          - path: /
            backend:
              serviceName: old-nginx
              servicePort: 80
    ```

5.  In the left-side navigation pane under Container Service-Kubernetes, choose **Discovery and Load Balancing** \> **Ingresses**. 

    You can see that the virtual host name points to old-nginx.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/61479/156393239630952_en-US.png)

6.  Log on to the Master node and run the curl command to view the Ingress. 

    ```
    # curl -H "Host: www.example.com" http://<EXTERNAL_IP> 
    ```

    **Note:** You can obtain the value of <EXTERNAL\_IP\> by using either of the following two methods:

    -   Run the following command:

        ```
        kubectl get ingress
        ```

    -   In the left-side navigation pane under Container Service-Kubernetes, choose **Discovery and Load Balancing** \> **Ingresses** to view the endpoint information of the target Ingress.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/61479/156393239632797_en-US.png)

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/61479/156393239630961_en-US.png)


