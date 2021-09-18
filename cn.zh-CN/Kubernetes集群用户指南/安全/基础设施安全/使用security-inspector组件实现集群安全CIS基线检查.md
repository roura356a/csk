---
keyword: [CIS Kubernetes基线检查, security-inspector, ACK]
---

# 使用security-inspector组件实现集群安全CIS基线检查

CIS Kubernetes基线是CIS推出的一系列用于构建一个安全可靠的Kubernetes集群的安全配置建议。本文介绍如何在命令行下通过security-inspector组件提供的功能实现集群安全CIS基线检查。

-   已创建一个ACK集群。具体操作，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   已在集群中安装security-inspector组件。具体操作，请参见[管理组件](/cn.zh-CN/Kubernetes集群用户指南/组件/管理组件.md)。

## CIS基线概述

CIS是互联网安全中心（Center for Internet Security）的缩写，它针对一些常见的系统制定了一套安全配置指南，称之为基线。CIS基线是由一组互联网安全专家共同讨论和制定的，这些安全指南被政府、企业和学术机构广泛认可和执行。

CIS Kubernetes基线是针对开源Kubernetes发行版本所编写的，旨在尽可能广泛应用于各个发行版本，同时不同的基线版本与特定的Kubernetes版本相关联。更多信息，请参见[CIS Kubernetes 基线](https://www.cisecurity.org/benchmark/kubernetes/)。

CIS也推出了更符合各大云厂商实际场景的CIS Kubernetes基线，例如针对ACK场景的CIS基线，即CIS Alibaba Cloud Container Service For Kubernetes（ACK）Benchmark。

## 使用security-inspector组件实现集群安全CIS基线检查

您可以通过以下步骤按照CIS Kubernetes基线标准扫描集群，并获取CSV格式的扫描结果报告。

1.  使用以下命令创建一个扫描任务。

    执行CIS基线检查，程序会根据集群版本选择合适的基线版本进行检查。

    ```
    kubectl apply -f - <<EOF
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

    以上命令中benchmarkVersion字段的可用值如下，您可以根据需要选取合适的值（推荐使用cis-kubernetes-auto）。

    |benchmarkVersion字段的值|含义|适用集群|
    |--------------------|--|----|
    |cis-kubernetes-auto|由组件根据集群版本自动选择最合适的CIS Kubernetes基线标准进行扫描。|Kubernetes 1.15及以上版本|
    |cis-kubernetes-ack-1.0|按照CIS Alibaba Cloud Container Service For Kubernetes（ACK）Benchmark v1.0.0基线标准进行扫描。|ACK专有版集群和托管版集群（Kubernetes 1.18及以上版本）|
    |cis-kubernetes-1.20|按照CIS Kubernetes V1.20 Benchmark v1.0.0基线标准进行扫描。|Kubernetes 1.20及以上版本|
    |cis-kubernetes-1.6|按照CIS Kubernetes Benchmark v1.6.0基线标准进行扫描。|Kubernetes 1.16~1.19版本|
    |cis-kubernetes-1.5|按照CIS Kubernetes Benchmark v1.5.1基线标准进行扫描。|Kubernetes 1.15版本|

2.  等待5分钟左右，通过以下命令确认本次扫描任务是否完成。

    ```
    kubectl get benchmarkjobs.securityinspector.alibabacloud.com cis-kubernetes-benchmark -o 'jsonpath={.status.phase}'; echo
    ```

    命令执行后返回`Succeeded`即表示扫描完成。

3.  确认扫描完成后，可以通过以下命令获取本次扫描结果的CSV格式报告。

    ```
    for name in $(kubectl get benchmarkcsvresults.securityinspector.alibabacloud.com -l securityinspector.task.name=cis-kubernetes-benchmark -o name)
    do
      filename="cis-$(echo $name | awk -F '/' '{print $2}')"; \
      kubectl get $name -o jsonpath='{.result.data}' > "$filename".csv; \
      echo "saved $filename.csv"
    done
    ```

    获取到报告后，对于报告中的结果您可以根据实际业务场景来判断是否需要采取措施，报告中各列的含义可以参考下文[报告解读](#section_bi2_4re_m60)。


## 报告解读

上文扫描获取报告中的各列的说明如下表。

|列名|含义|是否需要采取措施|
|--|--|--------|
|**Date**|扫描时间|否|
|**Result Schema**|扫描时遵照的基线标准。取值如下：-   cis-kubernetes-ack-1.0：CIS Alibaba Cloud Container Service For Kubernetes \(ACK\) Benchmark v1.0.0基线标准。
-   cis-kubernetes-1.20：CIS Kubernetes V1.20 Benchmark v1.0.0基线标准。
-   cis-kubernetes-1.6：CIS Kubernetes Benchmark v1.6.0基线标准。
-   cis-kubernetes-1.5：CIS Kubernetes Benchmark v1.5.1基线标准。

关于各个基线标准的具体内容，请参见[CIS Kubernetes基线标准文档](https://www.cisecurity.org/benchmark/kubernetes/)。

|否|
|**Node Name**|当前报告是哪个集群节点的扫描报告。|否|
|**Total Fail**|计分检查项未通过数量。|关于说明信息，请参见下方**Result**列的说明。|
|**Total Warn**|需要关注的不计分检查项数量。|关于说明信息，请参见下方**Result**列的说明。|
|**Total Pass**|检查项通过数量。|否|
|**Section Id**|CIS基线标准中定义的章节编号。|否|
|**Section Description**|CIS基线标准中定义的章节简述。|否|
|**Test Id**|CIS基线标准中定义的检查项ID。|否|
|**Test Description**|CIS基线标准中定义的检查项简述。|否|
|**Scored**|检查项是否计分。取值如下：-   Scored：计分。
-   Not scored：不计分。

|否|
|**Test Remediation**|检查项失败时推荐的调整/修复方法。更多信息，请参见[CIS Kubernetes基线标准文档](https://www.cisecurity.org/benchmark/kubernetes/)。

|关于说明信息，请参见下方**Result**列的说明。|
|**Result**|检查项结果。取值如下：-   fail：计分检查项未通过。
-   warn：检查项不计分需要关注。
-   pass：检查通过。

|各个值可采取的措施如下：-   fail：强烈建议根据**Test Remediation**列的推荐方法进行调整或修复。您也可以根据您的实际业务场景来判断是否调整或修复。
-   warn：保持关注，根据实际业务场景进行调整。
-   pass：不需要采取措施。 |

**相关文档**  


[CIS Kubernetes基线](https://www.cisecurity.org/benchmark/kubernetes/)

[security-inspector](/cn.zh-CN/产品发布记录/组件介绍与变更记录/安全/security-inspector.md)

