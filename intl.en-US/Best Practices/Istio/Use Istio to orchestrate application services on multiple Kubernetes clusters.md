# Use Istio to orchestrate application services on multiple Kubernetes clusters {#concept_nsk_d25_cfb .concept}

Istio supports hybrid cloud development. For example, Istio can help you run applications on Alibaba Cloud Container Service, your local Kubernetes clusters, or other public cloud services. Istio provides a unified view for the entire service platform to help you manage connections between the different environments, and guarantee the security. Istio supports multiple clusters, which allows adding multiple Kubernetes clusters to a single service mesh and enables cross-cluster service discovery. According to its official introduction, Istio will support global cluster-level load balancing and support non-flat networks through gateway peering.

[Use Istio to deploy application services across Kubernetes and ECS instances](reseller.en-US/Best Practices/Istio/Use Istio to deploy application services across Kubernetes and ECS instances.md#) describes the Istio mesh expansion capability provided by Alibaba Cloud Container Service for Kubernetes.

This topic describes an example of how to use Istio to orchestrate application services on multiple Kubernetes clusters on Alibaba Cloud Container Service. After the Istio control plane is installed on a Kubernetes cluster and Istio connects this cluster and the other Kubernetes cluster, a mesh network across multiple Kubernetes clusters is generated.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21317/155007336412672_en-US.png)

## Prepare Kubernetes clusters {#section_evd_f25_cfb .section}

Alibaba Cloud Container Service for Kubernetes 1.10.4 is available. You can use the console to create a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).

Make sure that you have installed kubectl and you can connect to each Kubernetes cluster by using kubectl. Additionally, the following conditions must be met:

-   Each cluster must have a unique pod CIDR block and service CIDR block.
-   All pods in each cluster must be routable to each other.
-   All Kubernetes control plane API servers must be routable to each other.

|Cluster|ContainerCIDR|ServiceCIDR|
|-------|-------------|-----------|
|Istio-control-plane|172.16.0.0/16|172.19.0.0/20|
|Istio-remote1|172.20.0.0/16|172.21.0.0/20|

To obtain the files used in this example, see [Istio multi-cluster sample files](https://github.com/osswangxining/istio-multicluster).

## Install the Istio control plane {#section_ivd_f25_cfb .section}

Deploy Istio by using App Catalog

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane, choose **Store** \> **App Catalog**, and click `ack-istio` on the right side.
3.  On the displayed page, select **istio-system** from the namespace drop-down list, and click **Values**. You can edit parameters to customize your Istio.

    ```
    ......
       istio-ilbgateway:
          enabled: true
    ......
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21317/155007336412673_en-US.png)

    **Note:** The readme document on the page provides the installation and removal information, especially common questions about Custom Resource Definition \(CRD\) versions.

    **Note:** If you have questions about CRD versions when you use Istio 1.0, see [FAQs for Istio practices on Alibaba Cloud Container Service for Kubernetes](https://yq.aliyun.com/articles/621026). Questions with solutions are in the progress of updating.


## Install the Istio remote component {#section_lvd_f25_cfb .section}

Run the following scripts to obtain the connection information of the control plane:

```
export PILOT_POD_IP=$(kubectl -n istio-system get pod -l istio=pilot -o jsonpath='{.items[0].status.podIP}')
export POLICY_POD_IP=$(kubectl -n istio-system get pod -l istio=mixer -o jsonpath='{.items[0].status.podIP}')
export STATSD_POD_IP=$(kubectl -n istio-system get pod -l istio=statsd-prom-bridge -o jsonpath='{.items[0].status.podIP}')
export TELEMETRY_POD_IP=$(kubectl -n istio-system get pod -l istio-mixer-type=telemetry -o jsonpath='{.items[0].status.podIP}')
export ZIPKIN_POD_IP=$(kubectl -n istio-system get pod -l app=jaeger -o jsonpath='{.items[0].status.podI
P}')
echo "remotePilotAddress: $PILOT_POD_IP"
echo "remotePolicyAddress: $POLICY_POD_IP"
echo "remoteStatsdPromBridge: $STATSD_POD_IP"
echo "remoteTelemetryAddress: $TELEMETRY_POD_IP"
echo "remoteZipkinAddress: $ZIPKIN_POD_IP"
```

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane, choose **Store** \> **App Catalog**, and click `ack-istio-remote` in the right pane.
3.  On the displayed page, select **istio-system** from the namespace drop-down list, and click **Values**. Enter the following information in the values area to customize the Istio remote component.

    ```
      .....
      envoyStatsd:
        enabled: false
        host: "$remoteStatsdPromBridge"
    ```

    ```
    # Remote Istio endpoints. Can be hostnames or IP addresses# The Pilot address is required. The remaining parameters are optional.
    remotePilotAddress: "$remotePilotAddress"
    remotePolicyAddress: "$remotePolicyAddress"
    remoteTelemetryAddress: "$remoteTelemetryAddress"
    remoteZipkinAddress: "$remoteZipkinAddress"
    ```


## Install the sample on the cluster Istio-control-plane {#section_mvd_f25_cfb .section}

Run the following commands or use the console to create the `bookinfo` namespace, and then deploy the modified application. In the modified application, the `details` component is removed and `ingressgateway` is defined.

To obtain the files used in this example, see [Istio multi-cluster sample files](https://github.com/osswangxining/istio-multicluster).

```
kubectl create ns bookinfo 
\kubectl label namespace bookinfo istio-injection=enabled
kubectl apply -n bookinfo -f ./bookinfo/bookinfo-without-details.yaml
kubectl apply -n bookinfo -f ./bookinfo/bookinfo-gateway.yaml
```

In the modified deployment based on the example, the `details` component runs on the Istio-control-plane Kubernetes cluster installed with the Istio control plane. Furthermore, the `details`component runs on the other Kubernetes cluster, that is, Istio-remote-1.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21317/155007336412674_en-US.png)

After the application runs normally, you can access the `/productpage` page through the access address exposed by `ingressgateway`. The following is a sample page. The `details` part may not be displayed because the `details` component has not been installed on the Istio-remote-1 cluster or joined to the same mesh.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21317/155007336412675_en-US.png)

## Configure the remote cluster {#section_pvd_f25_cfb .section}

The Istio control plane requires access to all the clusters in the mesh to complete service discovery. The following describes how to create a Service account in the remote cluster and grant the account RBAC permissions. The credentials of the Service account will be used to generate a kubeconfig file for the remote cluster, after which you can access the remote cluster.

The user with the root permission of each cluster must do the following on the cluster to add the cluster to the service mesh. Run the `generate-kubeconfig.sh` command in which a parameter is specified to identify each cluster.

```
 ./generate-kubeconfig.sh myremote1
```

After you complete the preceding steps, the kubeconfig file of the remote cluster is created in the current directory. The cluster file name and the original kubeconfig cluster name are the same.

## Configure the control plane cluster {#section_qvd_f25_cfb .section}

On the cluster that runs the Istio control plane, create a Secret for each remote cluster:

```
 ./create-secret.sh myremote1
```

## Install the sample on the Istio-remote-1 cluster {#section_rvd_f25_cfb .section}

Install the `details` part of the sample on the Istio-remote-1 cluster. To obtain the YAML file, see [Istio multi-cluster sample files](https://github.com/osswangxining/istio-multicluster).

```
kubectl apply -n bookinfo -f ./bookinfo/bookinfo-details.yaml
```

The `details` service is then registered in the control plane. After that, you can check whether the `details` service is contained in the response result of `{pilot-ipAddress}:8080/v1/registration`.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21317/155007336512676_en-US.png)

Access the `/productpage` page again. The `details` part is displayed on the page as follows.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21317/155007336512677_en-US.png)

## Conclusion {#section_uvd_f25_cfb .section}

This topic describes an example of how to use Istio to orchestrate application services on multiple Kubernetes clusters on Alibaba Cloud Container Service. After the Istio control plane is installed on a Kubernetes cluster and Istio connects this cluster and the other Kubernetes cluster, a mesh network across multiple Kubernetes clusters is generated.

We recommend that you use Alibaba Cloud Container Service for Kubernetes to quickly build Istio so that you can easily integrate this platform with your microservices developments in your projects.

