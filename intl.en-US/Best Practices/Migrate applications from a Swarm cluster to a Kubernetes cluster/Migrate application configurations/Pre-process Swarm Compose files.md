# Pre-process Swarm Compose files

This topic describes how to pre-process Swarm Compose files.

1.  Download a Swarm Compose file.

    1.  Log on to the [Container Service for Swarm console](https://cs.console.aliyun.com). In the left-side navigation pane, click **Applications**. On the Applications page, find the application that you want to manage and click **Change Configuration** in the Actions column.

    2.  Copy the configurations and save them in a YAML file on an on-premises machine. In this topic, the file is named swarm-piggymetrics.yaml, which is the Swarm Compose file to be pre-processed.

2.  Replace environment variables in the Swarm compose file with actual values.

    The Container Service for Swarm console allows you to use a configuration file to replace variables in a Swarm Compose file with actual values. Kubernetes does not support this feature. Before you use Kompose to convert a Swarm Compose file, you must manually replace the dollar signs \(*$*\) in the Compose file with actual values. To replace variables in the Swarm Compose file, perform the following steps:

    1.  Log on to the [Container Service for Swarm console](https://cs.console.aliyun.com). In the left-side navigation pane, click **Configurations**. On the page that appears, select the region where the configuration file is deployed and find the configuration file.
    2.  Replace the dollar signs \(*$*\) in the swarm-piggymetrics.yaml file with the actual values based on this configuration file.
3.  Pre-process labels.

    You must pre-process the labels in the Swarm Compose file. Otherwise, Kompose may report an error and stop the conversion as a result of invalid formats or unsupported labels. The following section describes two methods of label processing. For more information about each label, see [Application configuration labels](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter comparisons/Application configuration labels.md), [Application release labels](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter comparisons/Application release labels.md), [Network configuration labels](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter comparisons/Network configuration labels.md), and [Log configuration parameters](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter comparisons/Log configuration parameters.md).

    -   Change the values of the following labels from BOOLEAN to STRING. For example, change true or false to `"true"` or `"false"`.
        -   aliyun.global
        -   aliyun.latest\_image
    -   Kubernetes Services with labels that contain the following values cannot be migrated. You must delete them and create new ones with the same configurations in the related Kubernetes cluster.
        -   external
4.  Modify the version number of the Swarm Compose file.

    Kompose supports Swarm Compose files of version 2. You must change the version declaration from version: '2.X' to version: '2' in the Swarm Compose file. Otherwise, an error occurs during conversion.


