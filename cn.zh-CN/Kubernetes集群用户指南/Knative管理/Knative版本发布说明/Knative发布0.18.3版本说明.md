---
keyword: Knative发布0.18.3版本说明
---

# Knative发布0.18.3版本说明

阿里云Knative当前已经支持0.18.3 版本，本文介绍Knative 0.18.3版本所做的变更内容和支持的特性。

**说明：** Knative Service的apiVersion推荐使用V1版本，V1alpha1和V1beta1版本将在Knative 0.19.0版本之后废弃。

## 版本解读

-   Knative 0.18.3版本功能的实现需要Kubernetes版本大于等于1.18。
-   多容器支持。Knative Service支持在一个Pod里配置多个业务容器。
-   支持基于Header的路由转发策略。在请求的Header中设置`Knative-Serving-Tag: {revision-tag}`，结合Kourier网关，可以将请求自动转发到指定的版本上。通过该功能可以实现基于Header的灰度发布。
-   兼容K8s的节点选择特性，该特性通过affinity，nodeSelector和tolerations参数配置。
-   Knative Service支持Dryrun功能，可以快速验证当前的Revision Template是否配置正确。在Template中可通过以下任意参数开启此特性：

    -   features.knative.dev/podspec-dryrun: enabled
    -   features.knative.dev/podspec-dryrun: strict
    **说明：** 当创建Knative Service时：

    -   将features.knative.dev/podspec-dryrun设置成`enabled`，表示如果K8s支持Dryrun功能，则会执行Dryrun。如果K8s不支持Dryrun功能也会继续执行创建操作。
    -   将features.knative.dev/podspec-dryrun设置成`strict`，表示当K8s不支持Dryrun功能时会返回`failure`。

