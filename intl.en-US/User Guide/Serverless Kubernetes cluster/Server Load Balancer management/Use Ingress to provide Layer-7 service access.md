# Use Ingress to provide Layer-7 service access {#concept_f2x_bfn_j2b .concept}

In the Alibaba Cloud serverless Kubernetes cluster, Server Load Balancer provides Layer-4 service access. You can also use Layer-7 service access provided by Ingress. This document describes how to provide Layer-7 domain name service access in the serverless Kubernetes cluster.

## Prerequisites {#section_e5m_bgn_j2b .section}

-   You have created a Serverless cluster. VPC cluster must be configured with a NAT gateway to access the external network and download the container image.
-   You have connected to the cluster by using kubectl, see [Connect to a Kubernetes cluster by using kubectl](reseller.en-US/User Guide/Serverless Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#).

## Instructions {#section_and_jfn_j2b .section}

-   If Server Load Balancer is not specified, system automatically generates a public network Server Load Balancer instance.
-   The default front-end listening ports for SLB instances are 80 \(HTTP Protocol\) and 443 \(HTTPS protocol \).
-   By default, the HTTPS certificate of the SLB instance is initialized for the first created Ingress-configured TLS certificate. Otherwise, the system default certificate is initialized. You can modify it in the SLB console as needed.
-   When you specify to use an existing SLB instance, SLB instance specification must be of performance guarantee type \(supports ENI\). Also, make sure that ports 80 and 443 are not currently used by other services.

## Use the default generated SLB instance {#section_j5s_3fn_j2b .section}

If an SLB instance is not specified, the system automatically generates a performance guaranteed public network SLB instance when the first Ingress is created.

1.  Deploy test services.
    1.  Create a file cafe-service.yaml, copy the following code to the file, and then run the kubectl apply -f cafe-service.yaml command to deploy a coffee service and tea service.

        ``` {#codeblock_yor_56y_x9k}
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
                image: registry.cn-hangzhou.aliyuncs.com/acs-sample/nginxdemos:latest
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
          replicas: 1
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
                image: registry.cn-hangzhou.aliyuncs.com/acs-sample/nginxdemos:latest
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
        ```

        The following outputs indicate the services are deployed:

        ``` {#codeblock_57p_plu_3ic}
        deployment "coffee" created
        service "coffee-svc" created
        deployment "tea" created
        service "tea-svc" created
        ```

    2.  Run the `kubectl get svc,deploy` command to view the status of the services.

        ``` {#codeblock_igy_ba7_t44}
        NAME             TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
        svc/coffee-svc   ClusterIP   <none>       <none>        80/TCP    1m
        svc/tea-svc      ClusterIP   <none>       <none>        80/TCP    1m
        
        NAME            DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
        deploy/coffee   2         2         2            2           1m
        deploy/tea      1         1         1            1           1m
        ```

2.  Configure an Ingress.
    1.  Create a file `cafe-ingress.yaml`, copy the following code into the file, and run the `kubectl apply -f cafe-ingress.yaml` command.

        ``` {#codeblock_0vz_4ns_jlz}
        apiVersion: extensions/v1beta1
        kind: Ingress
        metadata:
          name: cafe-ingress
        spec:
          rules:
          # Set a Layer-7 domain name.
          - host: foo.bar.com
            http:
              paths:
              # Set the context path.
              - path: /tea
                backend:
                  serviceName: tea-svc
                  servicePort: 80
              # Set the context path.
              - path: /coffee
                backend:
                  serviceName: coffee-svc
                  servicePort: 80
        ```

        The following output indicates that the Ingress is deployed.

        ``` {#codeblock_k1p_j7u_yj3}
        ingress "cafe-ingress" created
        ```

    2.  Run the `kubectl get ing` command to obtain the IP address of the SLB instance.

        ``` {#codeblock_ch7_dx5_oib}
        NAME           HOSTS         ADDRESS          PORTS     AGE
        cafe-ingress   foo.bar.com   139.***.**.***   80        1m
        ```

3.  Test service access.

    **Note:** The domain name of the SLB instance IP must be resolved manually.

    In this example, a DNS domain name resolution rule is added to hosts for testing service access. We recommend that you enter the domain name in your work environment.

    ``` {#codeblock_lrc_1h0_mfd}
    139.***.**.***    foo.bar.com
    ```

    -   Use your browser to access the coffee service.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16502/156436647410319_en-US.png)

    -   Run the following command to access the coffee service:

        ``` {#codeblock_w4f_3b4_7jl}
        curl -H "Host: foo.bar.com" http://139.***.**.***/coffee
        ```

    -   Use your browser to access the coffee service.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16502/156436647510320_en-US.png)

    -   Run the following command to access the tea service:

        ``` {#codeblock_my9_nox_6au}
        curl -H "Host: foo.bar.com" http://139.***.**.***/tea
        ```


## Use the specified SLB instance {#section_n5s_3fn_j2b .section}

You can specify to use of an existing SLB instance by using the `service.beta.kubernetes.io/alicloud-loadbalancer-id` annotation, but the SLB instance specification must be of performance guarantee type \(supports ENI\).

**Note:** System automatically initializes ports 80 and 443 of the SLB instance, make sure that ports are not currently used by other services.

1.  Deploy test services.
    1.  Create a file `tomcat-service.yml`, copy the following code into the file, and run the `kubectl apply -f tomcat-service.yml` command to deploy a Tomcat application.

        ``` {#codeblock_1ts_4q8_5th}
        apiVersion: extensions/v1beta1
        kind: Deployment
        metadata:
          name: tomcat
        spec:
          replicas: 1
          selector:
            matchLabels:
              run: tomcat
          template:
            metadata:
              labels:
                run: tomcat
            spec:
              containers:
              - image: tomcat:7.0
                imagePullPolicy: Always
                name: tomcat
                ports:
                - containerPort: 8080
                  protocol: TCP
              restartPolicy: Always
        ---
        apiVersion: v1
        kind: Service
        metadata:
          name: tomcat
        spec:
          ports:
          - port: 8080
            protocol: TCP
            targetPort: 8080
          selector:
            run: tomcat
          clusterIP: None
        ```

        The following outputs indicates the Tomcat application is deployed:

        ``` {#codeblock_8db_uc4_2lk}
        deployment "tomcat" created
        service "tomcat" created
        ```

    2.  Run the `kubectl get svc,deploy tomcat` command to view the application status.

        ``` {#codeblock_l0v_nt9_web}
        NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
        svc/tomcat   ClusterIP   <none>       <none>        8080/TCP   1m
        
        NAME            DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
        deploy/tomcat   1         1         1            1           1m
        ```

2.  Apply for an SLB instance.

    You must apply for a **performance guarantee type** SLB instance \(such as slb.s2.small\) under the cluster and region. According to the specific needs, private or public network can be used. For more information, see [Create an SLB instance](../../../../reseller.en-US/Instance/Create an SLB instance.md#). In this example, apply for an Internet SLB instance and record the ID of the SLB instance.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16502/156436647510321_en-US.png)

3.  Configure an TLS certificate.

    You must configure the TLS certificate for HTTPS access.

    1.  Run the following commands to generate an TLS certificate:

        ``` {#codeblock_lhd_py5_u2z}
        $ openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=bar.foo.com/O=bar.foo.com"# 创建TLS证书Secret
        $ kubectl create secret tls cert-example --key tls.key --cert tls.crt
        
        secret "cert-example" created
        ```

    2.  Run the following command to view the created TLS certificate:

        ``` {#codeblock_82n_oj2_3gf}
        $ kubectl get secret cert-example
        
        NAME           TYPE                DATA      AGE
        cert-example   kubernetes.io/tls   2         12s
        ```

    **Note:** System automatically initializes the SLB HTTPS default certificate according to the first created Ingress TLS certificate. If you want to modify the HTTPS default certificate, you can modify it in the SLB console. If you want to configure multiple certificates, you can manually add them in the SLB console HTTPS listener domain name extension.

4.  Configure an Ingress.
    1.  Create a file `tomcat-ingress.yml`, copy the following command to the file, and run the `kubectl apply -f tomcat-ingress.yml` command.

        ``` {#codeblock_nv7_v55_a4x}
        apiVersion: extensions/v1beta1
        kind: Ingress
        metadata:
          name: tomcat-ingress
          annotations:
            # Specify an SLB instance.
            service.beta.kubernetes.io/alicloud-loadbalancer-id: lb-xxxxxxxxxx            ##Set the ID of the target SLB instance.
            service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners: "true"
        spec:
          tls:
          - hosts:
            - bar.foo.com
            # Set a TLS certificate.
            secretName: cert-example
          rules:
          # Set a Layer-7 domain name.
          - host: bar.foo.com
            http:
              paths:
              # Set the context path.
              - path: /
                backend:
                  serviceName: tomcat
                  servicePort: 8080
        ```

        The following output indicates the tomcat-ingress is deployed:

        ``` {#codeblock_hhc_qmh_zwq}
        ingress "tomcat-ingress" created
        ```

    2.  Run the `kubectl get ing tomcat-ingress` command to obtain the IP address of the SLB instance.

        ``` {#codeblock_fes_7de_1yh}
        NAME             HOSTS         ADDRESS        PORTS     AGE
        tomcat-ingress   bar.foo.com   47.***.**.**   80, 443   1m
        ```

5.  Test service access.

    **Note:** Currently, the domain name of the SLB instance IP must be resolved manually.

    In this example, a DNS domain name resolution rule is added to hosts for testing service access. We recommend that you enter the domain name in your work environment.

    ``` {#codeblock_9st_c9s_xuf}
    47.***.**.**   bar.foo.com
    ```

    -   Use you browser to access the Tomcat service.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16502/156436647510322_en-US.png)

    -   Run the following command to access the Tomcat service:

        ``` {#codeblock_yqg_fp7_koz}
        curl -k -H "Host: bar.foo.com" https://47.***.**.**
        ```


