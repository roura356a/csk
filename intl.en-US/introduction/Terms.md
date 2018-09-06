# Terms {#concept_b4m_chb_5db .concept}

## Basic terms {#section_bsn_phb_5db .section}

**Cluster**

A collection of cloud resources that are required to run containers. It associates with several server nodes, Server Load Balancer instances, Virtual Private Cloud \(VPC\), and other cloud resources.

**Node**

A server \(either an Elastic Compute Service \(ECS\) instance or a physical server\) that has a Docker Engine installed and is used to deploy and manage containers. The Agent program of Container Service is installed in a node and registered to a cluster. The number of nodes in a cluster is scalable.

**Container**

A running instance created by using a Docker image. A single node can run multiple containers.

**Image**

A standard packaging format of a container application in Docker. You can specify an image to deploy containerized applications. The image can be from the Docker Hub, Alibaba Cloud image service, or your private registry. An image ID is uniquely identified by the URI of the image repository and the image tag \(latest by default\).

## Kubernetes terms {#section_bvw_flb_5db .section}

**Node**

Nodes in Kubernetes clusters provide the computing power and are the active servers where all the pods are running. The active servers can be physical machines or virtual machines. Containers that are running on the kubelet management nodes must run on the active servers.

**Namespace**

The namespace provides the virtual isolation for Kubernetes clusters.  By default, Kubernetes clusters have three namespaces: the default namespace default, the system namespaces kube-system, and kube-public. The administrator can also create new namespaces to meet the requirements.

**Pod**

The minimum basic unit of Kubernetes that is used to deploy applications or services.  A pod can encapsulate one or multiple containers, storage resources, an independent network IP address, and policy options of managing and controlling the running method of containers.

**Replication Controller**

Replication Controller \(RC\) makes sure that a specified number of pod replicas are running in a Kubernetes cluster at any time  Replication Controller \(RC\) makes sure that a specified number of pod replicas are running in a Kubernetes cluster at any time by monitoring the running pod.  One or more pod replicas can be specified. If the number of pod replicas is less than the specified number, RC starts to run new pod replicas. If the number of pod replicas exceeds the specified number, RC starts to stop the redundant pod replicas.

**Replica Set**

The upgraded version of RC. The only difference between Replica Set \(RS\) and RC is the support for selector. RS supports more types of matching modes.  Generally, the RS objects are not used independently, but are used as the deployment parameters in the ideal status.

**Deployment**

The deployment indicates an update operation for a Kubernetes cluster by users,   has a wider application range than RS, and can create a service, update a service, and perform a rolling update of a service.   Performing a rolling update of a service actually creates a new RS, gradually adds the number of replicas in the new RS to the ideal status, and reduces the number of replicas in the old RS to zero. Such a compound operation cannot be described well by an RS, but can be described by a more common deployment.  We do not recommend that you manually mange and use the RS created by the deployment.

**Service**

The basic operation unit of Kubernetes. As the abstract of real application service, each service has many containers to provide the support. The port of Kube-Proxy and service selector determine the service request to pass to the backend container, and a single access interface is displayed externally. The external is not required to know how the backend works, which is good for expanding or maintaining the backend.

**Labels**

Essentially a collection of key-value pairs that are attached to the resource objects. Labels are used to specify the attributes of objects that are meaningful for users, but do not have any direct significance for kernel systems.  You can add a label directly when creating an object or modify the label at any time. Each object can have more labels, but the key value must be unique.

**Volume**

The volumes in Kubernetes clusters are similar to the Docker volumes. The only difference is that the range of Docker volumes is a container, while the lifecycle and range of Kubernetes volumes are a pod.  The volumes declared in each pod are shared by all the containers in the pod.  You can use the Persistent Volume Claim \(PVC\) logical storage, and ignore the actual storage technology in the backend. The specific configurations about Persistent Volume \(PV\) are completed by the storage administrator.

**PV and PVC**

PV and PVC allow the Kubernetes clusters to have the abstract logical capabilities of storage, and you can ignore the configurations of actual backend storage technology in the pod configuration logic, leaving the configurations to the PV configurator.  The relationship between PV and PVC of storage is similar to that between node and pod of computing. PV and node are the resource provider, changed according to the cluster infrastructure, and configured by the Kubernetes cluster administrator. PVC and pod are the resource user, changed according to the business and service requirements, and configured by the Kubernetes cluster user, namely, the service administrator.

**Ingress**

A collection of rules that authorize the inbound access to the cluster.  You can provide the externally accessible URL, Server Load Balancer, SSL, and name-based virtual host by using the Ingress configurations.  You can request the Ingress by posting Ingress resources to API servers.  The Ingress Controller is used to implement Ingress by using Server Load Balancer generally, and can configure the edge router and other frontends, which helps you handle the traffic in the HA method.

## Related documents. {#section_ijb_lmb_5db .section}

-   [Docker glossary](https://docs.docker.com/glossary/)
-   [Kubernetes concepts](https://kubernetes.io/docs/concepts/)

