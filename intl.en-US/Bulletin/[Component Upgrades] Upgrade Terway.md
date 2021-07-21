# \[Component Upgrades\] Upgrade Terway

-   v1.0.9.15-g3957085-aliyun

    The issue that the Terway upgrade occasionally fails is fixed.

-   v1.0.9.14-ga0346bb-aliyun
    -   The issue that Terway fails to obtain the elastic network interface \(ENI\) information is fixed.
    -   The issue that the **failed to move veth to host netns: file exists** error is reported during container creation is fixed.
    -   Periodic scanning is supported to check the status of ENIs. ENIs that are abnormally released are periodically recycled.
    -   Health checks are optimized. TCP port checks are performed instead of HTTP path checks.

You can upgrade Terway in the ACK console. The upgrade does not affect your workloads.

