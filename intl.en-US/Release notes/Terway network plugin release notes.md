# Terway network plugin release notes {#concept_wk1_grd_qfb .concept}

This topic describes the latest releases of the Terway network plugin.

## April 11, 2019 {#section_z9i_r3p_0p0 .section}

**Version**: v1.0.9.15-g3957085-aliyun

**Image address**: registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.9.15-g3957085-aliyun

**Changes**: Fixed the bug that caused upgrade failures that occurred to the Terway network plugin.

**Remark**: This upgrade does not affect application functionality.

## March 28, 2019 {#section_3mj_c62_b7d .section}

**Version**: v1.0.9.14-ga0346bb-aliyun

**Image address**: registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.9.14-ga0346bb-aliyun

**Changes**:

-   Fixed the bug that caused the Terway network plugin to fail to obtain the EIP information.
-   Fixed the bug that caused the error message **failed to move veth to host netns: file exists**. The error message was displayed when a container was created.
-   Added a new feature that enables the system to detect the status of an EIP and retrieve its exception releases.
-   Changed the health check method for the Terway network plugin from the HTTP path check to the TCP port check.

**Remark**: This upgrade does not affect application functionality.

