# Security {#concept_jyf_5sb_wdb .concept}

## Authorization {#section_ppd_vsb_wdb .section}

Kubernetes clusters support authorizing RAM users to perform operations on clusters.

For more information, see [Use sub-accounts](reseller.en-US/User Guide/Kubernetes cluster/授权管理/Use sub-accounts.md#).

## Full-link TLS certificates {#section_qpd_vsb_wdb .section}

The following communication links in Container Service Kubernetes clusters are verified by TLS certificates to prevent the communication from being eavesdropped or tampered:

-   `kubelet` on worker nodes actively communicates with `apiserver` on master nodes
-   `apiserver` on master nodes actively communicates with `kubelet` on worker nodes

During initialization, the master node uses SSH tunnels to connect to the SSH service of other nodes \(port 22\) for initialization.

## Native secret & RBAC support {#section_spd_vsb_wdb .section}

Kubernetes secrets are used to store sensitive information such as passwords, OAuth tokens, and SSH keys. Using plain text to write sensitive information to a pod YAML file or a Docker image may leak the information, while using secrets avoids such security risks effectively.

For more information, see [Secret](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/auth/secrets.md).

Role-Based Access Control \(RBAC\) uses the Kubernetes built-in API group to drive authorization and authentication, which allows you to use APIs to manage pods that correspond to different roles, and the access permissions of roles.

For more information, see [Using RBAC authorization](https://kubernetes.io/docs/admin/authorization/rbac/).

## Network policy {#section_tpd_vsb_wdb .section}

In a Kubernetes cluster, pods on different nodes can communicate with each other by default. In some scenarios, to reduce risks, the network intercommunication among different business services is not allowed and you must introduce the network policy. In Kubernetes clusters, you can use the Canal network driver to implement the support for network policy.

## Image security scan {#section_upd_vsb_wdb .section}

Kubernetes clusters can use Container Registry to manage images, which allows you to perform image security scan.

Image security scan identifies the security risks in images quickly and reduces the possibility of applications running on your Kubernetes cluster being attacked.

## Security group and Internet access {#section_vpd_vsb_wdb .section}

By default, each newly created Kubernetes cluster is assigned a new security group with the minimal security risk. This security group only allows ICMP for the Internet inbound.

By default, you cannot use Internet SSH to access your clusters. To use Internet SSH to connect to the cluster nodes, see [Access Kubernetes clusters by using SSH](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/SSH访问Kubernetes集群.md#).

The cluster nodes access the Internet by using the NAT Gateway, which further reduces the security risks.

