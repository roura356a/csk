---
keyword: [container storage, Kubernetes, principles]
---

# Storage basics

Container Service for Kubernetes \(ACK\) uses the Kubernetes orchestration system as a management platform for clusters, applications, storage, networks and other modules. This topic describes the basics about container storage in ACK. You can use this topic to help you understand the basics and principles of storage modules when you use the storage features provided by ACK.

## Volumes

On-disk files in a container are ephemeral. The Kubernetes volume abstraction solves the problem that files are lost when the container restarts. A volume is a data transfer channel between a pod and an external storage device. A volume also enables data sharing between containers in a pod, between pods, and between a pod and the external environment.

A volume defines the details of external storage and is embedded in a pod. A volume is essentially a resource mapping to external storage in the Kubernetes system. When a workload requires external storage, the system queries related information in the volume and mounts external storage.

**Note:** A volume has the same lifecycle as the pod that uses the volume. When the pod is deleted, the volume is deleted at the same time. Whether the data in the volume is retained depends on the volume type.

Kubernetes provides a wide variety of volume types. ACK supports the following commonly used volume types:

|Volume|Description|
|------|-----------|
|Local storage|You can mount local storage as volumes, such as HostPath and emptyDir volumes. These volumes store data on specific nodes of the cluster and do not drift with applications. The stored data becomes unavailable when the nodes are down.|
|Network storage|You can mount network storage as volumes, such as Ceph, GlusterFS, NFS, and iSCSI volumes. These volumes store data by using remote storage services. When you use these volumes, you must mount the storage services locally.|
|Secrets and ConfigMaps|Secrets and ConfigMaps are special volumes that store the object information about a cluster. Object data is mounted as volumes to nodes for use by applications.|
|PVC|Persistent volume claims \(PVCs\) provide a mechanism for defining volumes. A PVC abstracts a volume into a pod-independent object. The storage information that is defined for or associated with this object is stored in a volume and mounted for use by Kubernetes workloads.|

**Note:** Container Storage Interface \(CSI\) and FlexVolume are two extensions of volumes. Each extension can be divided into different StorageClasses. For more information about CSI, see [Overview](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Overview.md).

**Notes about volumes**

-   A pod can mount multiple volumes.
-   A pod can mount multiple types of volume.
-   A volume that is mounted on a pod can be shared among the containers in the pod.
-   We recommend that you create persistent volumes \(PVs\) and PVCs to mount volumes in Kubernetes.
-   We recommend that you do not mount an excess number of volumes on a pod.

## PVs and PVCs

Not all Kubernetes volumes are persistent. Container storage requires a remote storage service to enable data persistence. To do this, Kubernetes introduces two resource objects, PV and PVC, which abstract details of how storage is implemented from how it is consumed, and decouple the responsibility of the user from the system administrator. The details of PV and PVC are:

-   **PV**

    PV is the abbreviation for persistent volume. PVs are a specific type of volume in Kubernetes. A PV object defines a specific StorageClass and a set of volume parameters. All information about the target storage service is stored in a PV object. Kubernetes references the information stored in the PV object to mount volumes.


**Note:** PVs are used at the cluster level instead of the node level. A PV has its own lifecycle, which is independent of the pod lifecycle.

-   **PVC**

    PVC is the abbreviation for persistent volume claim. PVCs are a type of abstract volume in Kubernetes and represents the data volume of a specific StorageClass. PVCs are designed to separate storage from application orchestration. A PVC object abstracts storage details and implements storage orchestration. This makes storage volume objects independent of application orchestration in Kubernetes and decouples applications from storage at the orchestration layer.


**Notes about PVs and PVCs**

-   **The binding of PVs and PVCs**

    One PVC object is bound to only one PV object. One PV object cannot be bound to multiple PVC objects, and one PVC object cannot be bound to multiple PV objects. To configure storage for an application, you must declare a PVC object. Kubernetes selects the PV object that best fits the PVC object and binds them together. PVCs are a type of storage object used by applications and belong to the application domain. PVs are a type of storage object that belongs to the storage domain.

    A PVC object must be bound to a PV object before it can be consumed by a pod. The process of binding a PVC object to a PV object is the process of PV consumption. Only a PV object that meets the following requirements can be bound to a PVC object:

    -   VolumeMode: The PV object to be consumed must be in the same volume mode as the PVC object.
    -   AccessMode: The PV object to be consumed must be in the same access mode as the PVC object.
    -   StorageClassName: If this parameter is defined for a PVC object, only a PV object that has the corresponding parameters defined can be bound to this PVC object.
    -   LabelSelector: The appropriate PV object is selected from a PV list based on label matching.
    -   size: The PV object to be consumed must have a storage capacity that is no less than that of the PVC object.
-   **The size fields in PV and PVC definitions**

    The size fields in PV and PVC definitions have the following uses:

    -   The system determines whether a PV object and a PVC object can be bound based on the size parameters.
    -   To dynamically create a PV object based on a PVC object and a StorageClass, some storage types determine the capacities of the PV object and backend storage based on the size of the PVC object.
    -   For storage types that support resize operations, the size of the PVC object is used as the capacities of the PV object and backend storage after the scale-out.
    The size values of a PVC object and a PV object are used as configuration parameters when the system performs operations on the PVC object and PV object.

    When data is written to the volume, the size fields of the PVC object and PV object are not referenced. Instead, write operations depend on the actual capacity of the underlying storage medium.


## How to use volumes

Volumes are commonly used in the following ways:

-   **Static volumes**

    Static volumes are PV objects created by administrators. All volumes can be created as static volumes.

    The cluster administrator analyzes the storage needs of the cluster and pre-allocates storage media, such as disks and NAS file systems. The administrator also creates PV objects to be consumed by PVC objects. If PVC needs are defined in workloads, Kubernetes binds PVC and PV objects based on relevant rules. This allows applications to access storage services.

-   **Dynamic volumes**

    Dynamic volumes are PV objects automatically created by the storage plug-in.

    The cluster administrator configures a backend storage pool and creates a StorageClass. When a PVC object needs to consume a PV object, the storage plug-in dynamically creates a PV object based on the PVC needs and the details of the StorageClass. The definition of StorageClass is:

    -   **StorageClass**

        When you declare a PVC object, you can add the StorageClassName field to this PVC object. This allows the Provisioner plug-in to create a suitable PV object based on the definition of StorageClassName when no PV object in the cluster fits the declared PVC object. This creates a dynamic volume.

        The dynamic volume is created by the Provisioner plug-in and bound with the PVC object based on StorageClassName.

        A StorageClass is a template used to create a PV object. When a PVC object triggers the automatic PV creation process, a PV object is created based on the content of a StorageClass. The following example shows the content of a StorageClass:

        ```
        apiVersion: storage.k8s.io/v1
        kind: StorageClass
        metadata:
          name: alicloud-disk-topology
        parameters:
          type: cloud_ssd
        provisioner: diskplugin.csi.alibabacloud.com
        reclaimPolicy: Delete
        allowVolumeExpansion: true
        volumeBindingMode: WaitForFirstConsumer
        ```

        |Parameter|Description|
        |---------|-----------|
        |reclaimPolicy|Specifies the value of the persistentVolumeReclaimPolicy field used to create a PV object. Valid values: Delete and Retain.         -   Delete specifies that a dynamically created PV object is automatically released when the bound PVC object is released.
        -   Retain specifies that a PV object is dynamically created, but must be released by the administrator. |
        |allowVolumeExpansion|Specifies whether the PV object created based on the current StorageClass performs dynamic scale-out. Default value: `false`. This parameter is used only to enable or disable dynamic scale-out. Whether to enable this feature is determined by the underlying storage plug-in.|
        |volumeBindingMode|Specifies the time when PV objects are dynamically created. Valid values: Immediate \(immediate creation\) and WaitForFirstConsumer \(delayed creation\).|

    -   **Delayed binding**

        Certain types of storage, such as Alibaba Cloud disks, impose limits on the mount attribute. For example, volumes can be mounted to only nodes in the same zone as the volumes. This type of volume encounters the following problems:

        -   A volume is created in Zone A, but Zone A has no available node resources. Therefore, the created volume cannot be mounted to a pod upon startup.
        -   When cluster administrators plan PVC and PV objects, they cannot determine in which zones they can create multiple PV objects for backup.
        The StorageClass template provides the volumeBindingMode field to fix the preceding problems. When volumeBindingMode is set to WaitForFirstConsumer, the storage plug-in delays volume creation when it receives the PVC pending state. Instead, the storage plug-in creates a volume only after the PVC object is consumed by a pod.

        **Note:**

        The process of delayed binding:

        The Provisioner plug-in delays volume creation when it receives the PVC pending state. Instead, the plug-in creates a volume only after the PVC object is consumed by a pod. If a pod consumes the PVC object and the scheduler determines that the PVC object has delayed binding enabled, the PV scheduling process continues. The scheduler patches the scheduling result to the metadata of the PVC object. When the Provisioner plug-in determines that scheduling information has been written to the PVC object, it retrieves location information such as the zone and node based on the scheduling information to create a volume. Then, the plug-in triggers the PV creation process.

        We recommend that you use the delayed binding feature when you create dynamic volumes in a multi-zone cluster. The preceding configuration process is supported by ACK.

    **Advantages of dynamic volumes**

    -   Dynamic volumes allow Kubernetes to implement automatic PV lifecycle management. PV objects are created and deleted by the Provisioner plug-in.
    -   PV objects can be automatically created, which simplifies configuration and reduces the workload of system administrators.
    -   Dynamic volumes maintain consistency between the PVC-required storage capacity and the PV capacity that is configured by the Provisioner plug-in. This optimizes storage capacity planning.
-   **Mount SubPath volumes**

    Kubernetes provides the `volumeMounts.subPath` property, which can be used to specify a subpath inside the referenced volume instead of its root.

    **Note:** We recommend that you do not use SubPath volumes because the mount and unmount of SubPath volumes may cause errors in some Kubernetes versions. You can use the Container Storage Interface \(CSI\) plug-in to mount subdirectories, such as those of NAS file systems and OSS buckets.


## Quota limits

-   Certain types of storage impose quota limits on the number of volumes that can mounted on a node. For example, you can mount up to 16 disks as volumes on a node. ACK provides scheduling policies that meet quota limits. When the number of volumes that are mounted on a node reaches the quota, the scheduler no longer schedules the specified type of volume to this node.
-   The CSI plug-in provided by ACK allows you to use the MAX\_VOLUMES\_PERNODE field to configure the number of volumes per node. For more information about the CSI plug-in, see [Overview](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Overview.md).

## References

-   For more information about Kubernetes storage, see [Storage](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/).
-   For more information about the CSI plug-in, see [Overview](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Overview.md).

