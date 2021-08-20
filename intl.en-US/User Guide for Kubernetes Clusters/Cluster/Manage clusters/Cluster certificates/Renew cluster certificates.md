# Renew cluster certificates

This topic describes how to renew the certificates used in a cluster.

About two months before a certificate expires, a red button appears on the Clusters page in the console to remind you to renew the certificate. You will also receive an internal message or Short Message Service \(SMS\) notification about the expiring certificate.

You can click the button to renew the certificate. It requires about 5 to 10 minutes to complete the process based on the number of nodes in the cluster. After the certificate is renewed, its validity period is extended by five years.

## Notes

-   During the renewal process, the following system components will be restarted: kube-apiserver, kube-controller-manager, kube-scheduler, and kubelet. If your business logic is strongly reliant on system components such as apiserver, make sure that your services are available during the renewal before you start the process.
-   We recommend that you renew certificates during off-peak hours.

## Backup

|Node type|Backup content|
|---------|--------------|
|Master|-   /etc/kubernetes/
-   /var/lib/kubelet/pki
-   /etc/systemd/system/kubelet.service.d/10-kubeadm.conf
-   /etc/kubeadm/
-   Critical business data

**Note:** If /var/lib/kubelet/pki is empty or you have no critical business data, backup is not required. |
|Worker|-   /etc/kubernetes/
-   /etc/systemd/system/kubelet.service.d/10-kubeadm.conf
-   /var/lib/kubelet/pki/\*
-   Critical business data

**Note:** If /var/lib/kubelet/pki/\* is empty or you have no critical business data, backup is not required. |

## Certificates

|Certificate or conf filename|Path|
|----------------------------|----|
|-   apiserver.crt
-   apiserver.key

|/etc/kubernetes/pki|
|-   apiserver-kubelet-client.crt
-   apiserver-kubelet-client.key

|/etc/kubernetes/pki|
|-   front-proxy-client.crt
-   front-proxy-client.key

|/etc/kubernetes/pki|
|-   dashboard.crt
-   dashboard.key

|/etc/kubernetes/pki/dashboard|
|admin.conf|/etc/kubernetes|
|kube.conf|/etc/kubernetes|
|controller-manager.conf|/etc/kubernetes|
|scheduler.conf|/etc/kubernetes|
|kubelet.conf|/etc/kubernetes|
|config|~/.kube/|
|-   kubelet-client-current.pem or kubelet-client.crt\\
-   kubelet-client.key

**Note:** If the kubelet-client.key file does not exist, renewal is not required.

|/var/lib/kubelet/pki **Note:** If this path is empty, renewal is not required. |

|Certificate or conf filename|Path|
|----------------------------|----|
|-   kubelet-client-current.pem or kubelet-client.crt
-   kubelet-client.key

**Note:** If the kubelet-client.key file does not exist, renewal is not required.

|/var/lib/kubelet/pki **Note:** If this path is empty, renewal is not required. |
|kubelet.conf|/etc/kubernetes|

