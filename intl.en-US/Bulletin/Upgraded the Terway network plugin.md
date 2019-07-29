# Upgraded the Terway network plugin {#concept_262573 .concept}

-   v1.0.9.15-g3957085-aliyun

    Fixed the bug that caused upgrade failures that occurred to the Terway network plugin.

-   v1.0.9.14-ga0346bb-aliyun
    -   Fixed the bug that caused the Terway network plugin to fail to obtain the EIP information.
    -   Fixed the bug that caused the error message **failed to move veth to host netns: file exists**. The error message was displayed when a container was created.
    -   Added a new feature that enables the system to detect the status of an EIP and retrieve its exception releases.
    -   Changed the health check method for the Terway network plugin from the HTTP path check to the TCP port check.

The preceding upgrades do not affect application functionality.

We recommend that you use the Container Service console to upgrade the Terway network plugin to the latest version.

