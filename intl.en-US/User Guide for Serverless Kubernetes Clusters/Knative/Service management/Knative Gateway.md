# Knative Gateway

Knative Gateway provides multiple cloud gateway solutions that do not require resident resources. These solutions provide the same stability and reliability as cloud services. Knative Gateway allows you to reduce infrastructure costs and O&M workloads.

## Benefits

By default, open source Knative supports multiple gateway solutions, such as Istio, Gloo, Contour, Kourier, and Ambassador. Among these solutions, Istio is most frequently used. This is because Istio can also be used as a service mesh. Each serverless Kubernetes \(ASK\) cluster must contain at least two resident gateway instances. The two gateway instances provide backup for each other to ensure high availability. The gateway controllers must be resident. You must pay infrastructure and O&M fees for these resident resources.

ASK Knative uses Server Load Balancer \(SLB\) instances as gateways. Internet-facing SLB instances function as Internet gateways and internal-facing SLB instances function as internal gateways. Knative Gateway supports both HTTP and HTTPS. By default, Knative generates a self-signed certificate for HTTPS connections. This certificate can secure all domain names. Therefore, you can use the certificate to test application services. Before you use Knative to deploy online services, you must create an HTTPS certificate and replace the certificate ID of Knative Gateway Service with the ID of the HTTPS certificate. For more information, see [Configure SSL certificates](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Knative/Service management/Configure SSL certificates.md).

To improve user experience, Alibaba Cloud provides Knative Gateway based on SLB instances.

![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5547867061/p171606.png)

## Use Knative Gateway

1.  Query the IP address of an SLB instance.

    Run the following command:

    ```
    kubectl -n knative-serving get svc
    ```

    Output example:

    ```
    NAME                    TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                      AGE
    ingress-gateway         LoadBalancer   172.21.XX.XX   8.131.XX.XX      80:32701/TCP,443:30561/TCP   2d20h
    ingress-local-gateway   LoadBalancer   172.21.XX.XX   192.168.XX.XX    80:32537/TCP                 2d20h
    ```

    **Note:**

    -   ingress-gateway indicates an Internet gateway that exposes Services to the Internet.
    -   ingress-local-gateway indicates an internal gateway that exposes Services only within a virtual private cloud \(VPC\).
2.  Access a Knative Gateway Service.

    -   Access the Service from the Internet
        -   Access the Service over HTTP.

            Run the following command:

            ```
            curl -H  "Host: helloworld-go.default.example.com" http://8.131.XX.XX
            ```

            Output example:

            ```
            Hello Knative!
            ```

        -   Access the Service over HTTPS.

            Run the following command:

            ```
            curl -H  "Host: helloworld-go.default.example.com" https://8.131.XX.XX -k
            ```

            Output example:

            ```
            Hello Knative!
            ```

    -   Access the Service within a VPC

        Use the connect string `service name.namespace name.svc.cluster.local` to access the Service. The helloworld-go Service in the default namespace is used as an example.

        ```
        http://helloworld-go.default.svc.cluster.local
        ```

3.  Bind the IP address of the SLB instance to the domain name of Knative by modifying the hosts file. The following example shows how to bind the IP address to the domain name:

    **Note:** The default root domain name of Knative is example.com. You can customize a domain name. For more information, see [Set up a custom domain name for Knative Serving](/intl.en-US/User Guide for Kubernetes Clusters/Knative/Manage Knative services/Set up a custom domain name for Knative Serving.md).

    ```
    106.15.2**.** helloworld-go.default.example.com
    ```

    After you modify the hosts file, if you can use the domain name to access the Knative Gateway Service, it indicates that Knative Gateway functions as normal.

    ![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5547867061/p170859.png)


