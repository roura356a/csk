# Kubernetes supported functions {#concept_pkm_jzz_xdb .concept}

**API version**

Kubernetes 1.9 API is supported.

**Application load**

-   Deployment, StatefulSet, Job/CronJob, Bare Pod are supported.
-   DaemonSet is not supported.

**Pod definition**

Supports starting multiple containers, setting environment variables, RestartPolicy, health check commands, and mounting volumes.

**Load balancing**

-   Supports creation of Load Balancer type applications.
-   Ingress is supported.
-   NodePort type is not supported.

**Configuration**

Secret and ConfigMap are supported.

**Storage**

-   EmptyDir and NFS volume types are supported.
-   PersistentVolume and PersistentVolumeClaim are not supported.

**Namespace**

Only the default namespace can be viewed, and no namespaces can be added.

**Node**

Node information of Kubernetes cannot be viewed.

**Events**

Default namespace events can be viewed.

**Containers logs**

View the container logs in real time by using kubectl logs.

**Container exec/attach**

Enter the container to run the commands by using kubectl exec.

