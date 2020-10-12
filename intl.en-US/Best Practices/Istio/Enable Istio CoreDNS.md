# Enable Istio CoreDNS

This topic provides an overview of CoreDNS and its plug-ins and the CoreDNS configuration file, and describes how to enable Istio CoreDNS.

## CoreDNS and its plug-ins

CoreDNS is an incubation project led by Cloud Native Computing Foundation \(CNCF\). Formerly known as SkyDNS, the project aims to build a fast and flexible Domain Name System \(DNS\) server that allows users to access and use data in DNS in different ways. Based on the Caddy server framework, CoreDNS adopts a plug-in chain architecture that provides a large number of plug-ins. Each plug-in implements a DNS feature, such as DNS-based Kubernetes service discovery and Prometheus monitoring.

In addition to plug-ins, CoreDNS has the following benefits:

-   Simplified configuration: CoreDNS introduces an expressive domain-specific language \(DSL\). CoreDNS uses the Corefile configuration file, which is also developed based on the Caddy framework.
-   All-in-one solution: CoreDNS provides a simpler solution than kube-dns. CoreDNS is compiled into a binary executable file that has built-in features such as cache, backend storage, and health check. CoreDNS does not require third-party components to implement features. This enables simple deployment and secure memory management.

## Corefile overview

Corefile is the configuration file of CoreDNS. The file is developed based on the Caddyfile configuration file of the Caddy framework. The file includes the following content:

-   The port on which the server listens and the protocol that the server uses in port listening. You can specify different ports that multiple servers listen on.
-   The zone where the server is used as the authoritative DNS server.
-   The plug-ins to be loaded by the server.

The following content shows the typical format of a Corefile:

```
ZONE:[PORT] {
    [PLUGIN] ...
}
```

The following table describes the parameters in the Corefile configuration file.

|Parameter|Description|
|---------|-----------|
|ZONE|The zone where the server is used to resolve domain names.|
|PORT|The listening port. The parameter is optional. Default value: 53.|
|PLUGIN|The plug-ins to be loaded by the server. Each plug-in can have multiple parameters.|

## How the plug-ins work

After you start CoreDNS, CoreDNS starts servers based on the configuration file. Each server has its own chain of plug-ins. A DNS request is processed based on the following logic:

-   If the requested server has multiple zones, the zone that best matches the request is selected by using the greedy principle.
-   CoreDNS runs the plug-ins based on the order defined by plugin.cfg.
-   Plug-ins determine whether to process the request. The following possibilities exist:
    -   The current plug-in determines to process the request.

        The current plug-in generates a response and returns it to the client. The request ends and the next plug-in, such as whoami, will not be called.

    -   The current plug-in determines not to process the request.

        The next plug-in is called. If the last plug-in fails to process the request, the server returns a SERVFAIL response.

    -   The current plug-in processes the request in the form of Fallthrough.

        The scenario where a request that is being processed is redirected to the next plug-in is called a fallthrough. The fallthrough keyword is used to determine whether to allow this operation. For example, when the host plug-in is processing a request, if the queried domain name is not located in /etc/hosts, the next plug-in is called.

        -   The request is processed and hints are added to its response.
        -   The request is processed by the current plug-in and continues to be processed by the next plug-in after information such as a hint is added to its response. The additional information is included in the response to the client. For example, the metric plug-in adds additional information to the response.

## CoreDNS and Kubernetes

In Kubernetes 1.11 and later, CoreDNS has reached General Availability as a DNS plug-in for Kubernetes. We recommend that you use CoreDNS for DNS resolution in Kubernetes clusters. In Alibaba Cloud Container Service for Kubernetes 1.11.5 and later, CoreDNS is the default DNS plug-in.

You can run the `kubectl -n kube-system get configmap coredns -oyaml` command to view the CoreDNS configurations.

```
apiVersion: v1
data:
  Corefile: |-
    .:53 {
        errors
        health
        kubernetes cluster.local in-addr.arpa ip6.arpa {
           pods insecure
           upstream
           fallthrough in-addr.arpa ip6.arpa
        }
        prometheus :9153
        proxy . /etc/resolv.conf
        cache 30
        reload
    }
kind: ConfigMap
metadata:
  creationTimestamp: "2018-12-28T07:28:34Z"
  name: coredns
  namespace: kube-system
  resourceVersion: "2453430"
  selfLink: /api/v1/namespaces/kube-system/configmaps/coredns
  uid: 2f3241d5-0a72-11e9-99f1-00163e105bdf
```

The following content describes the parameters in the configuration file:

|Parameter|Description|
|---------|-----------|
|errors|The errors that are generated when plug-ins process requests. Errors are included in the standard output.|
|health|The health status of CoreDNS. You can view the status at http://localhost:8080/health.|
|kubernetes|Responds to DNS query requests based on the IP address of the service. The default cluster domain is cluster.local.|
|prometheus|Monitoring data in prometheus format, which can be obtained at http://localhost:9153/metrics.|
|proxy|If the local DNS server cannot resolve a domain name, the DNS resolution request is sent to the upper-level DNS server. The default value is /etc/resolv.conf.|
|cache|The time used for caching.|

## Enable Istio CoreDNS

In the default Istio configuration, Istio CoreDNS is not enabled to perform DNS resolution. After you install Istio, you can update the configuration to enable Istio CoreDNS.

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  Log on to the ACK console. In the left-side navigation pane, choose **Service Mesh** \> **Istio Management**.

3.  Click **Update** in the upper-right corner. The Update Release page appears.

4.  Find `istioCoreDNS` and set the value of `enabled` to true. Then click **Update**.

    If you cannot find the istioCoreDNS parameter, you can add the following lines in YAML format to the beginning of the configuration file on the Update Release page.

    ```
      istioCoreDNS:
        enabled: ture
    ```


## View the CoreDNS configuration in the cluster

1.  Run the `kubectl get svc istiocoredns -n istio-system` command to obtain the cluster IP of istiocoredns.

    ```
    NAME           TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)         AGE
    istiocoredns   ClusterIP   172.19.XX.XX   <none>         53/UDP,53/TCP   44m
    ```

    View the configmap configuration item of CoreDNS in the cluster. You can see that the istiocoredns service is added to configmap as the upstream DNS service of the \*.global domain, as shown in the following content.

    ```
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: coredns
      namespace: kube-system
    data:
      Corefile: |-
        .:53 {
            errors
            health
            kubernetes cluster.local in-addr.arpa ip6.arpa {
               pods insecure
               upstream
               fallthrough in-addr.arpa ip6.arpa
            }
            prometheus :9153
            proxy . /etc/resolv.conf
            cache 30
            reload
        }
        global:53 {
             errors
             cache 30
             proxy . {cluster IP of this istiocoredns service}
        }
    ```

2.  The CoreDNS container in the cluster reloads the configuration content. You can run the following commands to view the loading logs:

    ```
    kubectl get -n kube-system pod | grep coredns
    ```

    ```
    coredns-8645f4b4c6-5frkg                                     1/1     Running   0          20h
    coredns-8645f4b4c6-lj59t                                     1/1     Running   0          20h
    ```

    ```
    kubectl logs -f -n kube-system coredns-8645f4b4c6-5frkg
    ```

    ```
    ....
    2019/01/08 05:06:47 [INFO] Reloading
    2019/01/08 05:06:47 [INFO] plugin/reload: Running configuration MD5 = 05514b3e44bcf4ea805c87cc6cd56c07
    2019/01/08 05:06:47 [INFO] Reloading complete
    ```

    ```
    kubectl logs -f -n kube-system coredns-8645f4b4c6-lj59t
    ```

    ```
    ....
    2019/01/08 05:06:31 [INFO] Reloading
    2019/01/08 05:06:32 [INFO] plugin/reload: Running configuration MD5 = 05514b3e44bcf4ea805c87cc6cd56c07
    2019/01/08 05:06:32 [INFO] Reloading complete
    ```


## Create ServiceEntry to verify DNS resolution

You can use ServiceEntry to add additional entries to the service registry within Istio. This allows services that are automatically discovered in the mesh to access and route to manually added services. ServiceEntry describes the attributes of a service, including the domain name, virtual IP address, port number, protocol, and endpoint. The services may be APIs outside the mesh or services that are deployed within the mesh but are not listed in the service registry. For example, the services may be a group of VM-based services that need to communicate with Kubernetes-based services.

In the following example, wildcard characters are used to define hosts, and the address is specified:

1.  [Connect to a Kubernetes cluster by using kubectl](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Cluster management/Connect to a Kubernetes cluster by using kubectl.md).

2.  Create the external-svc-serviceentry.yaml file and copy the following content to the file. Run the `kubectl apply -f external-svc-serviceentry.yaml` command to create a ServiceEntry.

    ```
    apiVersion: networking.istio.io/v1alpha3
    kind: ServiceEntry
    metadata:
      name: external-svc-serviceentry
    spec:
      hosts:
      - '*.test.global'
      addresses:
      - 127.255.0.2
      ports:
      - number: 8080
        name: http1
        protocol: HTTP
      location: MESH_INTERNAL
      resolution: DNS
      endpoints:
      - address: 47.111. **.**
        ports:
          http1: 15443
    ```

3.  Run the following command to view the istiocoredns container. The command output shows that the domain name mappings for the new ServiceEntry have been loaded.

    ```
    kubectl get po -n istio-system | grep istiocoredns
    ```

    ```
    istiocoredns-cdc56b67-ngtkr                 2/2     Running   0          1h
    ```

    ```
    kubectl logs --tail 2 -n istio-system istiocoredns-cdc56b67-ngtkr -c istio-coredns-plugin
    ```

    ```
    2019-01-08T05:27:22.897845Z    info    Have 1 service entries
    2019-01-08T05:27:22.897888Z    info    adding DNS mapping: .test.global. ->[127.255.XX.XX]
    ```

4.  Run the following command to create a test container by using the tutum/dnsutils image:

    ```
    kubectl run dnsutils -it --image=tutum/dnsutils bash
    ```

5.  After you open the CLI, run the following dig command to view the result of domain name resolution:

    ```
    dig +short 172.19.XX.XX (Cluster IP of the DNS service) A service1.test.global
    ```

    ```
    127.255.XX.XX
    ```

    ```
    dig +short 172.19.XX.XX (Cluster IP of the DNS service) A service2.test.global
    ```

    ```
    127.255.XX.XX
    ```


