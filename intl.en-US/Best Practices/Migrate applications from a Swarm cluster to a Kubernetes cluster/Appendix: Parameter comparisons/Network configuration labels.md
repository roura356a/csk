# Network configuration labels

This topic describes the labels that are related to network configurations in clusters of Container Service for Swarm.

|Swarm label|Description|Related Kubernetes parameter|Sample Swarm configuration|Sample Kubernetes configuration|How to migrate|
|-----------|-----------|----------------------------|--------------------------|-------------------------------|--------------|
|net|The network mode. This parameter is available only in a Swarm compose file of version 1. It is the same as the --net parameter used in the Docker CLI. For more information, see [net](https://docs.docker.com/compose/compose-file/compose-file-v1/#net).|Some parameters can be directly converted into Kubernetes. For example, net: "host" can be directly converted to hostNetwork: true. Other values are not supported in Kubernetes. For more information, see [Host namespaces](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#host-namespaces).|For more information, see the sample Swarm configuration in [net](#section_5gf_5xq_yvr).|For more information, see the sample Kubernetes configuration in [net](#section_5gf_5xq_yvr).|Migrate manually:

 After you use Kompose to convert the Swarm compose file, you must manually delete unsupported parameters. |
|aliyun.routing.port\_|-   The simple routing configuration in Swarm. This parameter specifies one or more domain names for accessing the Service. For more information, see [routing](/intl.en-US/User Guide/Service orchestrations/routing.md).
-   If a domain name is suffixed with .local, it is only used for routing and load balancing among Services inside the cluster. For more information, see [Routing and Server Load Balancer between services in a cluster](/intl.en-US/User Guide/Service discovery and load balancing/Routing and Server Load Balancer between services in a cluster.md).

|The ingress parameter. Kompose converts only the first domain name. You must manually add rules for other domain names. In addition, you must replace the test domain name. For more information, see [t16679.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Configure an Ingress.md).|For more information, see the sample Swarm configuration in [aliyun.routing.port\_](#section_sus_s1a_xya).|For more information, see the sample Kubernetes configuration in [aliyun.routing.port\_](#section_sus_s1a_xya).|Automatic conversion by using Kompose and manual modification are both required.

 Manual modification:

-   Common domain name: After you use Kompose to convert the Swarm compose file, find the Kubernetes resource file \*-ingress.yaml of each Ingress.
    -   Replace host: your-cluster-id.alicontainer.com in this file with the test domain name of the Kubernetes cluster. For example, the test domain name of cluster k8s-piggymetrics-cluster is \*.c7f537c92438f415b943e7c2f8ca30b3b.cn-zhangjiakou.alicontainer.com. Use this test domain name to replace .your-cluster-id.alicontainer.com in each Ingress resource file.
    -   If two or more domain names exist, Kompose converts only the first domain name. You must manually add rules for other domain names in each ingress resource file.
-   Domain name suffixed with .local:
    -   You must first deploy the Kubernetes resource files.
    -   Create a Service in the Container Service for Kubernetes \(ACK\) console. A domain name suffixed with .local can be used for only communications within the Swarm cluster, and no port number needs to be specified. In Kubernetes, a Service name can contain only lowercase letters and hyphens \(`-`\). You cannot add the dot \(.\) in .local to the Service name in Kubernetes. You must modify the application code to migrate the domain name suffixed with .local to Kubernetes. |
|aliyun.routing.session\_sticky|Specifies whether to maintain a sticky session when the routing parameter is set for routing requests. **Note:** This label must be used together with `routing`.

|Not supported in Kubernetes.|-|-|No need to migrate.|
|hostname|The hostname used for accessing a Service across containers that run on different nodes based on Domain Name System \(DNS\) and Server Load Balancer \(SLB\). Typically, it is used by a Service to access another Service.|In Kubernetes, you can create a Service for accessing an application across pods. For more information, see [t16662.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Create a service.md).|For more information, see the sample Swarm configuration in [hostname](#section_viz_z8u_91w).|For more information, see the sample Kubernetes configuration in [hostname](#section_viz_z8u_91w).|Migrate manually:

1.  You must first deploy the Kubernetes resource files.
2.  Create ClusterIP type Services in the ACK console. Kompose cannot automatically convert certain parameters such as hostname, links, and external\_links in the Swarm Compose file because the container port is not specified in the file.

**Note:** In Kubernetes, a Service name can contain only lowercase letters and hyphens \(-\). If parameters such as hostname, links, and external\_links contain other characters such as dots \(`.`\), you must modify the application code to migrate these parameters to Kubernetes. |
|links|1.  The names or aliases for accessing Services across containers that run on different nodes.
2.  Similar to depends\_on, this parameter can be used to determine the startup sequence of Services.

|1.  Create Services with the same names as those specified by the links parameter for accessing applications across pods. For more information, see [t16662.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Create a service.md).
2.  The startup sequence can be determined depending on the requirements of your workload.

|For more information, see the sample Swarm configuration in [links](#section_ne1_6li_5vw).|For more information, see the sample Kubernetes configuration in [links](#section_ne1_6li_5vw).|
|external\_links|This parameter connects two Services to enable communication between each other. Compared with the links parameter, the external\_links parameter allows you to connect a Service to containers that are created through other Swarm compose files or in other methods.|In a Kubernetes cluster, you can create a Service for accessing an application across pods. The pods must be managed by the Kubernetes cluster.

 Migration method:

1.  Migrate the containers specified by external\_links parameter to the Kubernetes cluster.
2.  Create Services with the same names as those specified by the external\_links parameter for accessing applications across pods. For more information, see [t16662.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Create a service.md).

|For more information, see the sample Swarm configuration in [external\_links](#section_s1s_me0_viq).|For more information, see the sample Kubernetes configuration in [external\_links](#section_s1s_me0_viq).|
|```
external:
     host: $RDS_URL
     ports:
       - 3306
```

|The address of the external system to which the Service is directly linked. -   host: the domain name of the external system.
-   ports: the ports of the external system.

|The headless Service in Kubernetes. For more information, see [Headless Services](https://kubernetes.io/docs/concepts/services-networking/service/#headless-services).|For more information, see the sample Swarm configuration in [external: \*\*\*](#section_2nu_49l_8qg).|For more information, see the sample Kubernetes configuration in [external: \*\*\*](#section_2nu_49l_8qg).|Migrate manually:

1.  Delete the external parameter from the Swarm compose file before you use Kompose to convert this file. This prevents the conversion from being interrupted.
2.  After you use Kompose to convert the Swarm compose file, create a Kubernetes resource file for deploying a headless Service, which is Service of the ExternalName type.
3.  Use kubectl to deploy this Kubernetes resource file together with other Kubernetes resource files. |
|aliyun.lb.port\_\*|The Service port to be exposed through an SLB instance to the public or internal networks in Network Address Translation \(NAT\) mapping mode. For more information, see [Server Load Balancer routing](/intl.en-US/User Guide/Service discovery and load balancing/Server Load Balancer routing.md).|Access the Service through an SLB instance, which is similar to load balancing in Swarm. For more information, see [t16677.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Use annotations to configure SLB instances.md).|For more information, see the sample Swarm configuration in [aliyun.lb.port\_\*](#section_hry_prt_hpp).|For more information, see the sample Kubernetes configuration in [aliyun.lb.port\_\*](#section_hry_prt_hpp).|Migrate manually:

 After the Service is deployed, create a LoadBalancer type Service to access it through an SLB instance. For more information, see [t16677.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Use annotations to configure SLB instances.md).

**Note:**

-   The configuration in this example is for reference only. You must follow related Kubernetes documents to configure the Service based on the requirements of your workload.
-   When an SLB instance is attached to the Service port, production traffic may be immediately received. We recommend that you attach the SLB instance to a test port for testing before you attach the SLB instance to the Service port. |
|dns|The upstream DNS server of the container.|The DNS configurations that apply to pods. For more information, see [Pod's DNS Config](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-config). **Note:** If a DNS server is specified, the Service name may fail to be parsed.

|For more information, see the sample Swarm configuration in [dns](#section_co5_ssv_un3).|For more information, see the sample Kubernetes configuration in [dns](#section_co5_ssv_un3).|Migrate manually:

 After you use Kompose to convert the Swarm compose file, add the dnsConfig parameter to the Kubernetes resource file \*-deployment.yaml of the application. |

## net

|Sample Swarm configuration|Sample Kubernetes configuration|
|--------------------------|-------------------------------|
|![net](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2846858951/p83857.png)

|![net](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2846858951/p83861.png)

 For more information, see [logtail2-daemonset.yaml](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter configuration examples.md). |

## aliyun.routing.port\_

|Sample Swarm configuration|Sample Kubernetes configuration|
|--------------------------|-------------------------------|
|![aliyun.routing.port_](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2846858951/p83862.png)

|![aliyun.routing.port_](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2846858951/p83863.png)

 For more information, see [gateway-ingress.yaml](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter configuration examples.md). |

## hostname

|Sample Swarm configuration|Sample Kubernetes configuration|
|--------------------------|-------------------------------|
|![hostname](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2846858951/p83867.png)

|![hostname](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5641026061/p83868.png)

 **Note:** Create a Service with the same name \(marked by 1 as shown in the figure\) as the hostname in the ACK console. Use the container port as the Service port. |

## links

|Sample Swarm configuration|Sample Kubernetes configuration|
|--------------------------|-------------------------------|
|![links](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2846858951/p83876.png)

|![links](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5641026061/p83878.png)

 **Note:** Create Services with the same names as those specified by the links parameter in the ACK console. |

## external\_links

|Sample Swarm configuration|Sample Kubernetes configuration|
|--------------------------|-------------------------------|
|![external_links](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3846858951/p83889.png)

|![external_links](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5641026061/p83890.png)

 **Note:** Create a Service with the same name as the one specified by the external\_links parameter in the ACK console. |

## external: \*\*\*

The external: \*\*\* parameter includes the following fields:

```
external:
     host: $RDS_URL
     ports:
       - 3306
```

|Sample Swarm configuration|Sample Kubernetes configuration|
|--------------------------|-------------------------------|
|![external:](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3846858951/p83905.png)

|![external:  ](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3846858951/p83906.png)

 For more information, see [account-db-service.yaml](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter configuration examples.md). |

## aliyun.lb.port\_\*

|Sample Swarm configuration|Sample Kubernetes configuration|
|--------------------------|-------------------------------|
|![aliyun.lb.port_*](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3846858951/p83912.png)

|![aliyun.lb.port_*](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5641026061/p83913.png) |

## dns

|Sample Swarm configuration|Sample Kubernetes configuration|
|--------------------------|-------------------------------|
|![dns](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4846858951/p83914.png)

|![dns](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4846858951/p83915.png)

 For more information, see [logtail2-daemonset.yaml](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter configuration examples.md). |

