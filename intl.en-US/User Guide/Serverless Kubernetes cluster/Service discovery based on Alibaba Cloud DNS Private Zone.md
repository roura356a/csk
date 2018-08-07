# Service discovery based on Alibaba Cloud DNS Private Zone {#concept_efk_nsq_b2b .concept}

Alibaba Cloud Serverless Kubernetes supports service discovery. Currently,  service discovery for intranet SLB and headless service is supported.

## Alibaba Cloud DNS Private Zone {#section_q15_qsq_b2b .section}

Alibaba Cloud DNS Private Zone is a private domain name resolution and management service based on VPC \(virtual private cloud\) environment of Alibaba Cloud. You can map a private domain name to an IP resource address in one or more of the custom private networks, while your private domain names are not accessible in other network environments.

## Prerequisites {#section_cd5_h5q_b2b .section}

1.  You have activated Alibaba Cloud DNS Private Zone in the Alibaba Cloud DNS console.
2.  You have successfully created a Serverless Kubernetes cluster, see [Create a Serverless Kubernetes cluster](intl.en-US/User Guide/Serverless Kubernetes cluster/Cluster management/Create a Serverless Kubernetes cluster.md#).
3.  You have successfully connected to the Kubernetes cluster, see [Connect to a Kubernetes cluster by using kubectl](intl.en-US/User Guide/Kubernetes cluster/Clusters/Connect to a Kubernetes cluster by using kubectl.md#).

## Procedure {#section_u15_qsq_b2b .section}

1.  Connect to the Kubernetes cluster by using kubectl and run the following command to confirm the connection to the cluster.

    ```
    kubectl cluster-info
     Kubernetes master is running at https://xxxxxx.serverless-1.kubernetes.cn-shanghai.aliyuncs.com:6443
    ```

2.  Deploy and create a service.  Currently, only intranet service and headless service are supported.

    Take the intranet service as an example. Create a sample nginx-deployment-basic.yaml file.

    ```
    vim nginx-deployment-basic.yaml
    ```

    The sample template is as follows, copy the following yaml code in the yaml file. Then run the `kubectl create -f nginx-deployment-basic.yaml` command to create it.

    ```
    apiVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
     kind: Deployment
     metadata:
       name: nginx-deployment-basic
       labels:
         app: nginx
     spec:
       replicas: 2
       selector:
         matchLabels:
           app: nginx
       template:
         metadata:
           labels:
             app: nginx
         spec:
           containers:
           - name: nginx
             image: nginx:1.7.9 # replace it with your exactly <image_name:tags>
             ports:
             - containerPort: 80
     ---
     apiVersion: v1
     kind: Service
     metadata:
       name: nginx-service-intranet # Access by service name as short domain name
       annotations: ## Add annotation
         service.beta.kubernetes.io/alicloud-loadbalancer-address-type: intranet
     spec:
       ports:
       - port: 80
         protocol: TCP
       selector:
         app: nginx
       type: LoadBalancer
    ```

    You can also create services of the headless service type, as shown in the following sample template.

    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx-service-headless
    spec:
      ports:
      - port: 80
        protocol: TCP
      selector:
        app: nginx
      clusterIP: None
    ```

3.  Run the following command to view the health status of the application.

    ```
    kubectl get svc,pod,deployment
    ```

4.  Log on to the [Alibaba Cloud DNS console](https://dns.console.aliyun.com/).
5.  Click **Private Zone** \> **Zone List** in the left-side navigation pane to see that the record is automatically generated under this list.

    ![](images/6171_en-US.png)

    You can access Service \(long domain or short domain \) by the private domain name in VPC network environment.

    -   Long domain access: In this example, `nginx-service-intranet. $NAMESPACE.svc.cluster.local`, where $NAMESPACE is the Serverless cluster ID, which can be seen in the console, or you can also use environmental variables in the yaml file of the pod.

        ```
        env:
               - name: NAMESPACE
                 valueFrom:
                   fieldRef:
                     fieldPath: metadata.namespace
        ```

    -   Short domain access: nginx-service-intranet or nginx-service-headless, the name of the service defined in the yaml template. 

For more information, see [serverless-k8s-examples](https://github.com/AliyunContainerService/serverless-k8s-examples).

