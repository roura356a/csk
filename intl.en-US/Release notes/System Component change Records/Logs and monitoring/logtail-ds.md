---
keyword: [logtail-ds introduction, logtail-ds usage notes, logtail-ds release notes]
---

# logtail-ds

The logtail-ds component is an agent provided by Log Service to collect Kubernetes logs. This topic introduces logtail-ds and describes the usage notes and release notes of the component.

## Introduction

The logtail-ds component enables high-performance log collection with low resource overhead. By default, the component is installed in the kube-system namespace. The system automatically performs the following operations when you install logtail-ds:

1.  Creates a CustomResourceDefinition \(CRD\) named aliyunlogconfigs that is used to register CRDs with the Kubernetes system.
2.  Creates a Deployment named alibaba-log-controller that is used to manage CRDs.
3.  Installs Logtail as a DaemonSet that is used to collect log data.

You can use logtail-ds to dynamically filter the containers whose logs you want to collect. logtail-ds can collect multiple types of log data, such as standard output, files, and syslog. The component also supports multiple log parsing methods and configuration methods. For more information about the log collection features, see [Logtail overview](/intl.en-US/Data Collection/Logtail collection/Overview/Logtail overview.md).

## Usage notes

For more information about the usage notes of logtail-ds, see [Collect log files from containers by using Log Service](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Collect log files from containers by using Log Service.md).

## Release notes

For more information about the release notes of logtail-ds, see [logtail-ds release notes](/intl.en-US/Data Collection/Logtail collection/Release notes.md).

