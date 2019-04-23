# Terway network plugin release notes {#concept_wk1_grd_qfb .concept}

This topic describes the latest releases of the Terway network plugin.

|Date|Image address|Change|Relevant topic|
|----|-------------|------|--------------|
|April 11, 2019|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.9.15-g3957085-aliyun| Fixed the bug that caused upgrade failures that occurred to the Terway network plugin.

 This upgrade does not affect application functionality.

 |[Terway network plugin](../../../../intl.en-US/User Guide/Kubernetes cluster/Network management/Terway network plugin.md#)|
|March 28, 2019|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.9.14-ga0346bb-aliyun| -   Fixed the bug that caused the Terway network plugin to fail to obtain the EIP information.
-   Fixed the bug that prompted the prompted error message **failed to move veth to host netns: file exists** for creating a container.
-   Added the feature where the system detects the EIP status on a regular basis, and retrieves exception releases of EIPs on a regular basis.
-   Optimized the heath check method. Specifically, the health check method for the Terway network plugin was changed from the HTTP path check to the TCP port check.

 This upgrade does not affect application functionality.|[Terway network plugin](../../../../intl.en-US/User Guide/Kubernetes cluster/Network management/Terway network plugin.md#)|

