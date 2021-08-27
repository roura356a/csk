---
keyword: [OSS volumes, FAQ, frequently asked questions, FlexVolume]
---

# FAQ about OSS volumes

This topic provides answers to some frequently asked questions about Object Storage Service \(OSS\) volumes.

-   [Why do I fail to mount an OSS volume?](#section_td0_7vk_92o)
-   [Why does the OSS bucket directory that is mounted to a container become unavailable after the cluster that runs the pod is upgraded?](#section_n33_3bc_3jb)

## Why do I fail to mount an OSS volume?

Symptom:

Your attempt to mount an OSS volume failed.

Cause:

This issue may occur due to the following causes:

-   The AccessKey pair that you used is invalid.
-   The URL you used to mount the OSS volume cannot connect to the Internet or the internal network.

Solution:

-   Use a valid AccessKey pair.
-   Enable the URL to connect to the Internet or the internal network.

## Why does the OSS bucket directory that is mounted to a container become unavailable after the cluster that runs the pod is upgraded?

Symptom:

The OSS bucket directory that is mounted to a container becomes unavailable after the cluster that runs the pod is upgraded.

Cause:

After the cluster is upgraded, kubelet and the container network are restarted, which cause the OSSFS process to restart. As a result, the mapping between the host and the container directory becomes unavailable.

Solution:

Restart the container or recreate the pod to which the OSS bucket directory is mounted. You can configure the health check feature and enable the system to automatically restart a container or pod.

For more information about how to use OSS, see [Mount OSS volumes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/OSS volumes/Mount OSS volumes.md).

