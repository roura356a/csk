---
keyword: [logtail-windows, component that collects the container log]
---

# logtail-windows

This topic introduces logtail-windows and describes the usage notes and release notes for logtail-windows.

## Introduction

Container Service for Kubernetes \(ACK\) allows you to use the logtail-windows component to collect log data from Windows containers and send the data to Log Service.

## Usage notes

logtail-windows must be manually installed. For more information, see [Install Logtail V1.0.x on Windows nodes](/intl.en-US/User Guide for Kubernetes Clusters/Windows container/Use Logtail to collect application logs from Windows nodes.md).

logtail-windows 1.0.x is compatible with all Windows nodes. logtail-windows 1.0.20 and later are incompatible with Windows Server version 1903. To install logtail-windows 1.0.20 or later on a worker node, you must use Windows Server version 1909 or 2004 for the node, and configure the Semi-Annual Channel \(SAC\).

logtail-windows 1.1.x is dependent on the privileged proxy processes on Windows nodes. The image size of logtail-windows 1.1.x is 90% smaller than that of logtail-windows 1.0.x. The startup of the logtail-windows 1.1.x image is faster than that of the logtail-windows 1.0.x image. logtail-windows 1.1.x can be installed only on Windows nodes that are added from node pools and are added with the `windows.alibabacloud.com/deployment-topology=2.0` label when these nodes are added to the cluster. If the `windows.alibabacloud.com/deployment-topology=2.0` label is added to a Windows node after the node is added to the cluster, logtail-windows 1.1.x cannot be installed on the node.

## Release notes

**August 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.20|registry-vpc.$\{region\}.aliyuncs.com/acs/logtail-windows:v1.0.20|2021-08-21|-   The base images are updated. The original version number is overwritten.
-   The following Windows operating systems are supported: Windows Server version 1809 \(10.0.17763.2114\), Windows Server version 1909 \(10.0.18363.1556\), and Windows Server version 2004 \(10.0.19041.1165\).

|No impact on workloads.|
|v1.1.18-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/logtail-windows:v1.1.18-aliyun.1|2021-08-20|-   The base images are updated. The original version number is overwritten.
-   The following Windows operating systems are supported: Windows Server version 1809 \(10.0.17763.2114\), Windows Server version 1909 \(10.0.18363.1556\), and Windows Server version 2004 \(10.0.19041.1165\).

|No impact on workloads.|

**July 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.1.18-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/logtail-windows:v1.1.18-aliyun.1|2021-07-05|-   The base images are updated. The original version number is overwritten.
-   The size of the logtail-windows image is reduced from 2.7 GB to 220 MB.
-   The following Windows operating systems are supported: Windows Server version 1809 \(10.0.17763.1999\), Windows Server version 1909 \(10.0.18363.1556\), and Windows Server version 2004 \(10.0.19041.1052\).

|No impact on workloads.|

**June 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.1.18-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/logtail-windows:v1.1.18-aliyun.1|2021-06-17|-   Rancher Wins is used as the proxy of privileged operations.
-   The size of the logtail-windows image is reduced from 2.7 GB to 220 MB.
-   The following Windows systems are supported: Windows Server version 1809 \(10.0.17763.1935\), Windows Server version 1909 \(10.0.18363.1556\), and Windows Server version 2004 \(10.0.19041.985\).

|The upgrade is applicable only to Windows worker nodes that are installed with Rancher Wins.|

**April 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.20|registry-vpc.$\{region\}.aliyuncs.com/acs/logtail-windows:v1.0.20|2021-04-09|Windows Server version 1809 \(10.0.17763.1817\), Windows Server version 1909 \(10.0.18363.1440\), and Windows Server version 2004 \(10.0.19041.867\) are supported. Windows Server version 1903 is no longer supported.

|-   The upgrade is not applicable to worker nodes that run Windows Server version 1903.
-   No impact on worker nodes that run Windows Server version 1809, Windows Server version 1909, or Windows Server version 2004.
-   Windows Server version 1903 is no longer supported in later logtail-windows versions. |

**January 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.19|registry-vpc.$\{region\}.aliyuncs.com/acs/logtail-windows:v1.0.19|2021-01-27|-   Logstores can be created over the Internet.
-   Windows Server version 1809 \(10.0.17763.1697\), Windows Server version 1903 \(10.0.18362.1256\), Windows Server version 1909 \(10.0.18363.1316\), and Windows Server version 2004 \(10.0.19041.746\) are supported.

|No impact on workloads.|
|v1.0.18|registry-vpc.$\{region\}.aliyuncs.com/acs/logtail-windows:v1.0.18|2021-01-25|-   The availability issue in the China South 1 Finance region is fixed.
-   Logtail is upgraded to V1.0.0.18.
-   Windows Server version 1809 \(10.0.17763.1697\), Windows Server version 1903 \(10.0.18362.1256\), Windows Server version 1909 \(10.0.18363.1316\), and Windows Server version 2004 \(10.0.19041.746\) are supported.

|No impact on workloads.|
|v1.0.10|registry-vpc.$\{region\}.aliyuncs.com/acs/logtail-windows:v1.0.10|2021-01-10|-   Logtail is upgraded to V1.0.0.10.
-   Windows Server version 1809 \(10.0.17763.1637\), Windows Server version 1903 \(10.0.18362.1256\), Windows Server version 1909 \(10.0.18363.1256\), and Windows Server version 2004 \(10.0.19041.685\) are supported.

|No impact on workloads.|

