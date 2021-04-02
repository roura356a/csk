Configure a time zone for a pod 
====================================================

This topic describes how to configure different time zones for pods that run on Virtual Kubelet. When you use pods to deploy applications and want your pods to specify different time zones of different locations, you can refer to this topic. 

Prerequisites 
----------------------------------

Virtual Kubelet is upgraded to the latest version.

Background information 
-------------------------------------------

The following list describes the methods to upgrade Virtual Kubelet to the latest version for different types of Kubernetes clusters:

* Serverless Kubernetes (ASK) clusters: The administrator upgrades Virtual Kubelet.

  

* Managed Kubernetes clusters: You must upgrade Virtual Kubelet by yourself.

  

* Dedicated Kubernetes clusters: You must upgrade Virtual Kubelet by yourself.

  

* Self-managed Kubernetes clusters: You must upgrade Virtual Kubelet by yourself.

  




Procedure 
------------------------------

1. Create a ConfigMap and import the time zone that you want to specify. 

   For other time zones, use the corresponding files in the /usr/share/zoneinfo/Asia/ directory. Example:

       kubectl create configmap tz --from-file=/usr/share/zoneinfo/Asia/Shanghai

   

2. Create a YAML file to configure the time zone. 

       vim set-timezone.yaml

   

   Mount the ConfigMap to the /etc/localtime/Shanghai directory. Example:

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

   

3. Apply the configurations in the YAML file to the pod. 

       kubectl apply -f set-timezone.yaml

   




Verify the result 
--------------------------------------

Log on to the container and check whether the time zone is configured. 

1. Obtain the pod information. 

       kubectl get pod/timezone

   

   The following example shows the returned result:

       NAME         READY   STATUS    RESTARTS   AGE
       timezone     1/1     Running   0          7m20s

   

2. Go to the container. 

       kubectl exec timezone -it -- sh

   

3. Query the time zone of the container. 

       date -R

   

   If the time returned corresponds to the time zone, the time zone is configured. The following example shows the returned result:

       Fri, 01 May 2020 10:00:11 +0800

   



