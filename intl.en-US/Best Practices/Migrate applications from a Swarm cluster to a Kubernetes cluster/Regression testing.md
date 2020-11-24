# Regression testing

This topic describes how to use regression testing to check whether your applications run as expected in a cluster of Container Service for Kubernetes \(ACK\).

## Set a testing domain name

In an ACK cluster, you can configure Service ports to enable communication among Services in the cluster. You can configure Ingresses to enable external access to Services in an ACK cluster.

When you configure an Ingress, you can set multiple domain names such as production domain names and testing domain names. Production domain names are domain names that have obtained Internet Content Provider \(ICP\) numbers. You must enter the fully-qualified domain name. Testing domain names are provided by ACK. You can enter the prefix of the testing domain name. Perform the following steps to create and configure an Ingress:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Ingresses**.

5.  In the upper-right corner of the Ingresses page, click **Create**.

    For more information about how to create and configure an Ingress, see [Create an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Create an Ingress on the web UI.md).

6.  After the Ingress is created, you can find its domain name and endpoint on the Ingresses page.

    **Note:** The endpoint of the ingress is the public IP address of the Server Load Balancer \(SLB\) instance that is attached to the ACK cluster.


## Test whether your applications run as expected

You can use the testing domain name or production domain name to access applications deployed in the related ACK cluster. If you have not updated DNS settings to point the production domain name to the public IP address of the attached SLB instance, you must bind the public IP address to the production domain name on your on-premises machine. The following examples show the domain names that can be used:

![Testing domain name](../images/p48275.png "Testing domain name")

![Production domain name](../images/p48276.png "Production domain name")

**Note:** Perform regression testing based on the testing domain name or production domain name to check whether your applications run as expected in the newly created ACK cluster.

## Check application events

The Logstores for a Swarm cluster and the related ACK cluster are generated based on different rules. For more information, see [Migrate log configurations of applications](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Migrate application configurations/Migrate log configurations of applications.md). Therefore, application events of a Swarm cluster and the related ACK cluster are logged in different Logstores under the same Log Service project. During the test process, you can check whether application events are logged to the Logstore created for the ACK cluster and whether the events indicate that applications run as expected.

1.  Log on to the [Log Service console](https://sls.console.aliyun.com/?spm). In the **Projects** section, click the Log Service project that is used.

2.  On the details page of the Log Service project, click the **Logstores** tab. In the Logstore list, you can find the Logstores for the Swarm and ACK clusters.

3.  Select the Logstore for the ACK cluster and click **Search** to view the application events. This allows you to check whether the applications run as expected.


## Check the monitoring metrics of the applications

If application events can be logged to the Logstore for the ACK cluster, you need to check whether the related monitoring services, such as Cloud Monitor, Application Real-Time Monitoring Service \(ARMS\), message delivery, and storage services are correctly configured based on the Logstore. For more information, see [Log Service - Real-time consumption](/intl.en-US/Log consumption and shipping/Real-time subscription and consumption/Overview.md). You must check whether each log consumption scenario is correctly configured based on your actual requirements.

In this example, an application named swarm-piggymetrics is created to demonstrate how to use host monitoring and log monitoring of Cloud Monitor to monitor applications.

1.  View host monitoring metrics. For more information, see [t6293.md\#](/intl.en-US/FAQ/Operation/How do I view the monitoring data of a specified time period in the Cloud Monitor
         console?.md).

    When you create an ACK cluster, you can install the **Cloud Monitor agent**. The Cloud Monitor agent can be used to monitor the system load and network connections on each node in the ACK cluster. It also allows you to check whether the test traffic is received by the newly create ACK cluster.

2.  View log monitoring metrics.

    You can use log monitoring or ARMS to conduct fine-grained application monitoring. In this example, a log monitoring task named **findAccountCount\_k8s** is created based on the existing monitoring rules of the Swarm cluster to monitor the events of nodes in the ACK cluster. Parsing rules are also created to parse the logged events. You can check whether the test traffic is received by the ACK cluster through charts.

    **Note:** The preceding figure shows the steady increase of account queries over a short time period. This proves that the newly created ACK cluster k8s-piggymetrics-cluster handles test traffic and provides external services.


