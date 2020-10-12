# Enable Istio CoreDNS

## CoreDNS and its plug-ins

CoreDNS is an incubation project led by CNCF. Formerly known as SkyDNS, it aims to build a fast and flexible DNS server that provides users with various ways to access and use DNS data. Based on the Caddy server framework, CoreDNS implements a plug-in chain architecture that abstracts large amounts of logic into plug-ins and then exposes these plug-ins to users. Each plug-in performs different DNS functions, such as DNS service discovery and Prometheus monitoring.

In addition to pluginization, CoreDNS has the following features:

-   Simplified configuration: The introduction of a more expressive DSL, namely Corefile configuration file, which is also developed based on the Caddy framework.
-   Integrated solution: Unlike kube-dns, CoreDNS is compiled into single executable binaries with built-in functions such as Cache, Backend Storage, and Health Check. No third-party components are required to implement other functions, making deployment more convenient and memory management safer.

## Corefile overview

Corefile is the configuration file of CoreDNS. It was originated from the Caddyfile configuration file based on the Caddy framework. It defines the following content:

-   The protocol that decides the port on which the server listens. Multiple servers can be defined to listen to different ports.
-   The authoritative DNS resolution zone for which the server is responsible.
-   The plug-ins to be loaded by the server.

A typical Corefile format is as follows:

```
ZONE:[PORT] {
    [PLUGIN] ...
}
```

The parameters are described as follows.

|Parameter|Description|
|---------|-----------|
|ZONE|The zone for which the server is responsible.|
|PORT|Optional. The listening port. Default value: 53.|
|PLUGIN|The plug-ins to be loaded by the server. Each plug-in can have multiple parameters.|

## How the plug-ins work

When CoreDNS is started, it starts different servers according to the configuration file. Each server has its own chain of plug-ins. When a DNS request is received, the request goes through the following three steps in turn:

-   If the currently requested server has multiple zones, the greedy principle is adopted to select the zone that best matches.
-   Once a matching server is found, plug-ins in the plug-in chain are executed in the order defined by plugin.cfg.
-   Each plug-in determines whether the current request need to be processed. There are several possibilities:
    -   The request is processed by the current plug-in.

        The plug-in generates the corresponding response and returns it to the client, whereupon the request ends. The next plug-in, such as, whoami, is not called.

    -   The request is not processed by the current plug-in.

        The next plug-in is called. If the last plug-in fails to process the request, the server returns a SERVFAIL response.

    -   The request is processed by the current plug-in in the form of Fallthrough.

        If the request might jump to the next plug-in while it is being processed by the current plug-in, this process is called Fallthrough. The fallthrough keyword is used to decide whether to allow this operation. For example, for the host plug-in, if the queried domain name is not located in /etc/hosts, the next plug-in is called.

        -   The request is processed with hints.
        -   The request is processed by the plug-in and continues to be processed by the next plug-in after some information \(hints\) is added to its response. The additional information is used to form the final response to the client.

## CoreDNS and Kubernetes

Beginning Kubernetes 1.11, CoreDNS reached General Availability as a DNS plug-in for Kubernetes. Kubernetes recommends using CoreDNS as the DNS service within clusters. In Alibaba Cloud Container Service for Kubernetes 1.11.5 and later versions, CoreDNS is installed as the DNS service by default.

Run the `kubectl -n kube-system get configmap coredns -oyaml` command to view CoreDNS configuration.

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

The parameters in the configuration file are as follows:

|Parameter|Description|
|---------|-----------|
|errors|Errors are logged to the standard output.|
|health|Health status can be viewed at http://localhost:8080/health.|
|kubernetes|Responds to DNS query requests based on the IP of the service. The cluster domain defaults to cluster.local.|
|prometheus|Monitoring data in prometheus format can be obtained at http://localhost:9153/metrics.|
|proxy|If the domain name cannot be resolved locally, the upper address is queried. The /etc/resolv.conf configuration of the host is used by default.|
|cache|Cache time|

## Enable Istio CoreDNS

In the default Istio configuration, Istio CoreDNS is not enabled to perform DNS resolution. You can update the configuration to enable Istio CoreDNS after Istio is installed.

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the Kubernetes menu, choose **Service Mesh** \> **Istio Management** in the left-side navigation pane.

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

    View the configmap configuration item of CoreDNS in the cluster. You can see that the istiocoredns service is added to configmap as the upstream DNS service of the \*.global domain as follows:

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

2.  The CoreDNS container in the cluster reloads the configuration content. You can run the following command to view the loading log.

    ```
    # kubectl get -n kube-system pod | grep coredns
    coredns-8645f4b4c6-5frkg                                     1/1     Running   0          20h
    coredns-8645f4b4c6-lj59t                                     1/1     Running   0          20h
    
    # kubectl logs -f -n kube-system coredns-8645f4b4c6-5frkg
    ....
    2019/01/08 05:06:47 [INFO] Reloading
    2019/01/08 05:06:47 [INFO] plugin/reload: Running configuration MD5 = 05514b3e44bcf4ea805c87cc6cd56c07
    2019/01/08 05:06:47 [INFO] Reloading complete
    
    # kubectl logs -f -n kube-system coredns-8645f4b4c6-lj59t
    ....
    2019/01/08 05:06:31 [INFO] Reloading
    2019/01/08 05:06:32 [INFO] plugin/reload: Running configuration MD5 = 05514b3e44bcf4ea805c87cc6cd56c07
    2019/01/08 05:06:32 [INFO] Reloading complete
    ```


## Create ServiceEntry to verify DNS resolution

With ServiceEntry, additional entries can be added to the service registry within Istio. This allows services that are automatically discovered in the mesh to access and route to these manually added services. ServiceEntry describes the attributes of a service, including the DNS name, virtual IP address, port, protocol, and endpoint. The type of service may be an API outside the mesh, or an entry in the service registry within the mesh but not in the platform. For example, a group of VM-based services that need to communicate with the Kubernetes service.

In this example, wildcard characters are used to define hosts and the address is specified as follows:

1.  [Connect to a Kubernetes cluster by using kubectl](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Cluster management/Connect to a Kubernetes cluster by using kubectl.md).

2.  Create an external-svc-serviceentry.yaml file with the following content. Run the `kubectl apply -f external-svc-serviceentry.yaml` command to create a ServiceEntry.

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
      - address: 47.111.38.80
        ports:
          http1: 15443
    ```

3.  Run the following command to view the istiocoredns container. You can see that the domain mapping relationship for the above ServiceEntry has been loaded.

    ```
    # kubectl get po -n istio-system | grep istiocoredns
    istiocoredns-cdc56b67-ngtkr                 2/2     Running   0          1h
    
    # kubectl logs --tail 2 -n istio-system istiocoredns-cdc56b67-ngtkr -c istio-coredns-plugin
    2019-01-08T05:27:22.897845Z    info    Have 1 service entries
    2019-01-08T05:27:22.897888Z    info    adding DNS mapping: .test.global. ->[127.255.XX.XX]
    ```

4.  Run the following command to create a test container with the tutum/dnsutils image:

    ```
    # kubectl run dnsutils -it --image=tutum/dnsutils bash
    ```

5.  After you open the CLI, run dig to view the corresponding domain name resolution.

    ```
    # dig +short 172.19.XX.XX (The cluster IP of the DNS service) A service1.test.global
    127.255.XX.XX
    # dig +short 172.19.XX.XX (The cluster IP of the DNS service) A service2.test.global
    127.255.XX.XX
    ```


