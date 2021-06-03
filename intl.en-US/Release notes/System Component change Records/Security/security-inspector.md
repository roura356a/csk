---
keyword: [security-inspector, Overview, inspection features]
---

# security-inspector

This topic describes the architecture and features of security-inspector. The security-inspector component is the key component for implementing security inspection.

## Architecture

The following figure shows the architecture of security-inspector.

![security-inspector](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6258298951/p129654.png)

## Inspection features

security-inspector provides the following inspection features.

-   The security-inspector component uses Polaris to implement security inspection. This allows you to detect security risks of workload configurations in a Kubernetes cluster in real time.

    **Note:** Polaris is an open-source project that is used to identify security risks of workload configurations in a Kubernetes cluster For more information, see [Polaris](https://github.com/FairwindsOps/polaris).

-   The security-inspector component scans workload configurations in terms of health-checks, image policies, network configurations, resources, security capabilities, and security configurations. This allows you to check whether security risks exist in applications in real time. Solutions are also provided for you to deal with the security risks. For more information, see [Use the inspection feature to check for security risks in the workloads of an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Security/Use the inspection feature to check for security risks in the workloads of an ACK cluster.md).

