# Istio FAQ {#concept_ck1_yv2_5fb .concept}

This topic lists common Istio FAQ and their corresponding solutions.

## What do I do if the services in the cluster cannot access external URLs? {#section_dwh_gsk_5fb .section}

**Symptom**

The services in the cluster cannot access external URLs.

**Cause**

By default, this is because the pod in the Istio service mesh uses iptables to transparently forward all outbound traffic to the sidecar. The sidecar can only handle the traffic destined for addresses within the cluster.

**Solutions**

-   Define ServiceEntry to call external services.
-   Configure Istio to allow access to a specific range of IP addresses.

For more information, see [**Control Egress Traffic**](https://istio.io/docs/tasks/traffic-management/egress/).

## What do I do if Tiller is in an earlier version? {#section_dcp_2vk_5fb .section}

**Symptom**

The following message is displayed during the installation process:

```
Can't install release with errors: rpc error: code = Unknown desc = Chart incompatible with Tiller v2.7.0
```

**Cause**

Your current version of Tiller needs to be upgraded.

**Solution**

Run the following command to upgrade the Tiller version:

```
$ helm init --tiller-image registry.cn-hangzhou.aliyuncs.com/acs/tiller:v2.9.1
```

## What do I do if Custom Resource Definitions \(CRDs\) are in an invalid version? {#section_ilx_swl_5fb .section}

**Symptom**

The following message is displayed when you create Istio for the first time or upgrade from Istio 1.0:

```
Can't install release with errors: rpc error: code = Unknown desc = apiVersion "networking.istio.io/v1alpha3" in ack-istio/charts/pilot/templates/gateway.yaml is not available
```

**Cause**

CRDs do not exist or are of an earlier version.

**Note:** This problem occurs only in Helm 2.10.0 and earlier versions. For Helm later than V2.10.0, the system automatically upgrades CRDs.

**Solution**

1.  Download the latest Istio. For more information, see [**Downloading the Release**](https://preliminary.istio.io/docs/setup/kubernetes/download-release/).
2.  Run the following command to install the latest CRDs:

    ```
    $ kubectl apply -f install/kubernetes/helm/istio/templates/crds.yaml -n istio-system
    ```

3.  If you have enabled `certmanager`, you must run the following command to install the relevant CRDs:

    ```
    $ kubectl apply -f install/kubernetes/helm/istio/charts/certmanager/templates/crds.yaml
    ```


## What do I do if Istio cannot be installed when I log on as a RAM user? {#section_hgd_clq_5fb .section}

**Symptom**

The following message or a similar one is displayed during the installation process:

```
Error from server (Forbidden): error when retrieving current configuration of:
Resource: "apiextensions.k8s.io/v1beta1, Resource=customresourcedefinitions", GroupVersionKind: "apiextensions.k8s.io/v1beta1, Kind=CustomResourceDefinition"
```

**Cause**

The RAM user does not have permission to install Istio.

**Solutions**

-   Log on to Alibaba Cloud by using the primary account.
-   Grant the RAM user the required permissions. For example, you can grant the RAM user the cluster-admin custom role. For more information, see [Sub-account Kubernetes permission configuration guide](../../../../reseller.en-US/User Guide/Kubernetes cluster/Authorization/Sub-account Kubernetes permission configuration guide.md#).

## What do I do if CRDs are not removed after Istio is uninstalled? {#section_cnv_b4q_5fb .section}

**Symptom**

CRDs are not removed after Istio is uninstalled.

**Cause**

The system does not remove CRDs when you uninstall Istio.

**Solution**

1.  If you use Helm later than V2.9.0, perform step 2 directly. If you use Helm 2.9.0 or earlier, you must first run the following command to delete Job resources:

    ```
    $ kubectl -n istio-system delete job --all
    ```

2.  Run the following command to delete CRDs:

    ```
    $ kubectl delete -f install/kubernetes/helm/istio/templates/crds.yaml -n istio-system
    ```


