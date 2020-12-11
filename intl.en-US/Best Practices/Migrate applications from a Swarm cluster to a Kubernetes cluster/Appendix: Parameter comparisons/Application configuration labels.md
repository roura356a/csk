# Application configuration labels

This topic describes the labels that are related to application configurations in clusters of Container Service for Swarm.

|Swarm label|Description|Related Kubernetes parameter|Swarm configuration example|Kubernetes configuration example|How to migrate|
|-----------|-----------|----------------------------|---------------------------|--------------------------------|--------------|
|name|The name of a Service. This label does not have specific use and can be ignored.

|Not supported in Kubernetes|-|-|No need to migrate|
|image|The container image.|image|-|-|Automatically converted by Kompose|
|ports|The container ports.|containerPort. If your Kubernetes cluster is accessed by external systems, you must create a NodePort type Service.|-|-|Automatically converted by Kompose|
|environment|The container environment variables.|env|-|-|Automatically converted by Kompose|
|volumes|The directories or volumes of the host, which are used by a container.|volumeMounts

 volumes

|For more information, see the Swarm configuration example in [volumes](#section_pcb_c7j_vel).|For more information, see the Kubernetes configuration example in [volumes](#section_pcb_c7j_vel).|Automatically converted by Kompose|
|cap\_add/cap\_drop|Grants a container the permissions to modify the kernel or revokes the permissions to modify the kernel from a container.|securityContext:capabilities|For more information, see the Swarm configuration example in [cap\_add/cap\_drop](#section_mxi_sgh_gi2).|For more information, see the Kubernetes configuration example in [cap\_add/cap\_drop](#section_mxi_sgh_gi2).|Automatically converted by Kompose|
|privileged: true|Grants privileged permissions to the root account of a container. If you do not specify this parameter, the root account of a container has only permissions of a standard user.

 In a container that starts in privileged mode, you can view devices of the host. You can also mount devices to the container. You can start another Docker container in the container.

|securityContext:privileged|For more information, see the Swarm configuration example in [privileged: true](#section_lzl_ixx_0up).|For more information, see the Kubernetes configuration example in [privileged: true](#section_lzl_ixx_0up).|Automatically converted by Kompose|
|mem\_limit|The maximum amount of memory that can be used by a container.|resource:request/limits|For more information, see the Swarm configuration example in [mem\_limit](#section_hkh_fy9_u7f).|For more information, see the Kubernetes configuration example in [mem\_limit](#section_hkh_fy9_u7f).|Automatically converted by Kompose|
|cpu\_shares|The maximum number of cores that can be used by a container.|resource:request/limits|For more information, see the Swarm configuration example in [cpu\_shares](#section_tro_t0f_3pt).|For more information, see the Kubernetes configuration example in [cpu\_shares](#section_tro_t0f_3pt).|Automatically converted by Kompose|
|kernel\_memory|The same as the related parameter of the [docker run](https://docs.docker.com/engine/reference/run/) command.|Not supported in Kubernetes|-|-|No need to migrate|
|memswap\_reservation|The same as the related parameter of the [docker run](https://docs.docker.com/engine/reference/run/) command.|Not supported in Kubernetes|-|-|No need to migrate|
|memswap\_limit|The same as the related parameter of the [docker run](https://docs.docker.com/engine/reference/run/) command.|Not supported in Kubernetes|-|-|No need to migrate|
|shm\_size|The same as the related parameter of the [docker run](https://docs.docker.com/engine/reference/run/) command.|Not supported in Kubernetes|-|-|No need to migrate|
|oom-kill-disable|Specifies whether to disable the Out of Memory \(OOM\) killer for the container. It is the same as the --oom-kill-disable parameter of the `docker run` command.|Not supported in Kubernetes|-|-|No need to migrate|

## volumes

|Swarm configuration example|Kubernetes configuration example|
|---------------------------|--------------------------------|
|![volumes](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1746858951/p83627.png)

|![volumes](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1746858951/p83657.png)

 For more information, see [auth-service-deployment.yaml](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter configuration examples.md). |

## cap\_add/cap\_drop

|Swarm configuration example|Kubernetes configuration example|
|---------------------------|--------------------------------|
|![cap_add](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1746858951/p83643.png)

|![cap_add/cap_drop](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1746858951/p83659.png)

 For more information, see [auth-service-deployment.yaml](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter configuration examples.md). |

## privileged: true

|Swarm configuration example|Kubernetes configuration example|
|---------------------------|--------------------------------|
|![privileged: true](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1746858951/p83646.png)

|![privileged: true](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1746858951/p83660.png)

 For more information, see [auth-service-deployment.yaml](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter configuration examples.md). |

## mem\_limit

|Swarm configuration example|Kubernetes configuration example|
|---------------------------|--------------------------------|
|![mem-limit](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2746858951/p83650.png)

|![mem_limit](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2746858951/p83663.png)

 For more information, see [config-deployment.yaml](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter configuration examples.md). |

## cpu\_shares

|Swarm configuration example|Kubernetes configuration example|
|---------------------------|--------------------------------|
|![cpu_shares](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2746858951/p83653.png)

|![cpu_shares](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2746858951/p83665.png)

 For more information, see [config-deployment.yaml](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter configuration examples.md). |

