---
keyword: Knative Gateway
---

# Knative Gateway

Knative provides multiple Ingress gateway solutions for you to create gateways from cloud resources. These gateways are stable and reliable because they do not require resident resources. Knative Ingress gateways help reduce infrastructure costs and simplify your maintenance work. This topic describes the benefits of Knative Ingress gateways and how to use Knative Ingress gateways.

## Benefits

By default, open source Knative provides multiple Ingress gateway solutions, such as Istio, Gloo, Contour, Kourier, and Ambassador. Among these solutions, Istio is most frequently used. This is because Istio can also be used as a service mesh. At least two resident gateway instances are required for each application that is deployed within Knative. The two gateway instances provide backup for each other to ensure high availability. The Kubernetes controllers of these gateways must be resident. You must pay infrastructure and maintenance fees for these resident instances.

A serverless Kubernetes \(ASK\) cluster that has Knative enabled uses Server Load Balancer \(SLB\) instances as gateways. Internet-facing SLB instances function as external gateways and internal-facing SLB instances function as internal gateways. Knative Ingress gateways support both HTTP and HTTPS. By default, Knative generates a self-signed SSL certificate for each gateway. This certificate can secure all domain names. Therefore, you can use the certificate for testing purposes. Before you use Knative to deploy applications, you must create an SSL certificate and replace the certificate ID of Knative Gateway Service with the ID of the SSL certificate. For more information, see [Configure an SSL certificate](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Knative/Service management/Configure an SSL certificate.md).

To improve user experience, Alibaba Cloud allows you to use SLB instances as Knative Ingress gateways.

![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5547867061/p171606.png)

## Use Knative Ingress gateways

1.  Query the IP address of the SLB instance.

    Run the following command:

    ```
    kubectl -n knative-serving get svc
    ```

    Expected output:

    ```
    NAME                    TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                      AGE
    ingress-gateway         LoadBalancer   172.21.XX.XX   8.131.XX.XX      80:32701/TCP,443:30561/TCP   2d20h
    ingress-local-gateway   LoadBalancer   172.21.XX.XX   192.168.XX.XX    80:32537/TCP                 2d20h
    ```

    **Note:**

    -   ingress-gateway indicates an external gateway that exposes applications to the Internet.
    -   ingress-local-gateway indicates an internal gateway that exposes applications only within a virtual private cloud \(VPC\).
2.  Access the application.

    -   Access the application over the Internet
        -   Access the application over HTTP.

            Run the following command:

            ```
            curl -H  "Host: helloworld-go.default.example.com" http://8.131.XX.XX
            ```

            Expected output:

            ```
            Hello Knative!
            ```

        -   Access the application over HTTPS.

            Run the following command:

            ```
            curl -H  "Host: helloworld-go.default.example.com" https://8.131.XX.XX -k
            ```

            Expected output:

            ```
            Hello Knative!
            ```

    -   Access the application within a VPC

        Use the connect string `application name.namespace name.svc.cluster.local` to access the application. The helloworld-go application that belongs to the default namespace is used as an example.

        ```
        http://helloworld-go.default.svc.cluster.local
        ```

3.  Bind the IP address of the SLB instance to the domain name of Knative by modifying the hosts file. The following example shows how to bind the IP address to the domain name:

    **Note:** The default root domain name of Knative is example.com. You can use a custom domain name. For more information, see [Set up a custom domain name for Knative Serving](/intl.en-US/User Guide for Kubernetes Clusters/Knative/Manage Knative services/Set up a custom domain name for Knative Serving.md).

    ```
    106.15.2**.** helloworld-go.default.example.com
    ```

    If you can use the domain name to access the application after you modify the hosts file, the Knative Ingress gateway functions as normal.

    ![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5547867061/p170859.png)


