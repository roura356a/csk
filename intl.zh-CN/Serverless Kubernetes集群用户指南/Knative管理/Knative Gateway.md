# Knative Gateway

Kative Gateway具备多种Gateway实现方案，并且属于云产品级别的支撑，不需要常驻资源。不仅可以帮助您节省laaS成本还省去了很多运维负担。

## Knative Gateway优势

社区Knative默认支持Istio、Gloo、Contour、Kourier和Ambassador等多种Gateway实现方案。在这些实现方案中Istio使用频率最高。因为Istio除了可以充当Gateway的角色还能做为ServiceMesh服务使用。Serverless服务首先需要有Gateway实例常驻运行，而且为了保证高可用至少要有两个实例互为备份。其次这些Gateway的k8s Controller也需要常驻运行，这些常驻实例的IaaS费用和运维都是业务需要支付的成本。

ASK Knative使用SLB作为Gateway。除了HTTP以外，Gateway还提供HTTPS功能。Knative默认给Gateway生成一个自签名的HTTPS证书，没有域名限制，可用于测试。在使用Knative部署线上服务时，您需要创建HTTPS证书，并将Knative Gateway Service的证书ID修改为创建的证书ID，详细介绍请参见[t1960067.md\#](/intl.zh-CN/Serverless Kubernetes集群用户指南/Knative管理/配置HTTPS证书.md)。

为了极致的Serverless体验，通过阿里云SLB实现了Knative Gateway。Knative Gateway具备所有需要的功能并且属于云产品级别的支撑，不需要常驻资源。不仅节省了您的IaaS成本还省去了很多运维负担。

![1](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/3903222061/p171606.png)

## 使用Knative Gateway

1.  获取SLB的IP地址。

    输入以下命令：

    ```
    kubectl -n knative-serving get svc
    ```

    系统输出类似如下结果：

    ```
    NAME              TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                      AGE
    ingress-gateway   LoadBalancer   172.19.1*.***   106.15.2**.**   80:32185/TCP,443:31137/TCP   69d
    ```

2.  访问服务。

    -   通过HTTP访问服务。

        输入以下命令：

        ```
        curl -H  "Host: coffee.default.example.com" http://39.106.***.*
        ```

        系统输出类似如下结果：

        ```
        Hello Knative!
        ```

    -   通过HTTPS访问服务。

        输入以下命令：

        ```
        curl -H  "Host: coffee.default.example.com" https://39.106.***.* -k
        ```

        系统输出类似如下结果：

        ```
        Hello Knative!
        ```

3.  将SLB的IP地址与Knative域名进行Host绑定，在Hosts文件中添加绑定信息。以下为绑定样例。

    **说明：** Knative默认的路由根域名是example.com，您可以自定义域名，详细介绍请参见[t1095409.md\#](/intl.zh-CN/Kubernetes集群用户指南/Knative管理/Knative服务管理/Knative自定义域名.md)。

    ```
    106.15.2**.** helloworld-go.default.example.com
    ```

    完成Hosts绑定后，如果您可以通过域名访问服务，表示Knative Gateway可以正常使用。

    ![1](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/3375621061/p170859.png)


## 修改SLB Gateway 配置

Knative Gateway默认会通过公网SLB暴露服务。如果您的服务只能对VPC网络开放，您可以指定使用VPC内网SLB。除了SLB类型以外您还可以指定负载均衡规格、使用已有负载均衡、创建负载均衡时，指定主备可用区、创建按带宽付费的负载均衡、指定负载均衡名称。

Knative Gateway是通过Knative-Serving Namespace中的Ingress-Gateway K8s Service管理SLB的，最终通过阿里云的Cloud Controller Manager 实现Service和SLB的映射。以下操作都是通过Ingress-Gateway K8s Service实现。您可以通过`kubectl -n knative-serving edit svc ingress-gateway`修改Annotation，从而达到修改SLB配置的目的。

-   创建私有网络类型（VPC）的负载均衡。

    创建私有网络类型的负载均衡，需要通过`service.beta.kubernetes.io/alibaba-cloud-loadbalancer-address-type: "intranet"`指明是内网SLB，通过`service.beta.kubernetes.io/alibaba-cloud-loadbalancer-network-type: "vpc"`指明是VPC类型的内网SLB。

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

    VPC内网SLB如果想要对公网服务，需要为SLB绑定EIP，详细介绍请参见[t16155.md\#](/intl.zh-CN/用户指南/实例/绑定EIP.md)。

-   指定负载均衡规格。

    您可以调用CreateLoadBalancer创建指定规格的SLB，或者更新已有SLB的规格，详细介绍请参见[CreateLoadBalancer](https://help.aliyun.com/document_detail/27577.html#doc-api-Slb-CreateLoadBalancer)。

    **说明：** 如果您是通过SLB控制台修改SLB规格，可能会被CCM修改回原规格，请谨慎操作。

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

-   创建负载均衡时，指定主备可用区。
    -   某些地域的负载均衡不支持主备可用区，例如ap-southeast-5。
    -   创建负载均衡实例指定主备可用区后，主备可用区将不支持修改。

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

-   创建按带宽付费的负载均衡。
    -   仅支持公网类型的负载均衡实例，其他限制请参见[t4098.md\#]()。
    -   `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-charge-type`和`service.beta.kubernetes.io/alibaba-cloud-loadbalancer-bandwidth`两项Annotation为必选参数。此处提供的是示例值，请根据实际业务需要自行设置。

        **说明：** service.beta.kubernetes.io/alibaba-cloud-loadbalancer-bandwidth为带宽峰值。

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

-   指定负载均衡名称。

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

-   使用已有的负载均衡。
    -   Kubernetes LoadBalancer默认会创建新的SLB提供负载均衡服务。如果需要指定已有的SLB， 可以通过 `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id` Annotation指定已有SLB ID。以下示例为指定已有SLB创建一个LoadBalancer类型的K8s Service 。

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

    -   默认情况下，使用已有的负载均衡实例，不会覆盖监听。如要强制覆盖已有监听，请设置`service.beta.kubernetes.io/alibaba-cloud-loadbalancer-force-override-listeners`为`true`。

        **说明：** 使用已有的负载均衡，默认不覆盖已有监听。如果已有负载均衡的监听端口不再使用，则可以强制覆盖。如存在以下两种情况不建议强制覆盖监听。

        -   如果已有负载均衡的监听上绑定了业务，强制覆盖可能会引发业务中断。
        -   由于CCM目前支持的后端配置有限，无法处理一些复杂配置。如果有复杂的后端配置需求，可以在不覆盖监听的情况下，通过控制台自行配置监听。
    -   使用已有的负载均衡并强制覆盖已有监听，如果监听端口冲突，则会删除已有监听。

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

-   其他操作。

    除了上述这些常用的操作以外您还可以对Kubernetes Service管理的SLB进行更精细的控制，详细介绍请参见[t16677.md\#](/intl.zh-CN/Kubernetes集群用户指南/网络管理/Service管理/通过Annotation配置负载均衡.md)。


