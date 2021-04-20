# Kubernetes version support policy

This topic describes the Kubernetes version support policy used by Container Service for Kubernetes \(ACK\).

## Supported versions

Starting January 1, 2020, ACK supports only the major versions of Kubernetes. ACK supports Kubernetes versions based on the following rules:

-   Cluster creation

    ACK allows you to create clusters of up to three Kubernetes major versions. For example, ACK supports Kubernetes v1.12, v1.14, and v1.16. The earliest supported version \(v1.12 in this example\) is included in the whitelist. To create an ACK cluster of the earliest supported version, you must [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

    When a new Kubernetes version is introduced in ACK, the earliest supported version in the whitelist is phased out and the later version is added to the whitelist. In this example, when Kubernetes v1.18 is introduced in ACK, Kubernetes v1.12 is phased out and Kubernetes v1.14 is added to the whitelist. If you want to create an ACK cluster of the Kubernetes version in the whitelist, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

-   Cluster upgrades

    ACK allows you to upgrade clusters of four Kubernetes major versions. For example, you can upgrade an ACK cluster from v1.11 to v1.12, v1.12 to v1.14, v1.14 to v1.16, or v1.16 to v1.18. Earlier Kubernetes versions such as Kubernetes v1.10 are phased out. This means that you cannot upgrade ACK clusters of Kubernetes v1.10 to v1.11.

    When a new Kubernetes version is supported for upgrading ACK clusters, an earlier version is phased out. In this example, when ACK allows you to upgrade clusters of Kubernetes v1.18 to v1.20, Kubernetes v1.11 is phased out. This means that ACK no longer provides technical support for Kubernetes v1.11 or allows you to upgrade clusters of Kubernetes v1.11.


For more information, see [ACK Kubernetes version support roadmap](https://github.com/orgs/AliyunContainerService/projects/4).

**Note:**

-   In this topic, a **version** that consists of two individual numbers separated by a period \(.\) is referred to as a major version, such as v1.14. A version that consists of three individual numbers separated by periods is referred to as a minor version, such as v1.14.8.
-   After a Kubernetes version becomes **unavailable**, you can no longer select this version when you create Kubernetes clusters in the ACK console.
-   After a Kubernetes version is **deprecated**, you can no longer upgrade clusters of this version and no technical support is provided for this version.

## Release cycle

-   ACK updates Kubernetes major versions every six months.
-   After a major version is released, ACK releases minor versions that include feature updates and vulnerability fixes.

## Limits

-   For major versions, you can upgrade to only the following major version. This limit does not apply to minor version upgrades.
-   After a new minor version is released, technical support is provided for only this version. We recommend that you upgrade earlier minor versions to the latest at the earliest opportunity.

