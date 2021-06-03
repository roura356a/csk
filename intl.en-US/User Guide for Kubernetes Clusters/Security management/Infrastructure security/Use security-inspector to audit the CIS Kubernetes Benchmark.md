---
keyword: [auditing of the CIS Kubernetes Benchmark, security-inspector, Container Service for Kubernetes \(ACK\)]
---

# Use security-inspector to audit the CIS Kubernetes Benchmark

The Center for Internet Security \(CIS\) publishes the CIS Kubernetes Benchmark as a set of security recommendations for configuring Kubernetes in a secure manner. This topic describes how to use the security-inspector component to audit the CIS benchmark by using a command-line interface \(CLI\).

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   The security-inspector component is installed in the cluster. For more information, see [Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Upgrade cluster/Manage system components.md).

## Overview of CIS Benchmarks

The Center for Internet Security develops CIS benchmarks, which are sets of best practices for the secure configuration of common systems. CIS Benchmarks are developed through a consensus-based process comprised of cybersecurity professionals and experts, and are widely accepted by governments, businesses, industries, and academia.

The CIS Kubernetes Benchmark is written for the open source Kubernetes distribution and intended to be as universally applicable across distributions as possible. The Benchmark versions are tied to specific Kubernetes versions. For more information, see [CIS Kubernetes Benchmark](https://www.cisecurity.org/benchmark/kubernetes/).

CIS also releases CIS Kubernetes benchmarks that are specifically designed for Kubernetes distributions of different cloud service providers. For example, the CIS Benchmark for Alibaba Cloud Container Service for Kubernetes \(ACK\).

## Use security-inspector to audit the CIS Kubernetes Benchmark

ACK allows you to use security-inspector to scan an ACK cluster based on the CIS Kubernetes Benchmark and obtain the scan report in CSV format. To do this, perform the following steps:

1.  Run the following commands to create a scan task.

    To audit the CIS Kubernetes Benchmark, the component selects an appropriate benchmark version based on the Kubernetes version of the cluster.

    ```
    kubectl apply -f - <<EOF
    ---
    apiVersion: securityinspector.alibabacloud.com/v1alpha1
    kind: BenchmarkTask
    metadata:
      name: cis-kubernetes-benchmark
    spec:
      benchmarkVersion: 'cis-kubernetes-auto'
    ---
    apiVersion: securityinspector.alibabacloud.com/v1alpha1
    kind: BenchmarkJob
    metadata:
      name: cis-kubernetes-benchmark
    spec:
      taskName: cis-kubernetes-benchmark
    EOF
    ```

    You can set the benchmarkVersion parameter to one of the following values. Select the appropriate value based on your requirements. We recommend that you select cis-kubernetes-auto.

    |benchmarkVersion|Description|Applicable cluster|
    |----------------|-----------|------------------|
    |cis-kubernetes-auto|The component scans the cluster based on an appropriate CIS Kubernetes Benchmark that is automatically selected based on the Kubernetes version of the cluster.|Clusters of Kubernetes 1.15 and later|
    |cis-kubernetes-ack-1.0|The component scans the cluster based on the CIS Benchmark for Alibaba Cloud Container Service for Kubernetes \(ACK\) 1.0.|Dedicated and managed ACK clusters|
    |cis-kubernetes-1.6|The component scans the cluster based on the CIS Benchmarks for Kubernetes 1.6.|Clusters of Kubernetes 1.16 and later|
    |cis-kubernetes-1.5|The component scans the cluster based on the CIS Benchmarks for Kubernetes 1.5.|Kubernetes 1.15|

2.  Wait for 5 minutes. Then, run the following command to check whether the scan task is completed.

    ```
    kubectl get benchmarkjobs.securityinspector.alibabacloud.com cis-kubernetes-benchmark -o 'jsonpath={.status.phase}'; echo
    ```

    If the output shows `Succeeded`, it indicates that the scan task is completed.

3.  After the scan task is completed, run the following commands to obtain the scan report in CSV format.

    ```
    for name in $(kubectl get benchmarkcsvresults.securityinspector.alibabacloud.com -l securityinspector.task.name=cis-kubernetes-benchmark -o name)
    do
      filename="cis-$(echo $name | awk -F '/' '{print $2}')"; \
      kubectl get $name -o jsonpath='{.result.data}' > "$filename".csv; \
      echo "saved $filename.csv"
    done
    ```

    When you read the scan report, you can determine whether remediation measures are required based on your business scenarios. For more information about the scan report, see [Report interpretation](#section_bi2_4re_m60).


## Report interpretation

The following table describes the columns in the scan report.

|Column name|Description|Whether measures are required|
|-----------|-----------|-----------------------------|
|**Date**|The time of the scan.|No|
|**Result Schema**|The CIS benchmark based on which the scan is performed. Valid values:-   cis-kubernetes-ack-1.0: the CIS Benchmark for Alibaba Cloud Container Service for Kubernetes \(ACK\) 1.0.
-   cis-kubernetes-1.6: the CIS Benchmarks for Kubernetes 1.6.
-   cis-kubernetes-1.5: the CIS Benchmarks for Kubernetes 1.5.

For more information about the benchmarks, see [CIS Kubernetes Benchmarks](https://www.cisecurity.org/benchmark/kubernetes/).

|No|
|**Node Name**|The cluster node for which the report is generated.|No|
|**Total Fail**|The number of scored items that do not comply with benchmark recommendations.|For more information, see the description of **Result**.|
|**Total Warn**|The number of items that are not scored but require your attention.|For more information, see the description of **Result**.|
|**Total Pass**|The number of items that comply with benchmark recommendations.|No|
|**Section Id**|The section ID defined in the CIS benchmark.|No|
|**Section Description**|The section description defined in the CIS benchmark.|No|
|**Test Id**|The test ID defined in the CIS benchmark.|No|
|**Test Description**|The test description defined in the CIS benchmark.|No|
|**Scored**|Whether the item is scored. Valid values:-   Scored: Failure to comply with the recommendations decreases the final benchmark score.
-   Not scored: Failure to comply with the recommendations does not decrease the final benchmark score.

|No|
|**Test Remediation**|The recommended remediation measure if the item does not comply with the benchmark recommendation. For more information, see [CIS Kubernetes Benchmarks](https://www.cisecurity.org/benchmark/kubernetes/).

|For more information, see the description of **Result**.|
|**Result**|The check result. Valid values:-   fail: The scored item does not comply with the benchmark recommendation.
-   warn: The item is not scored but requires your attention.
-   pass: The item complies with the benchmark recommendation.

|You can take the following measures based on the check result:-   fail: We recommend that you take the remediation measures displayed in the **Test Remediation** column. You can also determine whether to adjust or fix the configurations based on actual business scenarios.
-   warn: You need to pay attention to the item and adjust the configuration based on actual business scenarios.
-   pass: No measure is required. |

**Related topics**  


[CIS Kubernetes Benchmark](https://www.cisecurity.org/benchmark/kubernetes/)

[security-inspector](/intl.en-US/Release notes/System Component change Records/Security/security-inspector.md)

