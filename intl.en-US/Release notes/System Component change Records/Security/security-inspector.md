---
keyword: [security-inspector, introduction, security inspection]
---

# security-inspector

security-inspector is a key component for performing security inspections. This topic describes the features and usage notes of security-inspector. This topic also lists the latest changes to security-inspector.

## Introduction

You can use security-inspector to perform multi-dimensional scans on workload configurations. This helps you better understand the security risks of your workloads. The following figure shows the architecture of security-inspector.

![security-inspector](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6258298951/p129654.png)

## Usage notes

security-inspector provides the following inspection features.

-   security-inspector uses Polaris to perform security inspections. This allows you to detect security risks of workload configurations in a Kubernetes cluster in real time.

    **Note:** Polaris is an open source project that is used to identify security risks of workload configurations in a Kubernetes cluster. For more information, see [Polaris](https://github.com/FairwindsOps/polaris).

-   You can use security-inspector to perform multi-dimensional scans on workloads. After a scan is completed, a report is provided by security-inspector to show the results of inspection items, including health checks, images, networks, resources, and securities. This provides information for you to better understand the security status of your applications. The report also offers suggestions on fixes that allow you to perform security reinforcement. For more information, see [Use the inspection feature to detect security risks in the workloads of an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Security/Use the inspection feature to detect security risks in the workloads of an ACK cluster.md).

## Release notes

**June 2021**

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v0.5.0.2-g5e33765-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/security-inspector:v0.5.0.2-g5e33765-aliyun|2021-06-24|The issue that inspection reports are not displayed as normal when one Log Service project is shared among multiple clusters is fixed.|

**March 2021**

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v0.4.0.0-g541eb31-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/security-inspector:v0.4.0.0-g541eb31-aliyun|2021-03-15|-   The Center for Internet Security \(CIS\) Kubernetes benchmark is supported. For more information, see [Use security-inspector to audit the CIS Kubernetes Benchmark](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Infrastructure security/Use security-inspector to audit the CIS Kubernetes Benchmark.md).
-   The following Kubernetes events are supported. You can find the events in Event Center when a scan is triggered.
    -   SecurityInspectorConfigAuditStart: Configuration auditing is started.
    -   SecurityInspectorConfigAuditFinished: Configuration auditing is completed.
    -   SecurityInspectorConfigAuditHighRiskFound: Risky configurations are found after configuration auditing is completed.
    -   SecurityInspectorBenchmarkStart: The benchmark check is started.
    -   SecurityInspectorBenchmarkFinished: The benchmark check is completed.
    -   SecurityInspectorBenchmarkFailedCheckFound: Failed inspection items are found after the benchmark check is completed. |

**January 2021**

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v0.3.0.2-gcb49252-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/security-inspector:v0.3.0.2-gcb49252-aliyun|2021-01-05|Permissions of anonymous users can be scanned to detect risky role-based access control \(RBAC\) permissions that are granted to the users.|

**December 2020**

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v0.2.0.22-gd1fbaff-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/security-inspector:v0.2.0.22-gd1fbaff-aliyun|2020-12-16|-   CustomResourceDefinitions \(CRDs\) are supported to store the latest inspection results.
-   Specified inspection items can be enabled or disabled based on your needs.
-   The workload whitelist feature is supported. |

**July 2020**

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v0.1.0.3-g69f71f6-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/security-inspector:v0.1.0.3-g69f71f6-aliyun|2020-07-06|Inspection tasks can be manually triggered to inspect the workloads in clusters and generate inspection reports.|

