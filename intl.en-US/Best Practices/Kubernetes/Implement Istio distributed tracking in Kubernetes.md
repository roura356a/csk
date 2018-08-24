# Implement Istio distributed tracking in Kubernetes {#concept_q3c_4hd_5db .concept}

## Background {#section_h1c_phd_5db .section}

Microservice is a focus in the current era. More and more IT enterprises begin to embrace the microservices. The microservice architecture splits a complex system into several small services and each service can be developed, deployed, and scaled independently. As a heaven-made match, the microservice architecture and containers \(Docker and Kubernetes\) further simplify the microservice delivery and strengthen the flexibility and robustness of the entire system.

When monolithic applications are transformed to microservices, the distributed application architecture composed of a large number of microservices also increases the complexity of operation & maintenance, debugging, and security management. As microservices grow in scale and complexity, developers must be faced with complex challenges such as service discovery, Server Load Balancer, failure recovery, indicator collection, monitoring, A/B testing, throttling, access control, and end-to-end authentication, which are difficult to resolve.

In May 2017, Google, IBM, and Lyft published the open-source service network architecture Istio, which provides the connection, management, monitoring, and security protection of microservices. Istio provides an infrastructure layer for services to communicate with each other, decouples the issues such as version management, security protection, failover, monitoring, and telemetry in application logics and service access. Being unrelated to codes, Istio attracts enterprises to transform to microservices, which will make the microservice ecology develop fast.

## Architecture principle of Istio {#section_i1c_phd_5db .section}

In Kubernetes, a pod is a collection of close-coupled containers, and these containers share the same network namespace. With the extension mechanism of Initializer in Kubernetes, an Envoy container is automatically created and started for each business pod, without modifying the deployment description of the business pod. The Envoy takes over the inbound and outbound traffic of business containers in the same pod. Therefore, the microservice governance functions, including the traffic management, microservice tracking, security authentication, access control, and strategy implementation, are realized by operating on the Envoy.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7252/15350778591151_en-US.png)

An Istio service mesh is logically split into a data plane and a control plane.

-   The data plane is composed of a collection of intelligent proxies \(Envoys\) deployed as sidecars that mediate and control all network communication between microservices.
-   The control plane is used to manage and configure the proxies to route traffic, and enforce polices at the runtime.

An Istio is mainly composed of the following components:

-   **Envoy:** The Envoy is used to mediate all the inbound and outbound traffic for all the services in the service mesh. Functions such as dynamic service discovery, Server Load Balancer, fault injection, and traffic management are supported. The Envoy is deployed as a sidecar to the pods of related services.
-   **Pilot:** The Pilot is used to collect and verify the configurations and distribute the configurations to all kinds of Istio components.
-   **Mixer:** The Mixer is used to enforce the access control and usage policies in the service mesh, and collect telemetry data from Envoy proxies and other services.
-   **Istio-Auth:** Istio-Auth provides strong service-to-service and end user authentication.

For more information about Istio, see the [Istio official document](https://istio.io/docs/concepts/what-is-istio/overview.html).

## Install Istio {#section_m1c_phd_5db .section}

Use an Alibaba Cloud Container Service Kubernetes cluster as an example.

Alibaba Cloud Container Service has enabled the Initializers plug-in by default for Kubernetes clusters if the cluster version is later than 1.8. No other configurations are needed.

**Note:** After you deploy the Istio, a sidecar is injected to each pod to take over the service communication. Therefore, we recommend that you verify this in the independent test environment.

**Create a Kubernetes cluster**

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
2.  Under Kubernetes, click **Clusters** in the left-side navigation pane. Click **Create Kubernetes Cluster** in the upper-right corner.
3.  Configure the parameters to create a cluster. For how to create a Kubernetes cluster, see [Create a cluster](../../../../intl.en-US/User Guide/Kubernetes cluster.md#).
4.  After the cluster is created, click **Manage** at the right of the cluster when the cluster status is changed to **Running**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7252/15350778591153_en-US.png)

5.  On the cluster Basic Information page, you can configure the corresponding connection information based on the page information. You can connect to the cluster either by using [kubectl](../../../../intl.en-US/User Guide/Kubernetes cluster/Clusters/Connect to a Kubernetes cluster by using kubectl.md#) or [SSH](../../../../intl.en-US/User Guide/Kubernetes cluster/Clusters/Access Kubernetes clusters by using SSH.md#).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7252/15350778591154_en-US.png)


**Deploy Istio release version**

Log on to the master node and run the following command to get the latest Istio installation package.

```
curl -L https://git.io/getLatestIstio | sh -
```

Run the following command:

```
cd istio-0.4.0 ##Change the working directory to Istio. 
Istioexport PATH=$PWD/bin:$PATH ##Add the istioctl client to PATH environment variable.
```

Run the following command to deploy Istio.

```
kubectl apply -f install/kubernetes/istio.yaml ## Deploy Istio system components.
kubectl apply -f install/kubernetes/istio-initializer.yaml # Deploy Istio initializer plug-in.
```

After the deployment, run the following command to verify if the Istio components are successfully deployed.

```
$ kubectl get svc,pod -n istio-systemNAME TYPE CLUSTER-IP EXTERNAL-IP PORT(S) AGEsvc/istio-ingress LoadBalancer 172.21.10.18 101.37.113.231 80:30511/TCP,443:31945/TCP 1msvc/istio-mixer ClusterIP 172.21.14.221 9091/TCP,15004/TCP,9093/TCP,9094/TCP,9102/TCP,9125/UDP,42422/TCP 1msvc/istio-pilot ClusterIP 172.21.4.20 15003/TCP,443/TCP 1mNAME READY STATUS RESTARTS AGEpo/istio-ca-55b954ff7-crsjq 1/1 Running 0 1mpo/istio-ingress-948b746cb-4t24c 1/1 Running 0 1mpo/istio-initializer-6c84859cd-8mvfj 1/1 Running 0 1mpo/istio-mixer-59cc756b48-tkx6c 3/3 Running 0 1mpo/istio-pilot-55bb7f5d9d-wc5xh 2/2 Running 0 1m
```

After all the pods are in the running status, the Istio deployment is finished.

## Istio distributed service tracking case {#section_w1c_phd_5db .section}

**Deploy and test the application BookInfo**

BookInfo is an application similar to an online bookstore, which is composed of several independent microservices compiled by different languages. The application BookInfo is deployed in the container mode and does not have any dependencies on Istio. All the microservices are packaged together with an Envoy sidecar. The Envoy sidecar intercepts the inbound and outbound call requests of services to demonstrate the distributed tracking function of Istio service mesh.

For more information about BookInfo, see [Bookinfo guide](https://istio.io/docs/guides/bookinfo.html).

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7252/15350778591155_en-US.png)

Run the following command to deploy and test the application Bookinfo.

```
kubectl apply -f samples/bookinfo/kube/bookinfo.yaml
```

In the Alibaba Cloud Kubernetes cluster environment, every cluster has been configured with the Server Load Balancer and Ingress. Run the following command to obtain the IP address of Ingress.

```
$ kubectl get ingress -o wide
NAME HOSTS ADDRESS PORTS AGE
gateway * 101.37.xxx.xxx 80 2m
```

If the preceding command cannot obtain the external IP address, run the following command to obtain the corresponding address.

```
export GATEWAY_URL=$(kubectl get ingress -o wide -o jsonpath={.items[0].status.loadBalancer.ingress[0].ip})
```

The application is successfully deployed if the following command returns 200.

```
curl -o /dev/null -s -w "%{http_code}\n" http://${GATEWAY_URL}/productpage
```

You can open `http://${GATEWAY_URL}/productpage` in the browser to access the application. GATEWAY\_URL is the IP address of Ingress.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7252/15350778591156_en-US.png)

**Deploy Jaeger tracking system**

Distributed tracking system helps you observe the call chains between services and is useful when diagnosing performance issues and analyzing system failures.

Istio ecology supports different distributed tracking systems, including [Zipkin](https://github.com/jaegertracing/jaeger) and [Jaeger](https://github.com/jaegertracing/jaeger). Use the Jaeger as an example.

Istio version 0.4 supports Jaeger. The test method is as follows.

```
kubectl apply -n istio-system -f https://raw.githubusercontent.com/jaegertracing/jaeger-kubernetes/master/all-in-one/jaeger-all-in-one-template.yml
```

After the deployment is finished, if you connect to the Kubernetes cluster by using kubectl, run the following command to access the Jaeger control panel by using port mapping and open [http://localhost:16686](http://localhost:16686/) in the browser.

```
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=jaeger -o jsonpath='{.items[0].metadata.name}') 16686:16686 &
```

If you connect to the Alibaba Cloud Kubernetes cluster by using SSH, run the following command to check the external access address of jaeger-query service.

```
$ kubectl get svc -n istio-system
NAME TYPE CLUSTER-IP EXTERNAL-IP PORT(S) AGE
jaeger-agent ClusterIP None <none> 5775/UDP,6831/UDP,6832/UDP 1h
jaeger-collector ClusterIP 172.21.10.187 <none> 14267/TCP,14268/TCP,9411/TCP 1h
jaeger-query LoadBalancer 172.21.10.197 114.55.82.11 80:31960/TCP ##The external access address is 114.55.82.11:80.
zipkin ClusterIP None <none> 9411/TCP
```

Record the external access IP address and port of jaeger-query and then open the application in the browser.

By accessing the application BookInfo for multiple times and generating the call chain information, we can view the call chain information of services clearly.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7252/15350778601159_en-US.png)

Click a specific Trace to view the details.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7252/15350778601160_en-US.png)

You can also view DAG.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7252/15350778601163_en-US.png)

## Implementation principle of Istio distributed tracking {#section_kbc_phd_5db .section}

The kernel of Istio service mesh is the Envoy, which is a high-performance and open-source Layer-7 proxy and communication bus. In Istio, each microservice is injected with an Envoy sidecar and this instance is responsible for processing all the inbound and outbound network traffic. Therefore, each Envoy sidecar can monitor all the API calls between services, record the time required by each service call, and record whether each service call is successful or not.

Whenever a microservice initiates an external call, the client Envoy will create a new span. A span represents the complete interaction process between a collection of microservices, starting from a caller \(client\) sending a request to receiving the response from the server.

In the service interaction process, clients record the request start time and response receipt time, and the Envoy on the server records the request receipt time and response return time.

Each Envoy distributes their own span view information to the distributed tracking system. When a microservice processes requests, other microservices may need to be called, which causes the creation of a causally related span and then forms the complete trace. Then, an application must be used to collect and forward the following Headers from the request message:

-   `x-request-id`
-   `x-b3-traceid`
-   `x-b3-spanid`
-   `x-b3-parentspanid`
-   `x-b3-sampled`
-   `x-b3-flags`
-   `x-ot-span-context`

Envoys in the communication links can intercept, process, and forward the corresponding Headers.

```
Client Tracer Server Tracer
┌──────────────────┐ ┌──────────────────┐
│ │ │ │
│ TraceContext │ Http Request Headers │ TraceContext │
│ ┌──────────────┐ │ ┌───────────────────┐ │ ┌──────────────┐ │
│ │ TraceId │ │ │ X─B3─TraceId │ │ │ TraceId │ │
│ │ │ │ │ │ │ │ │ │
│ │ ParentSpanId │ │ Extract │ X─B3─ParentSpanId │ Inject │ │ ParentSpanId │ │
│ │ ├─┼─────────>│ ├────────┼>│ │ │
│ │ SpanId │ │ │ X─B3─SpanId │ │ │ SpanId │ │
│ │ │ │ │ │ │ │ │ │
│ │ Sampled │ │ │ X─B3─Sampled │ │ │ Sampled │ │
│ └──────────────┘ │ └───────────────────┘ │ └──────────────┘ │
│ │ │ │
└──────────────────┘ └──────────────────┘
```

For specific codes, see the [Istio document](https://istio.io/docs/tasks/telemetry/distributed-tracing.html).

## Conclusion {#section_qbc_phd_5db .section}

Istio is accelerating the application and popularization of service mesh by using the good expansion mechanism and strong ecology. In addition to those mentioned in the preceding sections, Weave Scope, Istio Dashboard, and Istio-Analytics projects provide abundant call link visualization and analysis capabilities.

