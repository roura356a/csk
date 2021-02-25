# Migrate logging settings of applications

This topic describes how to migrate logging settings of applications.

If your applications use Log Service, make sure that the application logs are delivered from the cluster of Container Service for Kubernetes \(ACK\) to Log Service as expected during the migration. You must also check whether services that are reliant on Log Service run as expected, such as Cloud Monitor.

1.  Log on to the [Log Service console](https://sls.console.aliyun.com/?spm). In the **Projects** section, find and click the **Log Service project** to which the application logs are delivered.

2.  On the **Logstores** tab, you can view the Logstores that are generated for the Swarm and ACK clusters.


**Note:** The ACK cluster is automatically set to share the Log Service project of the Swarm cluster. In addition, Kompose is used to automatically migrate the logging settings. However, the Logstores for the Swarm cluster and the related ACK cluster are generated based on different rules. Therefore, application events of the related ACK cluster are logged in separate Logstores under the same Log Service project.

-   The names of Logstores for the Swarm cluster are automatically generated in the format of acslog-$\{app\}-$\{name\}.
-   The names of Logstores for the ACK cluster are automatically generated in the format of $\{name\}. Each Logstore for the ACK cluster corresponds to a Logstore for the Swarm cluster.
-   The names of Logstores for ACK clusters cannot contain uppercase letters. Therefore, Kompose automatically converts uppercase letters to lowercase letters during the migration.

For other Alibaba Cloud services that are reliant on Log Service and collect application events to the Log Service project of the Swarm cluster, you must migrate their logging settings or set them to use the Logstore of the ACK cluster. You must migrate the logging settings or change the Logstore if the Alibaba Cloud services are used in the following scenarios. For more information about how to convert and modify logging settings, see [Log Service - Real-time consumption](/intl.en-US/Log consumption and shipping/Real-time subscription and consumption/Overview.md).

-   Real-time consumption: Services consume logs in real time by using the Log Service SDK, Storm Spout, Spark Streaming client, web console, and services such as Cloud Monitor and Application Real-Time Monitoring Service \(ARMS\).
-   Real-time query: You can query and analyze logs in real time.
-   Delivery and storage: You can deliver logs to various storage systems for processing.
-   Logs of services integrated with Alibaba Cloud Security services: Log Service can be integrated with Alibaba Cloud Security services and uses an independent software vendor \(ISV\) to consume logs of cloud services.

