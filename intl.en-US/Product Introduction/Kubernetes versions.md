# Kubernetes versions {#concept_186482 .concept}

This topic describes which Kubernetes versions are supported, the release cycle of Kubernetes versions, and the validity period of Kubernetes versions on Alibaba Cloud.

## Supported Kubernetes versions {#section_j78_nmh_0li .section}

Alibaba Cloud Container Service for Kubernetes \(ACK\) supports the following four Kubernetes versions: V1.12, V1.11, V1.10, and V1.9. With Kubernetes V1.12 or V1.11, you can create resources in the ACK console, and perform corresponding O&M operations. However, with Kubernetes V1.10 and V1.9, you can only perform O&M operations for the resources created before these versions. No resources can be created with Kubernetes V1.10 and V1.9.

## Release cycle {#section_96e_ia8_bo0 .section}

-   The Kubernetes Community releases a major Kubernetes version every three months.
-   After the Kubernetes Community releases a major Kubernetes version, ACK examines and tests the Kubernetes version within two or three months later, and then releases the corresponding Kubernetes version to the Container Service console where you can upgrade your Kubernetes version.

## Validity periods {#section_wvf_h9b_563 .section}

-   Each Kubernetes version released by ACK is valid for one year \(starting from the date when it was released\). For example, Kubernetes V1.12 was released on April 1, 2019, therefore this version will expire on March 31, 2020. You can view the validity period of a Kubernetes version on the page of upgrading a Kubernetes cluster.
-   After a Kubernetes version is released to all the regions of Alibaba Cloud, ACK provides the function for you to upgrade your current Kubernetes version to the newly released version within two or three weeks. Then, you can upgrade the version of your Kubernetes on the page for upgrading Kubernetes clusters. However, if your current Kubernetes version expires, it cannot be upgraded through the page for upgrade Kubernetes clusters.

