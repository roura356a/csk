# Knative Gateway

Knative Gateway provides multiple cloud gateway solutions that require no resident resources. These solutions provide the same stability and reliability as cloud services. Knative Gateway allows you to reduce infrastructure costs and O&M workloads.

## Benefits

By default, the Knative community version supports multiple gateway solutions, such as Istio, Gloo, Contour, Kourier, and Ambassador. Among these solutions, Istio is most frequently used. This is because Istio can also be used as a service mesh. Each serverless Kubernetes \(ASK\) cluster must contain at least two resident gateway instances. The two gateway instances provide backup for each other to ensure high availability. The Kubernetes controllers of these gateways must be resident. You must pay infrastructure and O&M fees for these resident instances.

ASK Knative uses Server Load Balancer \(SLB\) instances as gateways. Knative Gateway supports both HTTP and HTTPS. By default, Knative generates a self-signed certificate for HTTPS connections. This certificate can secure all domain names. Therefore, you can use the certificate to test application services. Before you use Knative to deploy online services, you must create an HTTPS certificate and replace the certificate ID of Knative Gateway Service with the ID of the HTTPS certificate. For more information, see [Configure SSL certificates](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Knative management/Manage Knative services/Configure SSL certificates.md).

To improve user experience, Alibaba Cloud provides Knative Gateway based on SLB instances. Knative Gateway provides gateway services that are required by ASK clusters. Knative Gateway provides the same stability and reliability as cloud resources. Therefore, no resident resources are required. This reduces infrastructure costs and O&M workloads.

![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5547867061/p171606.png)

## Use Knative Gateway

1.  Query the IP address of the SLB instance.

    Run the following command:

    ```
    kubectl -n knative-serving get svc
    ```

    Output example:

    ```
    NAME              TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                      AGE
    ingress-gateway   LoadBalancer   172.19.1*. ***   106.15.2**. **   80:32185/TCP,443:31137/TCP   69d
    ```

2.  Access a Knative Gateway Service.

    -   Access a Knative Gateway Service over HTTP.

        Run the following command:

        ```
        curl -H  "Host: helloworld-go.default.example.com" http://106.15.2**. **
        ```

        Output example:

        ```
        Hello Knative!
        ```

    -   Access a Knative Gateway Service over HTTPS.

        Run the following command:

        ```
        curl -H  "Host: helloworld-go.default.example.com" https://106.15.2**.** -k
        ```

        Output example:

        ```
        Hello Knative!
        ```

3.  To bind the IP address of the SLB instance to the domain name of Knative, modify the hosts file. The following example shows how to bind the IP address and domain name:

    **Note:** The default root domain name of Knative is example.com. You can customize a domain name. For more information, see [Set up a custom domain name for Knative Serving](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative services/Use a custom domain name in Knative.md).

    ```
    106.15.2**.** helloworld-go.default.example.com
    ```

    After you modify the hosts file, if you can use the domain name to access the Knative Gateway Service, it indicates that Knative Gateway functions as normal.

    ![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5547867061/p170859.png)


