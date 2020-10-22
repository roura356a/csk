# Time zone

When you run applications in pods, you may want to specify time zones for pods. This topic describes how to specify a time zone for an Elastic Container Instance \(ECI\) that runs on the virtual-kubelet node.

## Prerequisites

`Virtual Kubelet` is upgraded to the latest version in your Kubernetes cluster. The user who is responsible for upgrading Virtual Kubelet varies according to the cluster type.

-   Serverless Kubernetes cluster: The administrator upgrades Virtual Kubelet.
-   Managed Kubernetes cluster: You must upgrade Virtual Kubelet by yourself.
-   Dedicated Kubernetes cluster: You must upgrade Virtual Kubelet by yourself.
-   User-created Kubernetes cluster: You must upgrade Virtual Kubelet by yourself.

## Sample YAML configuration file

Create a ConfigMap and specify the time zone in the ConfigMap.

```
$ kubectl create configmap tz --from-file=/usr/share/zoneinfo/Asia/Shanghai
```

Create an YAML configuration file. In the configuration file, add the configuration for mounting the ConfigMap to the /etc/localtime/Shanghai directory.

```
$ cat set-timezone.yaml
apiVersion: v1
kind: Pod
metadata:
  name: timezone
spec:
  containers:
  - name: timezone
    image: registry-vpc.cn-beijing.aliyuncs.com/eci_open/busybox:1.30
    command: [ "sleep", "10000" ]
    volumeMounts:
      - name: tz
        mountPath: /etc/localtime
        subPath: Shanghai
  volumes:
    - name: tz
      configMap:
        name: tz
  nodeSelector:
    type: virtual-kubelet
  tolerations:
  - key: virtual-kubelet.io/provider
    operator: Exists
```

**Run the following command to create an ECI based on the preceding configuration file:**

```
$ kubectl apply -f set-timezone.yaml
```

## Verification

Log on to the ECI and check whether the time zone is configured.

```
$ kubectl get pod/timezone
NAME                  READY   STATUS    RESTARTS   AGE
set-timezone   1/1         Running   0                 7m20s

$ kubectl exec timezone -it -- sh
/ # date -R
Fri, 01 May 2020 10:00:11 +0800
/ #
```

The preceding command output indicates that the time zone of the ECI is configured.

