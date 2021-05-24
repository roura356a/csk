---
keyword: [basic concepts, K8s, terms, term explanation]
---

# Basic concepts

Before you start with Container Service for Kubernetes \(ACK\), we recommend that you familiarize yourself with the basic concepts of ACK. This topic introduces the basic concepts and terms that are commonly used in ACK.

-   **Cluster**

    A cluster is a collection of cloud resources that are required to run containers. These cloud resources include Elastic Compute Service \(ECS\) instances, Server Load Balancer \(SLB\) instances, and virtual private clouds \(VPCs\).

    The following table describes the cluster types provided by ACK.

    |Cluster type|Description|
    |------------|-----------|
    |Professional managed Kubernetes cluster|Professional managed Kubernetes clusters are developed based on standard managed Kubernetes clusters. This type of cluster provides enterprise users with higher reliability and security in large-scale production environments and is covered by service level agreements \(SLAs\) that include compensation clauses.|
    |Standard managed Kubernetes cluster|You need to create only worker nodes for a standard managed Kubernetes cluster. ACK automatically creates and manages master nodes. This type of cluster is easy to use, cost-effective, and highly available. It also saves the efforts on cluster O&M and management.|
    |Dedicated Kubernetes cluster|You must create three master nodes and several worker nodes for a dedicated Kubernetes cluster to ensure high availability. This type of cluster allows you to implement fine-grained control of cluster infrastructures. You must plan, maintain, and upgrade the Kubernetes cluster on your own.|
    |Kubernetes cluster for heterogeneous computing|This type of cluster supports nodes with heterogeneous computing capabilities, such as nodes with NVIDIA GPUs and Hanguang NPUs. This type of cluster also allows you to deploy CPU nodes and nodes with heterogeneous computing capabilities at the same time. It saves the efforts of driver installation and management. It supports mainstream AI computing frameworks and enables the sharing and isolation of GPU and NPU resources among multiple containers.|
    |Kubernetes cluster that supports sandboxed containers|You can create Elastic Bare Metal instances in a Kubernetes cluster that supports sandboxed containers. This provides high performance in scenarios where the load is heavy and bandwidth is high.|
    |Kubernetes cluster for confidential computing|You can create a managed Kubernetes cluster that is based on Intel Software Guard Extensions \(Intel SGX\). This type of cluster can protect your code and data, and is suitable in scenarios such as data protection, blockchain, key management, intellectual property rights, and genomics computing.|
    |Edge Kubernetes cluster|Managed edge Kubernetes clusters are provided by ACK to coordinate services in the cloud and on the edges. Managed edge Kubernetes clusters support features such as node autonomy, cell-based management, network traffic management, and native APIs to manage and maintain resources at the edge side. To use these features, you do not need to rewrite the logic of your services. This provides a native and centralized method for application lifecycle management and resource scheduling in edge computing scenarios.|
    |Serverless Kubernetes \(ASK\) cluster|You do not need to create or manage master nodes or worker nodes in an ASK cluster. You can use the ACK console or a command-line interface \(CLI\) to configure resources for containers, specify container images for applications, provide methods for external access, and start applications.|
    |Registered Kubernetes cluster|ACK allows you to register a Kubernetes cluster that is deployed in a data center or on a third-party cloud. This provides a centralized manner for the management of external Kubernetes clusters in the ACK console.|


-   **Node**

    A virtual machine \(VM\) or a physical server that has Docker Engine installed and can be used to deploy and manage containers. ACK installs an agent program on a node and registers the node with the cluster to which the node belongs. The number of nodes in a cluster can be scaled based on your business requirements.


-   **Node pool**

    A node pool contains one or more nodes in a cluster. The nodes in a node pool have the same configurations.

    ACK provides two types of node pools: regular node pools and managed node pools. Regular node pools include default node pools and custom node pools. You can enable auto scaling of custom node pools and managed node pools.

    |Type|Category|Description|
    |----|--------|-----------|
    |Regular node pool|Default node pool|A default node pool is associated with the scaling group that is automatically assigned when you create a cluster. No other scaling group is created for the default node pool.|
    |Custom node pool|Each custom node pool corresponds to a scaling group. When you scale a custom node pool, ACK uses the Auto Scaling \(ESS\) service to add or remove nodes.|
    |Managed node pool|Managed node pool|Managed node pools support auto upgrading and auto repairing. This provides centralized, managed, and operations and maintenance \(O&M\)-free lifecycle management of nodes. You do not need to be concerned about the O&M of nodes, such as component upgrading, OS upgrading, and patching to fix Common Vulnerabilities and Exposures \(CVE\)-identified vulnerabilities. ACK automatically fixes exceptions in nodes of a managed node pool.|


-   **Virtual Private Cloud \(VPC\)**

    A VPC is a private network dedicated for your use in the cloud. You can configure the CIDR block, route table, and gateways of your VPC. You can use Alibaba Cloud services in a VPC, such as Elastic Compute Service \(ECS\), ApsaraDB RDS \(RDS\), and Server Load Balancer \(SLB\).


-   **Security group**

    Security groups function as virtual firewalls that provide Stateful Packet Inspection \(SPI\) and packet filtering capabilities to isolate security domains in the cloud. A security group is a logically isolated, mutually accessible group of instances within the same region that have the same security requirements.


-   **App Catalog**

    App Catalog is integrated with Helm and supports Helm features. App Catalog also provides extended features based on Helm, such as the graphical user interface \(GUI\).


-   **Orchestration template**

    Orchestration templates provide methods to save Kubernetes YAML files.


-   **Knative**

    Knative is a Kubernetes-based serverless framework. Knative creates a cloud-native and cross-platform orchestration standard for serverless applications.


-   **Kubernetes**

    Kubernetes is an open source, portable, and extensible platform for the management of containerized workloads and services. Kubernetes simplifies declarative configuration and automation.


-   **Container**

    The container technology is used to package an application along with its runtime dependencies. A node can run multiple containers.


-   **Image**

    A container image is a standard form of an application package. A container image represents binary data that encapsulates an application and all its software dependencies. When you deploy a containerized application, you can use custom images from Docker Hub, Container Registry, or a private image repository. An image ID is a unique identifier composed of the URI of the image repository and the image tag. The default tag is `latest`.


-   **Image repository**

    An image repository is used to store container images of Kubernetes and application development.


-   **Master node**

    A master node is the manager of a Kubernetes cluster. It runs components, such as kube-apiserver, kube-scheduler, kube-controller-manager, etcd, and container network plug-ins.


-   **Worker node**

    A worker node runs workloads in a Kubernetes cluster. A worker node can be either a VM or a physical server. A worker node servers pod scheduling and communicates with master nodes. A worker node runs components, such as the Docker runtime environment, kubelet, kube-proxy, and other optional components.


-   **Namespace**

    Namespaces are used to divide Kubernetes cluster resources into virtual and isolated spaces. By default, a Kubernetes is initialized with three namespaces: default, kube-system, and kube-public. The cluster administrator can also create new namespaces based on business requirements.


-   **Pod**

    A pod is the smallest deployable unit that can be created and managed when you deploy applications or services in Kubernetes. A pod encapsulates one or more containers, storage resources, a unique IP address, and configurations that specify how the containers run.


-   **ReplicationController**

    A ReplicationController ensures that a specified number of pod replicas are always running in a Kubernetes cluster. A ReplicationController monitors the number of running pods and adjusts them to the specified number. The specified number can be 1 or more. If the number of pod replicas is lower than the specified value, a ReplicationController starts new pod replicas. If the number of pod replicas exceeds the specified value, a ReplicationController stops redundant pod replicas.


-   **ReplicaSet**

    ReplicaSet is the successor to ReplicationController. Compared with ReplicationControllers, ReplicaSets support more selector types. ReplicaSets are not independently used, but are used by Deployments to reach the desired number of pods replicas.


-   **Workload**

    Workloads are applications that run in Kubernetes. Workloads include the following types:

    |Workload type|Description|
    |-------------|-----------|
    |Deployment|A Deployment performs a one-time operation on a Kubernetes cluster. Deployments are suitable for running applications that have the same features and are totally independent of each other.|
    |StatefulSet|A StatefulSet ensures the orderly deployment, scaling, and rolling update of applications. If you want to use storage volumes to persist data for workloads, you can deploy applications by using StatefulSets.|
    |DaemonSet|A DaemonSet ensures that all \(or some\) nodes run a pod. Different from a Deployment, a DaemonSet creates pods on specified nodes and ensures that all specified nodes run the DaemonSet pods. DaemonSets are suitable for logging and monitoring Kubernetes clusters.|
    |Job|A Job runs a one-time task. You can use a Job to run multiple pods in parallel.|
    |CronJob|A CronJob performs periodic or recurring operations based on a schedule. CronJobs are suitable for scenarios such as backing up data and sending emails.|
    |CustomResourceDefinition \(CRD\)|You can use CRDs to add third-party workloads to Kubernetes. CRDs provide a method to define custom resources.|


-   **Label**

    Labels are key-value pairs that are added to resource objects. Labels are intended to specify attributes of objects that are useful and relevant to users. The labels do not imply semantics to the core system. Labels can be added to an object when it is created, and added or modified at any time after it is created. Each object can have multiple labels. However, the key of each label must be unique.


-   **Service**

    Service is a basic operation unit in Kubernetes. A Service is an abstraction of real applications and services. Each Service has multiple containers as its backend. The ports opened by kube-proxy port and the selectors specified in Service configurations determine the backend container to which a request is forwarded by a Service. Then, a single access interface is provided to handle external requests. In this case, external users do not need to be concerned about how the backend works. This brings great convenience for the scaling and maintenance of the backend.


-   **Ingress**

    An Ingress is a collection of rules that authorize external access to cluster services. You can use Ingresses to configure URLs, SLB instances, Secure Sockets Layer \(SSL\) connections, and name-based virtual hosts that provide external access to services. You can request an Ingress by posting the Ingress resource to the cluster API server. An Ingress controller is used to implement an Ingress. In most cases, a load balancer is used for the implementation. An Ingress controller can also be used to configure edge routes and additional frontends and handle traffic with high availability.


-   **ConfigMap**

    ConfigMaps can be used to store fine-grained information, such as an attribute. ConfigMaps can also be used to store coarse-grained information, such as configuration files or JSON objects. You can use ConfigMaps to store non-sensitive, unencrypted configuration information.


-   **Secret**

    Secrets are used to store and manage sensitive information in Kubernetes clusters, such as passwords and certificates.


-   **Volume**

    Similar to Docker volumes, which serve a container, Kubernetes volumes serve a pod within its lifetime. The volumes declared in a pod are shared by all containers in the pod.


-   **Persistent volume \(PV\)**

    A PV is a storage resource in a Kubernetes cluster just like a node is a cluster resource. A PV has a lifecycle independent of any pod that uses the PV. You can create different types of PVs by using different types of StorageClasses.


-   **Persistent volume claim \(PVC\)**

    PVCs consume resources. Pods consume node resources and PVCs consume PV resources.


-   **StorageClass**

    StorageClasses enable the dynamic provisioning of PVs. You can use dynamic provisioning to automatically create PVs based on your requirements.


-   **Auto scaling**

    Auto scaling can automatically scale computing resources based on scaling policies to meet your business requirements. This provides a cost-effective method to manage resources. Typically, auto scaling is used in scenarios such as online workload scaling, large-scale computing and training, GPU-accelerated deep learning, inference and training based on shared GPU resources, and periodical workload scheduling. The following table describes the auto scaling service provided by ACK.

    |Scaling aspect|Scaling category|Description|
    |--------------|----------------|-----------|
    |Workload scaling|Horizontal pod autoscaling|Horizontal Pod Autoscaler \(HPA\) automatically scales pods based on CPU usage. HPA can be used to scale only workloads that can be scaled, such as Deployments and StatefulSets.|
    |Cron horizontal pod autoscaling|To reduce resource waste in some scenarios, ACK provides the kubernetes-cronhpa-controller component to automatically scale resources based on predefined schedules. CronHPA can be used to scale only workloads that can be scaled, such as Deployments and StatefulSets. CronHPA is also compatible with Horizontal Pod Autoscaler \(HPA\). You can use CronHPA and HPA in combination to scale workloads.|
    |Vertical pod autoscaling|Vertical pod autoscaling automatically sets limits on the usage of cluster resources based on the resource usage of pods. This way, ACK can schedule pods to nodes with sufficient resources. Vertical Pod Autoscaler \(VPA\) also maintains the amount of resources specified by `request` and `limit` in the initial container configuration. Vertical pod autoscaling is applicable to applications that cannot be horizontally scaled. In most cases, vertical pod autoscaling is used when exceptions in pods are resolved.|
    |Resource scaling|Auto scaling of nodes|ACK provides a component for the auto scaling of nodes. Regular instances, GPU-accelerated instances, and preemptible instances can be automatically added to or removed from an ACK cluster based on your requirements. This feature provides multiple scaling modes, supports various instance types and multi-zone instances, and meets your scaling requirements in different scenarios. This feature is applicable to all scenarios, especially online workloads, deep learning, and large-scale computing.|


-   **Observability**

    The observability of Kubernetes includes monitoring and logging. Monitoring allows developers to keep track of the operations of a system. Logging facilitates diagnostics and troubleshooting.


-   **Helm**

    Helm is a package management platform for Kubernetes. A chart describes a related set of resources that can be used to deploy an application. You can use charts to manage application packages.


-   **nodeAffinity**

    You can configure nodeAffinity to schedule pods to worker nodes with matching labels.


-   **Taint**

    Taints are opposite to nodeAffinity, and allow nodes to repel specific pods.


-   **Toleration**

    Tolerations are applied to pods, and allow \(but do not require\) pods to be scheduled to nodes with matching taints.


-   **podAffinity**

    You can configure podAffinity to deploy pods to the same topological domain as pods that meet the podAffinity settings. For example, you can use podAffinity to deploy services that communicate with each other to the same topological domain, such as a host. This reduces the network latency between these services.


-   **podAntiAffinity**

    You can configure podAntiAffinity to not deploy pods to the same topological domain as pods that meet the podAffinity settings. For example, you can use podAntiAffinity to schedule the pods of a service to different topological domains, such as multiple hosts. This allows you to enhance the stability of the service.


-   **Service mesh \(Istio\)**

    Istio is an open platform that allows you to connect, protect, control, and observe microservices. Alibaba Cloud Service Mesh \(ASM\) is a fully managed service mesh platform. ASM is compatible with the open source Istio service mesh. ASM allows you to manage services in a simplified manner. For example, you can use ASM to route and split inter-service traffic, secure inter-service communication with authentication, and observe the behavior of services in meshes.


## References

For more information about Kubernetes terms, see [Kubernetes concepts](https://kubernetes.io/docs/concepts/).

