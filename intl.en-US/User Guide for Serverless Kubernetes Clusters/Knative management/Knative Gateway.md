# Knative Gateway

Knative Gateway provides multiple cloud gateway solutions that require no resident resources. These solutions are as stable and reliable as cloud services. Knative Gateway helps you reduce infrastructure costs and O&M workloads.

## Benefits of Knative Gateway

By default, the Knative community version supports multiple gateway solutions, such as Istio, Gloo, Contour, Kourier, and Ambassador. Among these solutions, Istio is most frequently used. This is because Istio also functions as a service mesh. Each ASK cluster must contain at least two resident gateway instances. The two instances provide backup for each other to ensure high availability. The gateway controllers must be resident. You must pay for infrastructures and O&M of these resident instances.

To improve user experience, Alibaba Cloud provides Knative Gateway based on Server Load Balancer \(SLB\). This feature provides gateway capabilities required by ASK clusters and is as stable and reliable as cloud services. No resident resources are required. Therefore, Knative Gateway reduces infrastructure costs and O&M workloads.

![infrastructures](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7326963061/p176378.png)

## Use Knative Gateway

1.  Query the IP address of the SLB instance.

    ```
    kubectl -n knative-serving get svc
    ```

    ```
    NAME              TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                      AGE
    ingress-gateway   LoadBalancer   172.19.1*. ***   106.15.2**. **   80:32185/TCP,443:31137/TCP   69d
    ```

2.  Bind the IP address of the SLB instance to the domain name of Knative by modifying the hosts file. The following example shows how to bind them.

    **Note:** The default root domain name of Knative is example.com. You can customize a domain name. For more information, see [Use a custom domain name in Knative](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative services/Use a custom domain name in Knative.md).

    ```
    106.15.2**.** helloworld-go.default.example.com
    ```

    After you modify the hosts file, use the domain name to access Knative Gateway.

    ![host](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7326963061/p176379.png)


