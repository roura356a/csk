---
keyword: [cluster certificates, renew]
---

# Renew cluster certificates

This topic describes how to renew the certificates used in a cluster.

About two months before a certificate expires, a red button appears on the Clusters page in the console to remind you to renew the certificate. You will also receive an internal message or Short Message Service \(SMS\) notification about the expiring certificate.

You can click the button to renew the certificate. It requires about 5 to 10 minutes to complete the process based on the number of nodes in the cluster. After the certificate is renewed, its validity period is extended by five years.

## Precautions

-   During the renewal process, the following system components will be restarted: kube-apiserver, kube-controller-manager, kube-scheduler. If your business logic is strongly reliant on system components such as kube-apiserver, make sure that your services are available during the renewal before you start the process.
-   We recommend that you renew certificates during off-peak hours.

## Backup

|Node type|Backup content|
|---------|--------------|
|Master|-   /etc/kubernetes/
-   /var/lib/kubelet/pki
-   /etc/systemd/system/kubelet.service.d/10-kubeadm.conf
-   /etc/kubeadm/
-   Business-critical data

**Note:** If /var/lib/kubelet/pki is empty or you have no business-critical data, backup is not required. |
|Worker|-   /etc/kubernetes/
-   /etc/systemd/system/kubelet.service.d/10-kubeadm.conf
-   /var/lib/kubelet/pki/\*
-   Business-critical data

**Note:** If /var/lib/kubelet/pki/\* is empty or you have no business-critical data, backup is not required. |

## Renew certificates

|Certificate or conf filename|Path|Validity period|
|----------------------------|----|---------------|
|-   apiserver.crt
-   apiserver.key

|/etc/kubernetes/pki|The initial validity period is 10 years. The validity period is extended by five years after renewal.|
|-   apiserver-kubelet-client.crt
-   apiserver-kubelet-client.key

|/etc/kubernetes/pki|The initial validity period is 10 years. The validity period is extended by five years after renewal.|
|-   front-proxy-client.crt
-   front-proxy-client.key

|/etc/kubernetes/pki|The initial validity period is 10 years. The validity period is extended by five years after renewal.|
|-   dashboard.crt
-   dashboard.key

|/etc/kubernetes/pki/dashboard|The initial validity period is 10 years. The validity period is extended by five years after renewal.|
|-   kubelet.crt
-   kubelet.key

**Note:**

-   If the kubelet.key file does not exist, renewal is not required.
-   You can still use the cluster after kubelet.crt and kubelet.key expire.

|/var/lib/kubelet/pki **Note:** If this path is empty, renewal is not required.

|The initial validity period is 10 years. The validity period is extended by five years after renewal.|
|admin.conf|/etc/kubernetes|The initial validity period is 10 years. The validity period is extended by five years after renewal.|
|kube.conf|/etc/kubernetes|The initial validity period is 10 years. The validity period is extended by five years after renewal.|
|controller-manager.conf|/etc/kubernetes|The initial validity period is 10 years. The validity period is extended by five years after renewal.|
|scheduler.conf|/etc/kubernetes|The initial validity period is 10 years. The validity period is extended by five years after renewal.|
|kubelet.conf|/etc/kubernetes|The initial validity period is 10 years. The validity period is extended by five years after renewal.|
|config|~/.kube/|The initial validity period is 10 years. The validity period is extended by five years after renewal.|
|-   kubelet-client-current.pem or kubelet-client.crt
-   kubelet-client.key

**Note:** If the kubelet-client.key file does not exist, renewal is not required.

|/var/lib/kubelet/pki **Note:** If this path is empty, renewal is not required.

|The initial validity period is one year. When the certificate is about to expire, it is automatically renewed and the validity period is extended by one year.|

|Certificate or conf filename|Path|Validity period|
|----------------------------|----|---------------|
|-   kubelet.crt
-   kubelet.key

**Note:**

-   If the kubelet.key file does not exist, renewal is not required.
-   You can still use the cluster after kubelet.crt and kubelet.key expire.

|/var/lib/kubelet/pki **Note:** If this path is empty, renewal is not required.

|The initial validity period is 10 years. The validity period is extended by five years after renewal.|
|-   kubelet-client-current.pem or kubelet-client.crt
-   kubelet-client.key

**Note:** If the kubelet-client.key file does not exist, renewal is not required.

|/var/lib/kubelet/pki**Note:** If this path is empty, renewal is not required.

|The initial validity period is one year. When the certificate is about to expire, it is automatically renewed and the validity period is extended by one year.|
|kubelet.conf|/etc/kubernetes|The initial validity period is 10 years. The validity period is extended by five years after renewal.|

