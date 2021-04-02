Configure NTP for pods 
===========================================

This topic describes how to configure a Network Time Protocol (NTP) service for pods that run on a virtual-kubelet node. The NTP service synchronizes time between containers and the NTP server. 

Prerequisites 
----------------------------------

Virtual Kubelet is upgraded to the latest version.

Background information 
-------------------------------------------

The engineer who is responsible for upgrading Virtual Kubelet varies based on the Kubernetes cluster type:

* Serverless Kubernetes (ASK) cluster: the administrator.

  

* Managed Kubernetes cluster: you.

  

* Dedicated Kubernetes cluster: you.

  

* Self-managed Kubernetes cluster: you.

  




Procedure 
------------------------------

In the configuration file of the pod, add the following annotation to specify the IP address of the NTP server that you want to use: `k8s.aliyun.com/eci-ntp-server`. 

1. Create a YAML file that is used to configure the NTP service. 

       vim set-ntp-pod.yaml

   

   The content of the YAML file is:

       apiVersion: v1
       kind: Pod
       metadata:
         annotations:
           k8s.aliyun.com/eci-ntp-server: 10.10.5.1 # The IP address of the NTP server.
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

   

2. Apply the configuration in the YAML file to the pod. 

       kubectl apply -f set-ntp-pod.yaml

   




Check the result 
-------------------------------------

Log on the elastic container instance to check whether the NTP service works as expected. 

1. Query the information about the pod. 

       kubectl get pod/set-custom-ntp

   

   Sample output:

       NAME                  READY   STATUS    RESTARTS   AGE
       set-custom-ntp   1/1         Running   0                 7m20s

   

2. Log on to a container. 

       kubectl exec set-custom-ntp -it -- bash

   

3. Query the source of the time of the container. 

       chronyc sources

   

   If the IP address of the NTP server is returned, it indicates that the NTP service works as expected. Sample output:

       210 Number of sources = 1
       MS Name/IP address         Stratum Poll Reach LastRx Last                            sample
       ===============================================================================
       ^* 10.10.5.1                    2           6     377     35       +40us[ +135us] +/-   14ms 

   



