---
keyword: Knative 0.18.3 release note
---

# Knative 0.18.3

Container Service for Kubernetes \(ACK\) supports Knative 0.18.3. This topic describes the changes and features of Knative 0.18.3.

**Note:** We recommend that you set the apiVersion parameter of Knative Services to V1. The V1alpha1 and V1beta1 versions will be deprecated after Knative 0.19.0 is released.

## Features

-   To use the features of Knative 0.18.3, the Kubernetes version must be 1.18 or later.
-   Supports multiple containers in a pod. Knative Services allow you to deploy multiple containers in a pod.
-   Supports header-based routing policies. You can specify the `Knative-Serving-Tag: {revision-tag}` header in a request. This allows the Kourier ingress to directly send requests to specific revisions. You can use this feature to implement header-based canary releases.
-   Adds compatibility with the nodeSelector feature in Kubernetes. The following parameters are required to configure this feature: affinity, nodeSelector, and tolerations.
-   Knative Services support the dry-run feature. This feature allows you to validate the configurations of the current revision template. You can use one of the following parameters to enable the dry-run feature in the template:

    -   features.knative.dev/podspec-dryrun: enabled
    -   features.knative.dev/podspec-dryrun: strict
    **Note:** When you create a Knative Service:

    -   If you set features.knative.dev/podspec-dryrun to `enabled`, a dry run is performed if Kubernetes supports the dry-run feature. If Kubernetes does not support the dry-run feature, the system still tries to create the Knative Service.
    -   If you set features.knative.dev/podspec-dryrun to `strict`, a `failure` is returned if Kubernetes does not support the dry-run feature.

