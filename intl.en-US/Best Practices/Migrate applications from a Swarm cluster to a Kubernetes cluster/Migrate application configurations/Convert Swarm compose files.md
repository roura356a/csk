# Convert Swarm compose files

This topic describes how to convert Swarm compose files.

1.  Use Kompose to convert a Swarm compose file.

    After a Swarm compose file is pre-processed, run the following command to convert the file by using Kompose:

    ```
    kompose-linux-amd64 convert -f source/swarm-piggymetrics.yaml --volumes PersistentVolumeClaimOrHostPath
    ```

    ![kompose](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4077685061/p47978.png)

    **Note:** If the conversion succeeds, Kubernetes resource files are generated. Warnings appear for labels that cannot be automatically converted. Use one of the following methods to process these labels:

    -   Modify the Swarm compose file and use Kompose to convert the file.
    -   Modify the generated Kubernetes resource files.
    -   Ignore the warnings. Deploy the generated Kubernetes resource files. Then, log on to the Container Service for Kubernetes \(ACK\) console and manually migrate the configurations.
2.  Modify the Swarm compose file.

    Kompose can convert most Swarm labels. A small number of Swarm labels cannot be automatically converted. Kompose will be optimized to support more labels. When warnings appear, modify the Swarm compose file based on the warning information and use Kompose to convert the file again. For more information about the exceptions that may occur during conversion and how to handle these exceptions, see [Troubleshooting](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Migrate application configurations/Troubleshooting.md).

3.  Modify the generated Kubernetes resource files.

    After a Swarm compose file is converted to Kubernetes resource files, modify these Kubernetes resource files to migrate the labels that cannot be converted by Kompose. These labels include:

    -   aliyun.routing.port\_
    -   aliyun.global
    -   external
    -   environment: constraint
    -   extra\_hosts
    -   net
    -   dns
    For more information about each label, see [Application configuration labels](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter comparisons/Application configuration labels.md), [Application release labels](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter comparisons/Application release labels.md),[Network configuration labels](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter comparisons/Network configuration labels.md), and [Log configuration parameters](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Appendix: Parameter comparisons/Log configuration parameters.md).


