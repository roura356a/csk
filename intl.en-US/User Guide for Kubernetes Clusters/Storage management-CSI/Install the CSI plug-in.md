# Install the CSI plug-in

The CSI plug-in consists of CSI-Plugin and CSI-Provisioner. This topic describes how to install the CSI plug-in in a cluster of Container Service for Kubernetes \(ACK\).

**Note:** To install the CSI plug-in, make sure that the Kubernetes version of the cluster is later than 1.14 and the CSI plug-in is selected when you create the cluster. You can also manually set `enable-controller-attach-detach` to true for kubelet on each node.

## Considerations

-   To use FlexVolume, set `--enable-controller-attach-detach` to false for kubelet. By default, this parameter is set to false for ACK clusters.
-   Install the CSI plug-in in the kube-system namespace.

## Install CSI-Plugin and CSI-Provisioner

By default, CSI-Plugin and CSI-Provisioner are installed in ACK clusters. If CSI-Plugin and CSI-Provisioner are not installed, you can manually install them. For more information, see [alibaba-cloud-csi-driver](https://github.com/kubernetes-sigs/alibaba-cloud-csi-driver/blob/master/README.md).

## Verify the installation

You can connect to a master node of your ACK cluster to check whether CSI-Plugin and CSI-Provisioner are installed. For more information about how to connect to a master node of your ACK cluster, see [Use SSH to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use SSH to connect to an ACK cluster.md).

-   Run the following command to generate several lists of pods that are in the Running state. The number of lists is determined by the number of nodes.

    ```
    kubectl get pod -n kube-system | grep csi-plugin
    ```

-   Run the following command to generate a list of pods that are in the Running state:

    ```
    kubectl get pod -n kube-system | grep csi-provisioner
    ```


