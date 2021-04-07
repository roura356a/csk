# Kubernetes versions

This topic describes the Kubernetes versions that are supported by Container Service for Kubernetes \(ACK\).

## Supported versions

From January 1, 2020, ACK supports only Kubernetes major versions. ACK supports Kubernetes versions based on the following rules:

-   Supported versions when you create clusters

    ACK allows you to create clusters of Kubernetes 1.12, 1.14, and 1.16. However, to create clusters of Kubernetes 1.12, you must submit a ticket to apply for this version to be enabled for your account.

    When ACK supports a new Kubernetes version, earlier versions are added to the version whitelist. For example, when ACK supports Kubernetes 1.18, Kubernetes 1.14 is added to the version whitelist and Kubernetes 1.12 is no longer available. To use Kubernetes versions in the version whitelist, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

-   Supported versions when you upgrade clusters

    ACK allows you to upgrade clusters of Kubernetes 1.11, 1.12, 1.14, and 1.16. Earlier Kubernetes versions, such as Kubernetes 1.10, are deprecated.

    When ACK allows you to upgrade Kubernetes clusters, earlier Kubernetes versions are deprecated. For example, when ACK allows you to upgrade to Kubernetes 1.16, Kubernetes 1.10 is deprecated, which indicates that ACK no longer provides technical support for Kubernetes 1.10 or upgrading from Kubernetes 1.10.


For more information, see [ACK Kubernetes version supporting roadmap](https://github.com/orgs/AliyunContainerService/projects/4).

**Note:**

-   In this topic, a **version** that consists of two individual numbers separated by a period is referred to as a major version, such as V1.14. A version that consists of three individual numbers separated by periods is referred to as a minor version, such as V1.14.8.
-   After a Kubernetes version becomes **unavailable**, you cannot select this version when you create Kubernetes clusters in the ACK console.
-   After a Kubernetes version is **deprecated**, you cannot upgrade from this Kubernetes version and no technical support is provided for this version.

## Release cycle

-   ACK updates major Kubernetes versions every six months.
-   After a major version is released, ACK will release minor versions that include feature updates and fixes for security vulnerabilities.

## Limits

-   For major versions, you can upgrade to only the next major version. This limit does not apply to minor version upgrades.
-   After a new minor version is launched, technical support is provided for only this version. We recommend that you upgrade earlier minor versions to the latest minor version as soon as possible.

