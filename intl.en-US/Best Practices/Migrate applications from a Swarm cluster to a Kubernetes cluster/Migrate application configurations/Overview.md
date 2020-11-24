# Overview

This topic describes how to migrate application configurations from Swarm to Kubernetes after operations and maintenance \(O&M\) engineers have migrated cluster configurations. The following steps are required: set up the environment, pre-process and convert Swarm compose files, deploy Kubernetes resource files, manually migrate application configurations that cannot be automatically converted, and debug the application to fix potential issues.

## Comparison between reserved instances, pay-as-you-go instances, and subscription instances

Swarm and Kubernetes both use various concepts in terms of applications, services, and access methods. For more information about the differences between these concepts, see [Concepts](/intl.en-US/Best Practices/Comparison between Swarm and Kubernetes cluster functions/Basic terms.md).

## Prerequisites

An application is required to be migrated from Swarm to Kubernetes. A Kubernetes cluster with the same cluster configurations is created for the application. In this topic, an application named swarm-piggymetrics is used as an example and its configurations are migrated to the Kubernetes cluster named k8s-piggymetrics-cluster.

Based on [PiggyMetrics](https://github.com/sqshq/PiggyMetrics?spm=a2c4e.11153940.blogcont610430.14.40667737Uedu2h), swarm-piggymetrics is an application built on a microservice architecture. PiggyMetrics is a Spring Cloud project on GitHub.

## Procedure

1.  [Set up the environment](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Migrate application configurations/Set up the environment.md).
2.  [Pre-process Swarm compose files](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Migrate application configurations/Pre-process Swarm compose files.md).
3.  [Convert Swarm compose files](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Migrate application configurations/Convert Swarm compose files.md).
4.  [Deploy Kubernetes resource files](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Migrate application configurations/Deploy Kubernetes resource files.md).
5.  [Manually migrate application configurations](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Migrate application configurations/Manually migrate application configurations.md).
6.  [Debug application startup](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Migrate application configurations/Debug application startup.md).
7.  [Migrate log configurations of applications](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Migrate application configurations/Migrate log configurations of applications.md).

## Related topics

For more information about migration-related questions, see [Troubleshooting](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Migrate application configurations/Troubleshooting.md).

For more information about the mapping of labels, see [Application configuration parameters](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter comparisons/Application configuration parameters.md), [Application release labels](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter comparisons/Application release labels.md), [Network configuration labels](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter comparisons/Network configuration labels.md), and [Log configuration parameters](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter comparisons/Log configuration parameters.md).

