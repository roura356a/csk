# Install the plug-in

This topic describes how to install the CSI plug-in in a cluster of Container Service for Kubernetes \(ACK\). The CSI plug-in consists of CSI-Plugin and CSI-Provisioner.

**Note:** To install the CSI plug-in, make sure that the Kubernetes version of the cluster is 1.14 or later and the CSI driver is selected when you create the cluster. You can also manually set `enable-controller-attach-detach` to true for kubelet on each node.

## Notes

-   To use FlexVolume, set `enable-controller-attach-detach` to false for kubelet on each node. By default, this parameter is set to false for ACK clusters.
-   Install the CSI plug-in in the kube-system namespace.

## Install CSI-Plugin and CSI-Provisioner.

By default, CSI-Plugin and CSI-Provisioner are installed in ACK clusters. If CSI-Plugin and CSI-Provisioner are not installed, you can manually install them. For more information, see [alibaba-cloud-csi-driver](https://github.com/kubernetes-sigs/alibaba-cloud-csi-driver/blob/master/README.md).

## Check the installation

You can connect to a master node of your ACK cluster to check the installation result. For more information about how to connect to a master node of your ACK cluster, see [Use SSH to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use SSH to connect to an ACK cluster.md)

-   Run the following command to query pods that are related to CSI-Plugin. If CSI-Plugin is installed, a list of pods in the Running state are returned.

    ```
    kubectl get pod -n kube-system | grep csi-plugin
    ```

-   Run the following command to query pods that are related to CSI-Provisioner. If CSI-Provisioner is installed, a list of pods in the Running state are returned.

    ```
    kubectl get pod -n kube-system | grep csi-provisioner
    ```


