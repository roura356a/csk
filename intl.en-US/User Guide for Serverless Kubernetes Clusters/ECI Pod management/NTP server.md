# NTP server

This topic describes how to configure a Network Time Protocol \(NTP\) server for an Elastic Container Instance \(ECI\) that runs on the `virtual-kubelet` node. After the custom NTP server is configured, containers in the ECI synchronize time from the NTP server.

## Prerequisites

`Virtual Kubelet` is upgraded to the latest version in your Kubernetes cluster. The user who is responsible for upgrading Virtual Kubelet varies according to the cluster type.

-   Serverless Kubernetes cluster: The administrator upgrades Virtual Kubelet.
-   Managed Kubernetes cluster: You need to upgrade Virtual Kubelet by yourself.
-   Dedicated Kubernetes cluster: You need to upgrade Virtual Kubelet by yourself.
-   User-created Kubernetes cluster: You need to upgrade Virtual Kubelet by yourself.

## Sample YAML configuration file

In the configuration file of the ECI, you need to add the `k8s.aliyun.com/eci-ntp-server` annotation to specify the IP address of the NTP server to be configured.

```
$ cat set-ntp-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    k8s.aliyun.com/eci-ntp-server: 100.100.5.1 # The IP address of your NTP server.
  name: set-custom-ntp
spec:
  nodeName: virtual-kubelet
  containers:
    - image: centos:latest
      command:
        - sleep
        - "3600"
      imagePullPolicy: IfNotPresent
      name: centos
```

Run the following command to create the ECI:****

```
$ kubectl apply -f set-ntp-pod.yaml
```

## Verification

Log on the ECI and check whether the NTP server is configured.

```
$ kubectl get pod/set-custom-ntp
NAME                  READY   STATUS    RESTARTS   AGE
set-custom-ntp   1/1         Running   0                 7m20s

kubectl exec set-custom-ntp -it -- bash
[root@default-set-custom-ntp /]# chronyc sources
210 Number of sources = 1
MS Name/IP address         Stratum Poll Reach LastRx Last                            sample
===============================================================================
^* 100.100.5.1                    2           6     377     35       +40us[ +135us] +/-   14ms	
```

According to the command output, the NTP server whose IP address is `100.100.5.1` is configured for the ECI.

