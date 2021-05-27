---
keyword: [Kubernetes, versions, policy, Kubernetes]
---

# Kubernetes versions

Container Service for Kubernetes \(ACK\) provides container-centric solutions based on the open source Kubernetes system. Alibaba Cloud periodically updates the list of Kubernetes versions that are supported by ACK. This topic describes how ACK supports different Kubernetes versions.

## Supported versions

Starting January 1, 2020, ACK supports only the major versions of Kubernetes. ACK supports Kubernetes versions based on the following rules:

-   **Cluster creation**

    ACK allows you to create clusters for two Kubernetes major versions, such as V1.16 and V1.18. When a new major version is supported, the earliest supported version \(V1.16 in this example\) is removed from the list. You can no longer create clusters for the deprecated version. For example, after Kubernetes V1.20 is supported, you can no longer create clusters for Kubernetes V1.16.

-   **Upgrades and O&M**

    Alibaba Cloud guarantees the stability and availability of ACK clusters for the latest three Kubernetes major versions. You can upgrade ACK clusters for the two earlier major versions to the latest major version. For example, if the latest Kubernetes version is V1.20, you can upgrade ACK clusters for Kubernetes V1.18 and V1.16 to V1.20. Alibaba Cloud does not guarantee the stability of ACK clusters created for deprecated Kubernetes versions. In addition, Alibaba Cloud does not guarantee that you can upgrade these clusters to the supported Kubernetes versions. We recommend that you upgrade your clusters when the list of supported Kubernetes versions is updated.

-   **Technical support**

    Alibaba Cloud provides technical support for the latest three major versions of Kubernetes. The technical support includes consultation, online tutorials, and issue locating and fixing. However, Alibaba Cloud is not duty-bound to provide high-quality and effective technical support for deprecated Kubernetes versions.


**Note:**

-   **Supported major versions**: A major version supported by ACK consists of two individual numbers separated by a period \(.\), such as V1.18. The latest minor version corresponding to the major version is also supported, such as V1.18.8. For example, if the supported major version is V1.14, the supported minor version can be V1.14.8.
-   **Deprecated versions**:
    -   Deprecated Kubernetes versions include both major and minor versions.
    -   Deprecated major versions refer to major versions that are earlier than the three supported major versions of Kubernetes.
    -   Deprecated minor versions refer to minor versions other than the latest one. Some of these minor versions are released to fix vulnerabilities listed in the Common Vulnerabilities & Exposures \(CVE\) system.

For more information about the Kubernetes versions supported by ACK, see [Overview of Kubernetes release notes](/intl.en-US/Release notes/Kubernetes release notes/Overview of Kubernetes release notes.md).

## Release cycle

-   In most cases, Alibaba Cloud updates the list of Kubernetes major versions supported by ACK every six months.
-   After a major version is added to the list, the list of supported minor versions will be updated to support new features or patch vulnerabilities.

## Limits

-   For major versions, you can upgrade to only the following major version. This limit does not apply to minor version upgrades.
-   After a new minor version is supported, technical support is provided for only this version. We recommend that you upgrade earlier minor versions to the latest at the earliest opportunity.

