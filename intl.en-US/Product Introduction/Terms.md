# Terms

This topic describes the common terms that are used in Container Service for Kubernetes \(ACK\).

## Basic terms

-   **cluster**

    A collection of cloud resources that are required to run containers. Several cloud resources are combined to form a cluster. These cloud resources can be Elastic Compute Service \(ECS\) instances, Server Load Balancer \(SLB\) instances, or virtual private clouds \(VPCs\).


-   **managed Kubernetes cluster**

    A cluster for which you only need to create worker nodes. This type of ACK cluster is easy to use with low cost and high availability. You do not need to manage the master nodes of the ACK cluster.


-   **dedicated Kubernetes cluster**

    A cluster for which you must create three master nodes and several worker nodes to achieve high availability. This type of ACK cluster allows you to adopt a fine-grained approach to manage the cluster infrastructure. However, it requires that you manually plan, maintain, and upgrade the ACK cluster.


-   **serverless Kubernetes cluster**

    A cluster for which you do not need to create and manage any master nodes or worker nodes. You can use the ACK console or command-line interface \(CLI\) to configure resources for containers, specify container images for applications, provide methods for external access, and start applications.


-   **node**

    A virtual machine \(VM\) or a physical server that has Docker Engine installed and is used to deploy and manage containers. The Agent program of ACK is installed on a node and registered with a cluster. The number of nodes in a cluster can be scaled based on your business requirements.


-   **container**

    A runtime instance that is created from a Docker image. A single node can run multiple containers


-   **image**

    A standard packaging format of a containerized application in Docker. An image from Docker Hub, Alibaba Cloud Container Registry, or your private registry can be specified to deploy its packaged containerized application. An image ID is a unique identifier that is composed of the image repository URI and image tag. The default tag is latest.


## Kubernetes terms

-   **master node**

    The manager of an ACK cluster. It runs components such as kube-apiserver, kube-scheduler, kube-controller-manager, etcd, and container network. In most cases, three master nodes are deployed to ensure high availability.


-   **worker node**

    A node in an ACK cluster that serves your workloads. It can be either a VM or a physical server. A worker node schedules pods and communicates with the master node. Components that run on a worker node include the Docker runtime environment, kubelet, kube-proxy, and other optional add-on components.


-   **namespace**

    A method that is used in ACK to divide cluster resources between multiple users. By default, ACK starts with three initial namespaces: default, kube-system, and kube-public. Administrators can also create new namespaces as required.


-   **Pod**

    The smallest deployable computing unit that can be created and managed in ACK. A pod encapsulates one or more containers, storage resources, a unique network IP address, and options that specify how the containers run.


-   **replication controller \(RC\)**

    A feature that monitors running pods to ensure that a specified number of pod replicas are running at a time. One or more pod replicas can be specified. If the number of pod replicas is lower than the specified value, an RC starts new pod replicas. If the number of pod replicas exceeds the specified value, the RC deletes the redundant pod replicas.


-   **replica set \(RS\)**

    The upgraded version of RC. Compared with RCs, RSs support more selector types. RS objects are not independently used, but are used as deployment parameters under ideal conditions.


-   **deployment**

    An update operation performed on an ACK cluster. Deployment is more widely applied than RS. You can use deployments to create, update, or perform rolling updates for services. A new RS is created when you perform a rolling update for a service. A compound operation is performed. This allows you to increase the number of replicas in the new RS to a specified value. This applies to the original RS when the number of replicas decreases to zero. We recommend that you perform this compound operation during a deployment. We recommend that you do not attempt to manually manage the RS that is created in a deployment.


-   **service**

    The basic operation unit of ACK. It is an abstraction of real application services. Each service has multiple containers that support the service. The kube-proxy port and service selector specify the backend container to which a service request is forwarded. Then, a single access interface is provided to process external requests. In this case, users do not need to be concerned about how to scale or maintain the backend.


-   **labels**

    A collection of key-value pairs that are attached to resource objects. Labels are intended to specify attributes of objects that are useful and correspond to users. These labels are designed for users and do not affect core system. Labels can be attached to objects when the objects are created, and subsequently added and modified at your convenience. Each object can have a set of key-value labels, and each key must be unique for a specified object.


-   **volume**

    ACK volumes are similar to Docker volumes. However, these volumes are different in one key aspect. Docker volumes are used to persist data in Docker containers. ACK volumes share the same lifetime as the pods that enclose the volumes. The volumes declared in each pod are shared by all containers in the pod. You do not need to be concerned about the actual backend storage technology when you use persistent volume claim \(PVC\) logical storage. In similar cases, administrators specify the configurations for persistent volumes \(PVs\).


-   **PV and PVC**

    PVs and PVCs allow ACK clusters to provide a logical abstraction over the storage resources. This way, the actual settings of backend storage can be ignored for PVCs. In similar cases that are based on PVs, these settings are specified by the PV configurators. The relationship between PVs and PVCs is similar to that between nodes and pods. PVs and nodes are resource providers which can vary based on cluster infrastructure, and are configured by the administrators of an ACK cluster. PVCs and pods are resource consumers that can vary based on service requirements, and are configured by either the users or service administrators of an ACK cluster.


-   **Ingress**

    A collection of rules that allow inbound access to cluster services. An Ingress can be configured to provide services with externally-accessible URLs, implement load balancing, terminate SSL, and offer name-based virtual hosting. You can request the Ingress by posting Ingress resources to API servers. An Ingress controller requires an Ingress. In most cases, this requires a load balancer. It can also be used to configure your edge router or additional frontends and process network traffic.


## References

-   [Docker glossary](https://docs.docker.com/glossary/)
-   [Kubernetes concepts](https://kubernetes.io/docs/concepts/)

