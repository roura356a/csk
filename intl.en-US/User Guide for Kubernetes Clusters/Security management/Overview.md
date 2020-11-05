# Overview

## Authorization management

Container Service for Kubernetes \(ACK\) allows you to authorize Resource Access Management \(RAM\) users to manage ACK clusters.

For more information, see [RAM authorization](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Overview.md).

## End-to-end TLS certificate verification

In ACK clusters, TLS certificates are verified when the following connections are established. TLS certificate verification prevents data eavesdropping and tempering.

-   When `kubelet` on a worker node establishes a connection to `apiserver` on a master node.
-   When `apiserver` on a master node establishes a connection to `kubelet` on a worker node.

To complete the initialization process, the worker node that launches the initialization connects to Secure Shell \(SSH\) servers on other nodes through SSH tunnels. The SSH service port is port 22.

## Support for Kubernetes-native Secrets and RBAC

Kubernetes Secrets are used to store sensitive information such as passwords, OAuth tokens, and SSH keys. Using pod YAML files or Docker images to store sensitive information may result in information leakage because the information is stored in plain text. To avoid leaking sensitive information, you can use Secrets.

For more information, see [Secrets](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/auth/secrets.md).

Role-Based Access Control \(RBAC\) uses built-in API groups provided by Kubernetes to manage authorization and authentication. You can call the API to authorize different RBAC roles to manage different pods and regulate access to resources based on these roles.

For more information, see [Using RBAC Authorization](https://kubernetes.io/docs/admin/authorization/rbac/).

## Network isolation

By default, pods on different nodes of an ACK cluster are allowed to communicate with each other. In some scenarios, you may need to disallow intercommunication between different networks. This requires you to isolate networks by using network policies. In ACK clusters, you can use the Canal driver to isolate networks.

## Image scans

You can use Container Registry to manage and scan images for ACK clusters.

Image scans can detect security risks in images and protect applications running in ACK clusters against attacks.

For more information, see [Image Scanning]().

## Security groups and Internet access

By default, each new cluster is allocated a new security group with the minimum security risks. This security group allows only inbound Internet Control Message Protocol \(ICMP\) traffic transmitted over the Internet.

By default, you are disallowed to access to an ACK cluster through SSH over the Internet. For more information about how to access nodes by using SSH, see [create an SSH connection with a cluster node over the Internet](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use SSH to connect to an ACK cluster.md).

Nodes in an ACK cluster access the Internet through NAT gateways. This reduces security risks.

