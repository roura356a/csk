---
keyword: [domain name resolution, dnspolicy, CoreDNS, service discovery]
---

# Introduction and configuration of the DNS service in ACK clusters

In a cluster of Container Service for Kubernetes \(ACK\), Service names are preferably used to access Services. Therefore, a cluster-wide DNS service is required to resolve Service names to cluster IPs \(IP addresses assigned to Services\). CoreDNS is deployed in ACK clusters and serves as a DNS server to provide DNS resolution services for workloads deployed in ACK clusters. This topic describes how DNS resolution works in ACK clusters and how to configure CoreDNS and DNS policies in different scenarios.

Before you configure the pre-installed DNS server, make sure that you have completed the following steps:

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [t16645.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md)

By default, a Service named kube-dns is deployed in an ACK cluster to provide DNS resolution service for the ACK cluster. You can run the following command to query information about the kube-dns Service:

```
kubectl get svc kube-dns -n kube-system
```

The following output is returned:

```
NAME       TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)                  AGE
kube-dns   ClusterIP   172.24.0.10   <none>        53/UDP,53/TCP,9153/TCP   27d
```

Two pods named coredns are deployed as the backend of the DNS resolution service for the ACK cluster. You can run the following command to query information about the two pods named coredns.

```
kubectl get deployment coredns -n kube-system
```

The following output is returned:

```
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
coredns   2/2     2            2           27d
```

## How DNS resolution works in ACK clusters

The startup parameters of kubelet in an ACK cluster contain `--cluster-dns=<dns-service-ip>` and `--cluster-domain=<default-local-domain>`. These parameters are used to configure the IP addresses and suffixes of the base domain name for the DNS servers in the ACK cluster.

The DNS configuration file in the pod is /etc/resolv.conf. The file contains the following content:

```
nameserver xx.xx.0.10
search kube-system.svc.cluster.local svc.cluster.local cluster.local
options ndots:5
```

|Parameter|Description|
|---------|-----------|
|nameserver|Specifies the IP addresses of the DNS servers.|
|search|Specifies the suffixes that are used for DNS queries. More suffixes indicate more DNS queries. In ACK clusters, suffixes are `kube-system.svc.cluster.local`, `svc.cluster.local`, and `cluster.local`. Therefore, up to eight queries \(four for an IPv4 address and four for an IPv6 address\) are generated for a request sent to an ACK cluster.|
|options|Specifies the options for the DNS configuration file. You can specify multiple key-value pairs. For example, ndots:5 specifies that if the number of dots in the domain name string is greater than 5, the domain name is a fully qualified domain name and is directly resolved. If the number of dots in the domain name string is less than 5, the domain name is appended with the suffixes specified by the search parameter before it is resolved.|

Based on the preceding DNS resolution settings on the pods, queries of internal domain names \(Services\) and external domain names are sent to the DNS servers of an ACK cluster for DNS resolution.

## Configure DNS policies for an ACK cluster in different scenarios

You can use dnsPolicy to specify different DNS policies among pods. ACK clusters support the following DNS policies:

-   ClusterFirst: This policy indicates that a pod uses CoreDNS to resolve domain names from inside or outside of the ACK cluster. The /etc/resolv.conf file contains the address of the DNS server that is provided by CoreDNS, which is kube-dns. This is the default DNS policy for workloads in an ACK cluster.
-   None: This policy indicates that a pod ignores the DNS settings of the ACK cluster. You must customize the DNS settings by using the dnsConfig field.
-   Default: This policy indicates that a pod inherits the DNS resolution settings from the node where the pod is deployed. In an ACK cluster, nodes are created based on Elastic Compute Service \(ECS\) instances. Therefore, a pod directly uses the /etc/resolv.conf file of the ECS instance-based node where the pod is deployed. This file contains the address of a DNS server that is provided by Alibaba Cloud DNS.
-   ClusterFirstWithHostNetwork: This policy indicates that a pod in the host network uses the ClusterFirst policy. By default, a pod in the host network uses the Default policy.

You can use the preceding DNS policies in different scenarios.

-   **Scenario 1: Use CoreDNS provided by ACK clusters to resolve domain names**

    In this scenario, you must specify `dnsPolicy: ClusterFirst` for the DNS policy settings. The following code block is an example:

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: alpine
      namespace: default
    spec:
      containers:
      - image: alpine
        command:
          - sleep
          - "10000"
        imagePullPolicy: Always
        name: alpine
      dnsPolicy: ClusterFirst
    ```

-   **Scenario 2: Customize DNS settings for a pod**

    To customize DNS settings for a Deployment, you must specify `dnsPolicy: None` for the DNS policy settings. The following code block is an example:

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: alpine
      namespace: default
    spec:
      containers:
      - image: alpine
        command:
          - sleep
          - "10000"
        imagePullPolicy: Always
        name: alpine
      dnsPolicy: None
      dnsConfig:
        nameservers: ["169.254.xx.xx"]
        searches:
        - default.svc.cluster.local
        - svc.cluster.local
        - cluster.local
        options:
        - name: ndots
          value: "2"
    ```

    Descriptions of parameters in the dnsConfig section.

    |Parameter|Description|
    |---------|-----------|
    |nameservers|A list of IP addresses of DNS servers for a pod. You can specify up to three IP addresses. If you specify dnsPolicy as `None` for a pod, you must specify at least one IP address. If you do not specify dnsPolicy as None for a pod, this parameter is optional. The listed DNS server IP addresses will be added to the nameserver parameter of the DNS configuration file that is generated based on the specified DNS policy. Duplicate IP addresses are removed.|
    |searches|A list of DNS search domains for hostname lookup in a pod. This parameter is optional. The listed DNS search domains will be added to the list of base search domains that are generated based on the specified DNS policy. Duplicate domain names are removed. You can specify up to six search domains.|
    |options|A list of optional items. Each item can contain a name \(required\) and a value \(optional\). The specified items will be added to the list of optional items that are generated based on the specified DNS policy. Duplicate items are removed.|

    For more information, see [DNS for Services and Pods](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/).

-   **Scenario 3: Use the DNS settings of an ECS instance that is provided by Alibaba Cloud**

    If your application pods do not need to access other services deployed in the ACK cluster, you can specify `dnsPolicy: Default` for the DNS policy settings. In this scenario, the DNS resolution is performed by Alibaba Cloud DNS and CoreDNS is not required. The following code block is an example:

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: alpine
      namespace: default
    spec:
      containers:
      - image: alpine
        command:
          - sleep
          - "10000"
        imagePullPolicy: Always
        name: alpine
      dnsPolicy: Default
    ```

-   **Scenario 4: Enable pods in the host network to access services of an ACK cluster**

    If you specify hostNetwork:true for the network settings of your application pods, your application pods can directly use the host network. In this case, the default DNS policy for a pod is Default. As a result, your application pods cannot access services deployed in the ACK cluster. If you want to enable pods that run in the host network to access services deployed in the ACK cluster, you must specify `dnsPolicy: ClusterFirstWithHostNet` for the DNS policy settings. The following code block is an example:

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: alpine
      namespace: default
    spec:
      hostNetwork: true
      dnsPolicy: ClusterFirstWithHostNet
      containers:
      - image: alpine
        command:
          - sleep
          - "10000"
        imagePullPolicy: Always
        name: alpine
    ```


## Configure CoreDNS for an ACK cluster

This section describes the default configurations of CoreDNS and how to configure extended features based on CoreDNS. This applies to scenarios as described in [Scenario 1: Use CoreDNS provided by ACK clusters to resolve domain names](#li_wa7_oax_ncy).

**Note:** If local-dns is installed in your ACK cluster, you can find a configuration file named node-local-dns in the kube-system namespace. The configuration file of local-dns is configured in the same way as that of CoreDNS.

**Default configurations of CoreDNS**

In the kube-system namespace, you can find a ConfigMap named coredns. For more information about how to view a ConfigMap, see [View a ConfigMap](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Configuration items and key/Manage ConfigMaps.md). The following code block is an example for the Corefile field of the coredns ConfigMap. CoreDNS provides services based on the plug-ins specified in the Corefile field.

```
  Corefile: |
    .:53 {
        errors
        health {
           lameduck 5s
        }
        ready
        kubernetes cluster.local in-addr.arpa ip6.arpa {
          pods insecure
          upstream
          fallthrough in-addr.arpa ip6.arpa
          ttl 30
        }
        prometheus :9153
        forward . /etc/resolv.conf
        cache 30
        loop
        reload
        loadbalance
    }
```

|Parameter|Description|
|---------|-----------|
|errors|Errors are logged to stdout.|
|health|The health check report of CoreDNS. The default listening port is 8080. This parameter is used to evaluate the health status of CoreDNS. You can visit `http://localhost:8080/health` to view the health check report of CoreDNS.|
|ready|The health check report of the CoreDNS plug-ins. The default listening port number is 8181. This parameter is used to evaluate the readiness of the CoreDNS plug-ins. You can visit `http://localhost:8181/ready` to view the readiness of the CoreDNS plug-ins. After all plug-ins are in the running state, 200 is returned for the readiness of CoreDNS plug-ins.|
|kubernetes|The CoreDNS kubernetes plug-in that is used to provide DNS resolution for Services inside an ACK cluster.|
|prometheus|Metrics of CoreDNS. You can visit `http://localhost:9153/metrics` to view metrics of CoreDNS in Prometheus format.|
|forward or proxy|DNS queries are forwarded to the predefined DNS server. By default, DNS queries of domain names that are not within the cluster domain of Kubernetes are forwarded to the predefined resolver \(/etc/resolv.conf\). The default configurations are based on the /etc/resolv.conf file on the host.|
|cache|DNS cache.|
|loop|Loop detection. If a loop is detected, CoreDNS is suspended.|
|reload|Allows automatic reload of a changed Corefile. After you edit the ConfigMap configuration, wait two minutes for the changes to take effect.|
|loadbalance|A round-robin DNS loadbalancer that randomizes the order of A, AAAA, and MX records in the answer.|

**Configure extended features based on CoreDNS**

You can configure extended features based on CoreDNS in the following scenarios:

-   **Scenario 1: Enable Log Service**

    To log each DNS resolution performed by CoreDNS, enable the log plug-in by adding log to Corefile. The following code block is an example:

    ```
      Corefile: |
        .:53 {
            errors
            log
            health {
               lameduck 5s
            }
            ready
            kubernetes cluster.local in-addr.arpa ip6.arpa {
              pods insecure
              upstream
              fallthrough in-addr.arpa ip6.arpa
              ttl 30
            }
            prometheus :9153
            forward . /etc/resolv.conf
            cache 30
            loop
            reload
            loadbalance
        }
    ```

-   **Scenario 2: Customize DNS servers for specified domain names**

    If domain names with a suffix of example.com need to be resolved by a user-defined DNS server \(for example, DNS server 10.10.0.10\), you must add a custom resolution setting for the domain names. The following code block is an example:

    ```
    example.com:53 {
      errors
      cache 30
      forward . 10.10.0.10
    }
    ```

    The following code block shows all configurations:

    ```
      Corefile: |
        .:53 {
            errors
            health {
               lameduck 5s
            }
            ready
            kubernetes cluster.local in-addr.arpa ip6.arpa {
              pods insecure
              upstream
              fallthrough in-addr.arpa ip6.arpa
              ttl 30
            }
            prometheus :9153
            forward . /etc/resolv.conf
            cache 30
            loop
            reload
            loadbalance
        }
        example.com:53 {
            errors
            cache 30
            forward . 10.10.0.10
        }
    ```

-   **Scenario 3: Customize DNS servers for external domain names**

    If the domain names that need to be resolved by user-defined DNS servers do not contain the same suffix, you can use user-defined DNS servers to perform DNS resolution for all external domain names. In this scenario, you must forward the domain names that cannot be resolved by the on-premises DNS servers to Alibaba Cloud DNS. Do not modify the /etc/resolv.conf files on ECS instances of the ACK cluster. Assume that the IP addresses of the user-defined DNS servers are 10.10.0.10 and 10.10.0.20, you can modify the forward parameter. The following code block is an example:

    ```
      Corefile: |
        .:53 {
            errors
            health {
               lameduck 5s
            }
            ready
            kubernetes cluster.local in-addr.arpa ip6.arpa {
              pods insecure
              upstream
              fallthrough in-addr.arpa ip6.arpa
              ttl 30
            }
            prometheus :9153
            forward . 10.10.0.10 10.10.0.20
            cache 30
            loop
            reload
            loadbalance
        }
    ```

-   **Scenario 4: Customize hosts for specified domain names**

    You can configure the hosts plug-in if you want to customize hosts for specified domain names, for example, you may need to point www.example.com to 127.0.0.1. The following code block is an example:

    ```
      Corefile: |
        .:53 {
            errors
            health {
               lameduck 5s
            }
            ready
            
            hosts {
              127.0.0.1 www.example.com
              fallthrough
            }
          
            kubernetes cluster.local in-addr.arpa ip6.arpa {
              pods insecure
              upstream
              fallthrough in-addr.arpa ip6.arpa
              ttl 30
            }
            prometheus :9153
            forward . /etc/resolv.conf
            cache 30
            loop
            reload
            loadbalance
        }
    ```

    **Note:** You must specify fallthrough. Otherwise, domain names other than the specified one may fail to be resolved.

-   **Scenario 5: Enable external access to services in an ACK cluster**

    If you want to enable a process on an ECS instance to access services in the ACK cluster where the ECS instance is deployed, you can add the ClusterIP of kube-dns in the ACK cluster to the nameserver parameter of the /etc/resolv.conf file on the ECS instance. Do not modify other settings in the /etc/resolv.conf file.

    In an internal network, you can use an internal Server Load Balancer \(SLB\) instance to allow internal access to services in the ACK cluster. Then, log on to the [Alibaba Cloud DNS console](https://dns.console.aliyun.com/#/dns/domainList) and add an A record that points to the private IP address of the SLB instance.

-   **Scenario 6: Use a domain name to allow access to your service in an ACK cluster or enable CNAME resolution for an ACK cluster**

    You can use foo.example.com to allow all access to your service in an ACK cluster from the Internet, internal networks, and inside an ACK cluster. The following section describes how to enable this feature:

    -   Your service foo.default.svc.cluster.local is exposed to external access through an public-facing SLB instance. The domain name foo.example.com is resolved to the IP address of the public-facing SLB instance.
    -   Your service foo.default.svc.cluster.local is exposed to internal access through an public-facing SLB instance. Log on to the [Alibaba Cloud DNS console](https://dns.console.aliyun.com/#/dns/domainList) to point foo.example.com to the IP address of the internal SLB instance in the virtual private cloud \(VPC\) where the ACK cluster is deployed. For more information about the procedure, see the preceding section [Scenario 4: Customize hosts for specified domain names](#li_ejo_1bo_ag5).
    -   Inside the ACK cluster, you can use the Rewrite plug-in to add a Canonical Name \(CNAME\) record to point foo.example.com to foo.default.svc.cluster.local. The following code block is an example:

        ```
          Corefile: |
            .:53 {
                errors
                health {
                   lameduck 5s
                }
                ready
                
                rewrite stop {
                  name regex foo.example.com foo.default.svc.cluster.local
                  answer name foo.default.svc.cluster.local foo.example.com 
                }
        
                kubernetes cluster.local in-addr.arpa ip6.arpa {
                  pods insecure
                  upstream
                  fallthrough in-addr.arpa ip6.arpa
                  ttl 30
                }
                prometheus :9153
                forward . /etc/resolv.conf
                cache 30
                loop
                reload
                loadbalance
            }
        ```


