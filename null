---
keyword: [Logtail, migrate-controller, authentication based on AccessKey information, prerequisites]
---

# Prerequisites

This topic describes the requirements that must be met before you install Alibaba Cloud components in a registered cluster.

The nodes of a registered cluster may be located in an on-premises data center or hosted by other cloud providers. You can install specific Alibaba Cloud components in the registered cluster. For example, you can install the logtail-ds component to enable access to Alibaba Cloud Log Service, and install the migrate-controller component to enable access to Alibaba Cloud Object Storage Service \(OSS\). Before you can install the components in the cluster, you must create the AccessKey information to authenticate your requests.

To ensure the secure use of your AccessKey information only within the registered cluster and minimize the risk of leakage, you must first use the AccessKey information to deploy a Secret named alibaba-addon-secret in the registered cluster. Based on the type of component, you must:

-   Attach the required security policies to your Resource Access Management \(RAM\) user by using the AccessKey information.
-   Create a Secret named alibaba-addon-secret in the namespace that is required by the component. For example, the logtail-ds component uses the kube-system namespace and the migrate-controller component uses the velero namespace.

## logtail-ds

1.  For more information about how to install and use logtail-ds, see [Install and use logtail-ds](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Elastic node pools/Install and use logtail-ds.md).


## migrate-controller

For more information about how to install and use migrate-controller, see [Install migrate-controller](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Manage components/Install migrate-controller.md).

