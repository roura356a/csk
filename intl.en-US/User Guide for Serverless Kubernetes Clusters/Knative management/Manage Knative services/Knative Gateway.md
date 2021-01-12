# Knative Gateway

Knative Gateway provides multiple cloud gateway solutions that require no resident resources. These solutions are as stable and reliable as cloud services. Knative Gateway helps you reduce infrastructure costs and O&M workloads.

## Benefits of Knative Gateway

By default, the Knative community version supports multiple gateway solutions, such as Istio, Gloo, Contour, Kourier, and Ambassador. Among these solutions, Istio is most frequently used. This is because Istio also functions as a service mesh. Each serverless Kubernetes \(ASK\) cluster must contain at least two resident gateway instances. The two gateway instances provide backup for each other to ensure high availability. The Kubernetes controllers of these gateways must be resident. You must pay infrastructure and O&M fees for these resident instances.

ASK Knative uses Server Load Balancer \(SLB\) instances as gateways. Knative Gateway supports both HTTP and HTTPS. By default, Knative generates a self-signed certificate for HTTPS connections. This certificate can secure all domain names. Therefore, you can use the certificate to test application services. Before you use Knative to deploy online services, you must create an HTTPS certificate and replace the certificate ID of Knative Gateway Service with the ID of the HTTPS certificate. For more information, see [Configure SSL certificates](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Knative management/Manage Knative services/Configure SSL certificates.md).

To improve user experience, Alibaba Cloud provides Knative Gateway based on SLB instances. Knative Gateway provides gateway services required by ASK clusters. Knative Gateway is as stable and reliable as cloud resources. Therefore, no resident resources are required. This reduces infrastructure costs and O&M workloads.

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

2.  Access a service.

    -   Access a service over HTTP.

        Run the following command:

        ```
        curl -H  "Host: helloworld-go.default.example.com" http://106.15.2**. **
        ```

        Output example:

        ```
        Hello Knative!
        ```

    -   Access a service over HTTPS.

        Run the following command:

        ```
        curl -H  "Host: helloworld-go.default.example.com" https://106.15.2**.** -k
        ```

        Output example:

        ```
        Hello Knative!
        ```

3.  Bind the IP address of the SLB instance to the domain name of Knative by modifying the hosts file. The following example shows how to bind the IP address and domain name:

    **Note:** The default root domain name of Knative is example.com. You can customize a domain name. For more information, see [Set up a custom domain name for Knative Serving](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative services/Use a custom domain name in Knative.md).

    ```
    106.15.2**.** helloworld-go.default.example.com
    ```

    After you modify the hosts file, if you can use the domain name to access the service, it indicates that Knative Gateway functions as expected.

    ![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5547867061/p170859.png)


## Modify the configurations of an SLB instance that serves as a gateway

By default, Knative Gateway exposes services through a public-facing SLB instance. If you want to expose only services in a virtual private cloud \(VPC\), specify an internal SLB instance that is deployed in the VPC. In addition to the network type, you can also specify the specification of an SLB instance or choose to use an existing SLB instance. When you create an SLB instance, you can specify the name of the SLB instance, choose primary and secondary zones, and set the billing method to pay-by-bandwidth.

Knative Gateway manages SLB instances through Ingress-Gateway K8s Service in the Knative-Serving namespace. The SLB instance is mapped to a Knative service by the cloud controller manager \(CCM\) of Alibaba Cloud. All of the following operations are performed by using Ingress-Gateway K8s Service. To modify the configurations of an SLB instance, you can run the `kubectl -n knative-serving edit svc ingress-gateway` command to modify the annotation.

-   Create an SLB instance of the VPC network type.

    `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-address-type: "intranet"` specifies an intranet SLB instance. `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-network-type: "vpc"` specifies that network type of the internal SLB instance is VPC.

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-address-type: "intranet"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 443
        protocol: TCP
        targetPort: 443
      selector:
        run: nginx
      type: LoadBalancer
    ```

    If you want an internal SLB instance that is connected to a VPC to communicate with the Internet, you must associate an elastic IP address \(EIP\) with the SLB instance. For more information, see [Associate an Elastic IP address with an SLB instance](/intl.en-US/Classic Load Balancer/User Guide/Instance/Associate an EIP with an SLB instance.md).

-   Specify the specification of an SLB instance.

    You can call CreateLoadBalancer to create an SLB instance of a specified specification or update the specification of an existing SLB instance. For more information, see [CreateLoadBalancer](https://help.aliyun.com/document_detail/27577.html#doc-api-Slb-CreateLoadBalancer).

    **Note:** If you modify the specification in the SLB console, CCM may restore the SLB instance to the original specification. Proceed with caution.

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-spec: "slb.s1.small"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 443
        protocol: TCP
        targetPort: 443
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   Specify the primary and secondary zones when you create an SLB instance.
    -   SLB instances that are deployed in specific regions do not support primary and secondary zones, such as SLB instances that are deployed in the Indonesia \(Jakarta\) region.
    -   After you specify primary and secondary zones for the SLB instance, you can no longer change the primary and secondary zones.

        ```
        apiVersion: v1
        kind: Service
        metadata:
          annotations:
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-master-zoneid: "cn-beijing-g"
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-slave-zoneid: "cn-beijing-f"
          name: nginx
          namespace: default
        spec:
          ports:
          - port: 80
            protocol: TCP
            targetPort: 80
          selector:
            run: nginx
          type: LoadBalancer
        ```

-   Create a pay-by-bandwidth SLB instance.
    -   Only public-facing SLB instances support pay-by-bandwidth. For more information about other limits, see [t4098.md\#]().
    -   The `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-charge-type` and `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-bandwidth` annotations are required. The values of the annotations provided in this topic are only examples. Replace the values based on your requirements.

        **Note:** The service.beta.kubernetes.io/alibaba-cloud-loadbalancer-bandwidth annotation specifies the maximum bandwidth value.

        ```
        apiVersion: v1
        kind: Service
        metadata:
          annotations:
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-charge-type: "paybybandwidth"
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-bandwidth: "2"
          name: nginx
          namespace: default
        spec:
          ports:
          - port: 443
            protocol: TCP
            targetPort: 443
          selector:
            run: nginx
          type: LoadBalancer
        ```

-   Specify the name of an SLB instance.

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-name: "your-svc-name"
      name: nginx
    spec:
      externalTrafficPolicy: Local
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        app: nginx
      type: LoadBalancer
    ```

-   Use an existing SLB instance.
    -   By default, Kubernetes LoadBalancer creates an SLB instance to provide load balancing services. To use an existing SLB instance, specify the ID of the SLB instance in the `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id` annotation. In the following example, a LoadBalancer type Kubernetes service is created based on an existing SLB instance.

        ```
        apiVersion: v1
        kind: Service
        metadata:
          annotations:
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id: "${YOUR_LOADBALACER_ID}"
          name: nginx
          namespace: default
        spec:
          ports:
          - port: 443
            protocol: TCP
            targetPort: 443
          selector:
            run: nginx
          type: LoadBalancer
        ```

    -   By default, the existing listeners are not overwritten when you choose an existing SLB instance. To overwrite the existing listeners, set `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-force-override-listeners` to `true`.

        **Note:** By default, the existing listeners are not overwritten when you choose an existing SLB instance. You can forcibly overwrite the listeners if the ports on which these listeners listen are no longer used. We recommend that you do not overwrite existing listeners in the following scenarios:

        -   If the current SLB instance is associated with a service, after you overwrite the listeners of the SLB instance, a service disruption may occur.
        -   CCM supports limited backend configurations and cannot process complex configurations. If you require complex backend configurations, you can manually configure listeners in the SLB console without overwriting the existing listeners.
    -   If you use an existing SLB instance and forcibly overwrite the existing listeners, the existing listeners are deleted when listener port conflicts occur.

        ```
        apiVersion: v1
        kind: Service
        metadata:
          annotations:
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id: "${YOUR_LOADBALACER_ID}"
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-force-override-listeners: "true"
          name: nginx
          namespace: default
        spec:
          ports:
          - port: 443
            protocol: TCP
            targetPort: 443
          selector:
            run: nginx
          type: LoadBalancer
        ```

-   Other configurations.

    In addition to the preceding commonly used configurations, you can modify the configurations of the SLB instance managed by a Kubernetes service in a fine-grained manner. For more information, see [Use annotations to configure SLB instances](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Use annotations to configure SLB instances.md).


