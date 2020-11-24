# Application release labels

This topic describes the labels that are related to application releases in clusters of Container Service for Swarm.

|Swarm label|Description|Related Kubernetes parameter|Sample Swarm configuration|Sample Kubernetes configuration|How to migrate|
|-----------|-----------|----------------------------|--------------------------|-------------------------------|--------------|
|restart|The policy for restarting the container.|restartPolicy|-|-|Automatically converted by Kompose.|
|aliyun.latest\_image|Specifies whether to pull the latest image.|imagePullPolicy|aliyun.latest\_image: true|For more information, see the sample Kubernetes configuration in [aliyun.latest\_image](#section_6co_c0g_6gs).|Migrate manually:

 After you use Kompose to convert the Swarm compose file, add the imagePullPolicy field to the Kubernetes resource file \*-deployment.yaml of the application. The default value is Always. |
|depends\_on|The dependencies of services.|Uses methods such as init container, liveness probe, and readiness probe to implement features such as health check and dependency check. For more information, see [Handle service dependencies](https://yq.aliyun.com/articles/573791).|-|-|Depends on workload implementation.|
|aliyun.depends|The dependencies of services.|Uses methods such as init container, liveness probe, and readiness probe to implement features such as health check and dependency check. For more information, see [Handle service dependencies](https://yq.aliyun.com/articles/573791).|-|-|Depends on workload implementation.|
|environment: affinity:service! =db|The constraints of service deployments. For more information, see [Service deployment constraints \(affinity:service\)](/intl.en-US/User Guide/Service orchestrations/Service deployment constraints (affinity:service).md).|Uses labels such as nodeAffinity, podAffinity, and podAntiAffinity to configure affinity attributes of nodes and pods. For more information, see [Affinity and anti-affinity](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity).|-|-|Depends on workload implementation.|
|environment: constraint:group==1|Specifies that pods are scheduled to nodes with the group:1 label.|Uses nodeSelector to filter nodes. For more information, see [nodeSelector](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#nodeselector).|For more information, see the sample Swarm configuration in [environment: constraint:group==1](#section_u4x_kj4_9m8).|For more information, see the sample Kubernetes configuration in [environment: constraint:group==1](#section_u4x_kj4_9m8).|Migrate manually:

 After you use Kompose to convert the Swarm Compose file, add the nodeSelector field to the Kubernetes resource file \*-deployment.yaml of the application. |
|aliyun.global|Specifies whether the service is for global use.|You can create DaemonSet type applications for global use. For more information, see [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/).|For more information, see the sample Swarm configuration in [aliyun.global](#section_vds_ea9_bgo).|For more information, see the sample Kubernetes configuration in [aliyun.global](#section_vds_ea9_bgo).|Automatic conversion by using Kompose and manual modification are both required.

 Modify the configurations:

 After you use Kompose to convert the Swarm compose file, you must delete the status field in the generated Kubernetes resource file. The status field records intermediate statuses. |
|aliyun.scale|The number of containers for the service. You can use this field to scale out the service.|replicas|aliyun.scale: '10'|replicas: 10|Automatically converted by Kompose.|
|aliyun.rolling\_updates|Specifies whether to enable rolling update for the service.|strategy.type.RollingUpdate. For more information, see [Strategy](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy).|For more information, see the sample Swarm configuration in [aliyun.rolling\_updates and aliyun.rolling\_updates.parallelism](#section_fpm_gto_21u).|For more information, see the sample Kubernetes configuration in [aliyun.rolling\_updates and aliyun.rolling\_updates.parallelism](#section_fpm_gto_21u).|Migrate manually:

 After you use Kompose to convert the Swarm compose file, you must manually configure the rolling update policy. |
|aliyun.rolling\_updates.parallelism|The number of containers that are concurrently updated.|maxUnavailable. For more information, see [Strategy](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy).|Migrate manually:

 After you use Kompose to convert the Swarm compose file, you must manually configure the rolling update policy. |
|aliyun.auto\_scaling. \*|The policy for automatically adjusting the number of containers for the service based on the container resource usage. For more information, see [Container auto scaling](/intl.en-US/User Guide/Monitoring/Container auto scaling.md).|This label does not have a mapping field in Kubernetes. To enable auto scaling of pods, you can create an application and enable Horizontal Pod Autoscaling \(HPA\) in the Container Service for Kubernetes \(ACK\) console. For more information, see [t16700.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/HPA.md).|For more information, see the sample Swarm configuration in [aliyun.auto\_scaling. \*](#section_sqa_2ct_b27).|For more information, see the sample Kubernetes configuration in [aliyun.auto\_scaling. \*](#section_sqa_2ct_b27).|Migrate manually:

 After you deploy the Kubernetes resource file, configure HPA in the ACK console. |
|aliyun.probe.cmd|The shell command for health check. For more information, see [probe](/intl.en-US/User Guide/Service orchestrations/probe.md).|livenessProbe|For more information, see the sample Swarm configuration in [aliyun.probe.cmd, aliyun.probe.initial\_delay\_seconds, and aliyun.probe.timeout\_seconds](#section_b5b_w9f_s8l).|For more information, see the sample Kubernetes configuration in [aliyun.probe.cmd, aliyun.probe.initial\_delay\_seconds, and aliyun.probe.timeout\_seconds](#section_b5b_w9f_s8l).|Automatically converted by Kompose.|
|aliyun.probe.initial\_delay\_seconds|The delay duration for health check after the container starts. Unit: seconds. For more information, see [probe](/intl.en-US/User Guide/Service orchestrations/probe.md).|livenessProbe **Note:**

-   In Swarm, health check is performed to check the container status. However, the container can provide services to external systems regardless of the status.

-   In Kubernetes, if a health check performed by liveness probe fails in a pod, Kubernetes forcibly restarts the pod. Therefore, you must set an appropriate delay period for the liveness probe. Otherwise, Kubernetes repeatedly restarts the pod and the application fails to start.


|Automatically converted by Kompose.|
|aliyun.probe.timeout\_seconds|The timeout period of a health check. For more information, see [probe](/intl.en-US/User Guide/Service orchestrations/probe.md).|livenessProbe|Automatically converted by Kompose.|
|aliyun.probe.url|The URL to which the HTTP or TCP request for health check is sent. For more information, see [probe](/intl.en-US/User Guide/Service orchestrations/probe.md).|livenessProbe|For more information, see the sample Swarm configuration in [aliyun.probe.url](#section_j7b_6ul_u92).|For more information, see the sample Kubernetes configuration in [aliyun.probe.url](#section_j7b_6ul_u92).|Automatically converted by Kompose.|
|extra\_hosts|The entries to be added to the hosts file of the container.|The hostAliases parameter in Kubernetes. For more information, see [Adding Additional Entries with HostAliases](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/#adding-additional-entries-with-hostaliases).|For more information, see the sample Swarm configuration in [extra\_hosts](#section_w8s_dif_a4z).|For more information, see the sample Kubernetes configuration in [extra\_hosts](#section_w8s_dif_a4z).|Migrate manually:

 After you use Kompose to convert the Swarm compose file, add the hostAliases parameter to the Kubernetes resource file \*-deployment.yaml of the application. |
|entrypoint|The entry point to overwrite the default one.|command|For more information, see the sample Swarm configuration in [entrypoint](#section_bhl_v3z_cck).|For more information, see the sample Kubernetes configuration in [entrypoint](#section_bhl_v3z_cck).|Automatically converted by Kompose.|
|command|The command to overwrite the default one.|args|For more information, see the sample Swarm configuration in [command](#section_5ir_jbz_6md).|For more information, see the sample Kubernetes configuration in [command](#section_5ir_jbz_6md).|Automatically converted by Kompose.|

## aliyun.latest\_image

|Sample Swarm configuration|Sample Kubernetes configuration|
|--------------------------|-------------------------------|
|aliyun.latest\_image: true|![aliyun.latest_image](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6746858951/p83728.png)

 For more information, see [config-deployment.yaml](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter configuration examples.md). |

## environment: constraint:group==1

|Sample Swarm configuration|Sample Kubernetes configuration|
|--------------------------|-------------------------------|
|![environment: constraint:group==1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6746858951/p83736.png)

|![environment: constraint:group==1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6746858951/p83737.png)

 For more information, see [auth-mongodb-deployment.yaml](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter configuration examples.md). |

## aliyun.global

|Sample Swarm configuration|Sample Kubernetes configuration|
|--------------------------|-------------------------------|
|![aliyun.global](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6746858951/p83740.png)

|![aliyun.global](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6746858951/p83742.png)

 For more information, see [logtail2-daemonset.yaml](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter configuration examples.md). |

## aliyun.rolling\_updates and aliyun.rolling\_updates.parallelism

|Sample Swarm configuration|Sample Kubernetes configuration|
|--------------------------|-------------------------------|
|![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7746858951/p83749.png)

|![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7746858951/p83750.png)

 For more information, see [config-deployment.yaml](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter configuration examples.md). |

## aliyun.auto\_scaling. \*

|Sample Swarm configuration|Sample Kubernetes configuration|
|--------------------------|-------------------------------|
|![aliyun.auto_scaling. *](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7746858951/p83755.png)

|![aliyun.auto_scaling. *](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5442026061/p83756.png) |

## aliyun.probe.cmd, aliyun.probe.initial\_delay\_seconds, and aliyun.probe.timeout\_seconds

|Sample Swarm configuration|Sample Kubernetes configuration|
|--------------------------|-------------------------------|
|![aliyun.probe](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7746858951/p83757.png)

aliyun.latest\_image: true|![aliyun.probe.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7746858951/p83758.png)

 For more information, see [config-deployment.yaml](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter configuration examples.md). |

## aliyun.probe.url

|Sample Swarm configuration|Sample Kubernetes configuration|
|--------------------------|-------------------------------|
|![aliyun.probe.url](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7746858951/p83764.png)

|![probe](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7746858951/p83765.png)

 For more information, see [gateway-deployment.yaml](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter configuration examples.md). |

## extra\_hosts

|Sample Swarm configuration|Sample Kubernetes configuration|
|--------------------------|-------------------------------|
|![extra_hosts](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8746858951/p83766.png)

|![extra_hosts](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8746858951/p83767.png)

 For more information, see [logtail2-daemonset.yaml](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter configuration examples.md). |

## entrypoint

|Sample Swarm configuration|Sample Kubernetes configuration|
|--------------------------|-------------------------------|
|![entrypoint](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8746858951/p83768.png)

|![entrypoint](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8746858951/p83769.png)

 For more information, see [auth-service-deployment.yaml](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter configuration examples.md). |

## command

|Sample Swarm configuration|Sample Kubernetes configuration|
|--------------------------|-------------------------------|
|![command](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8746858951/p83770.png)

|![command](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8746858951/p83771.png)

 For more information, see [auth-service-deployment.yaml](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter configuration examples.md). |

