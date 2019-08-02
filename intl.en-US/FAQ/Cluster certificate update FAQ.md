# Cluster certificate update FAQ {#concept_745057 .concept}

## When do I have to update my cluster certificates? {#section_vqc_587_6os .section}

You can update your cluster certificates two months before your cluster certificates expire.

## How can I update cluster certificates? {#section_cv0_hss_n46 .section}

You can click the red button in the console to update the certificates automatically, or update them by following these steps:

1.  Update data according to the following table.

    |Node type|Backup data|
    |---------|-----------|
    |Master|     -   /etc/kubernetes/
    -   /var/lib/kubelet/pki
    -   /etc/systemd/system/kubelet.service.d/10-kubeadm.conf
    -   /etc/kubeadm/
    -   *Necessary service data*
 **Note:** If the /var/lib/kubelet/pki file contains no data, or if no *necessary service data* is available, do not back up them.

 |
    |Worker|     -   /etc/kubernetes/
    -   /etc/systemd/system/kubelet.service.d/10-kubeadm.conf
    -   /var/lib/kubelet/pki/\*
    -   *Necessary service data*
 **Note:** If the /var/lib/kubelet/pki/\* file contains no data, or if no *necessary service data* is available, do not back up them.

 |

2.  Update certificates according to the following tables.

    |Certificate or conf|Path|
    |-------------------|----|
    |     -   apiserver.crt
    -   apiserver.key
 |/etc/kubernetes/pki|
    |     -   apiserver-kubelet-client.crt
    -   apiserver-kubelet-client.key
 |/etc/kubernetes/pki|
    |     -   front-proxy-client.crt
    -   front-proxy-client.key
 |/etc/kubernetes/pki|
    |     -   dashboard.crt
    -   dashboard.key
 |/etc/kubernetes/pki/dashboard|
    |     -   kubelet.crt
    -   kubelet.key
 **Note:** If the kubelet.key file contains no data, do not update it.

 |/var/lib/kubelet/pki **Note:** If the file contains no data, do not update it.

 |
    |admin.conf|/etc/kubernetes|
    |kube.conf|/etc/kubernetes|
    |controller-manager.conf|/etc/kubernetes|
    |scheduler.conf|/etc/kubernetes|
    |kubelet.conf|/etc/kubernetes|
    |config|~/.kube/|
    |     -   kubelet-client-current.pem or kubelet-client.crt\\
    -   kubelet-client.key
 **Note:** If the kubelet-client.key file contains no data, do not update it.

 |/var/lib/kubelet/pki **Note:** If the file contains no data, do not update it.

 |

    |Certificate or conf|Path|
    |-------------------|----|
    |     -   kubelet.crt
    -   kubelet.key
 **Note:** If the kubelet.key file contains no data, do not update it.

 |/var/lib/kubelet/pki **Note:** If the file contains no data, do not update the file.

 |
    |     -   kubelet-client-current.pem or kubelet-client.crt
    -   kubelet-client.key
 **Note:** If the kubelet-client.key file contains no data, do not update it.

 |/var/lib/kubelet/pki **Note:** If the file contains no data, do not update the file.

 |
    |kubelet.conf|/etc/kubernetes|


## How long does it take to update a cluster certificate? {#section_ttv_j4z_m54 .section}

Each update takes 5 to 10 minutes.

**Note:** 

-   When the certificates are being updated, the native components of Kubernetes will be restarted for a while. Therefore, we recommend that you perform the update during off-peak hours.
-   The update does not affect existing online services.

