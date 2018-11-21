# Istio 常见问题 {#concept_ck1_yv2_5fb .concept}

您可通过本文了解 Istio 的常见问题及解决方法。

## 集群内无法访问集群外的 URL {#section_dwh_gsk_5fb .section}

**问题现象**

集群内无法访问集群外的 URL。

**问题原因**

缺省情况下，Istio 服务网格内的 Pod，由于其 iptables 将所有外发流量都透明的转发给了 Sidecar，所以这些集群内的服务无法访问集群之外的 URL，而只能处理集群内部的目标。

**解决方法**

-   通过定义 ServiceEntry 来调用外部服务。
-   配置 Istio 使其直接放行对特定IP地址范围的访问。

详细内容，可参考[**Control Egress Traffic**](https://istio.io/docs/tasks/traffic-management/egress/)

## Tiller 版本较低 {#section_dcp_2vk_5fb .section}

**问题现象**

安装过程中遇到如下提示：

```
Can't install release with errors: rpc error: code = Unknown desc = Chart incompatible with Tiller v2.7.0
```

**问题原因**

Tiller 版本较低，需要升级。

**解决方法**

执行以下任意一条命令，升级 Tiller 版本。

```
$ helm init --tiller-image registry.cn-hangzhou.aliyuncs.com/acs/tiller:v2.11.0 --upgrade
```

```
$ helm init --tiller-image registry.cn-hangzhou.aliyuncs.com/acs/tiller:v2.10.0 --upgrade
```

**说明：** 此处请升级到v2.10.0及以上版本。

## CRD（Custom Resource Definitions）版本问题 {#section_ilx_swl_5fb .section}

**问题现象**

在第一次创建或升级 Istio 1.0 时遇到如下提示：

```
Can't install release with errors: rpc error: code = Unknown desc = apiVersion "networking.istio.io/v1alpha3" in ack-istio/charts/pilot/templates/gateway.yaml is not available
```

**问题原因**

CRD 不存在或版本较低，需要安装最新版本的 CRD。

**说明：** 仅Helm版本为2.10.0及之前版本，会遇到此问题。2.10.0之后的版本，系统自动升级 CRD。

**解决方法**

1.  下载新版本的 Istio，可参考[**Downloading the Release**](https://preliminary.istio.io/docs/setup/kubernetes/download-release/)。
2.  执行以下命令，安装最新版本的 CRD。

    ```
    $ kubectl apply -f install/kubernetes/helm/istio/templates/crds.yaml -n istio-system
    ```

3.  如果启用了`certmanager`，需要执行以下命令安装相关的 CRD。

    ```
    $ kubectl apply -f install/kubernetes/helm/istio/charts/certmanager/templates/crds.yaml
    ```


## 子账号无法安装 Istio {#section_hgd_clq_5fb .section}

**问题现象**

安装过程中遇到类似如下提示：

```
Error from server (Forbidden): error when retrieving current configuration of:
Resource: "apiextensions.k8s.io/v1beta1, Resource=customresourcedefinitions", GroupVersionKind: "apiextensions.k8s.io/v1beta1, Kind=CustomResourceDefinition"
```

**问题原因**

当前使用账号不具有安装 Istio 的权限。

**解决方法**

-   切换为主账号登录。
-   对子账号赋予相应的权限，例如自定义角色中的 cluster-admin，可参考[子账号Kubernetes应用权限配置指导](../../../../intl.zh-CN/用户指南/Kubernetes 集群/授权管理/子账号Kubernetes应用权限配置指导.md#)。

## 卸载 Istio 后 CRD 残留 {#section_cnv_b4q_5fb .section}

**问题现象**

卸载 Istio 后，CRD 残留。

**问题原因**

卸载 Istio，系统不会删除 CRD，需要执行命令删除。

**解决方法**

1.  Helm为2.9.0及之前版本，需要执行以下命令，删除 Job 资源。

    ```
    $ kubectl -n istio-system delete job --all
    ```

2.  执行以下命令，删除 CRD。

    ```
    $ kubectl delete -f install/kubernetes/helm/istio/templates/crds.yaml -n istio-system
    ```


