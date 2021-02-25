Change the image of a pod with the IP address unchanged 
============================================================================

Assume that you deploy a bare pod on a virtual node. You may need to use new images after you debug the pod or add features to the pod during iteration. In this case, you need to change the image of the bare pod and make sure that the IP address of the pod is not changed. This topic describes how to change the image of a pod with the IP address unchanged.

Prerequisites 
----------------------------------

1. The Elastic Container Instance (ECI) and Resource Access Management (RAM) services are activated. A RAM role related to ECI is assigned to your account.

2. The Kubernetes command-line client `kubectl` is installed on your local computer and is connected to your Kubernetes cluster. For more information, see [Connect to a Kubernetes cluster by using kubectl](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Cluster management/Manage and access clusters/Use kubectl to connect to an ASK cluster.md).




Create a pod by running a command 
------------------------------------------------------

Prepare a YAML file used to create a pod.

    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
      namespace: default
    spec:
      nodeName: virtual-kubelet
      containers:
      - image: nginx:1.7.9
        imagePullPolicy: Always
        name: nginx



Run the following command to create the pod:

    kubectl create -f nginx.yaml



Query the information about the pod by running a command 
-----------------------------------------------------------------------------

    $ kubectl get pod/nginx -n default -o wide
    NAME    READY   STATUS     RESTARTS   AGE         IP                    NODE                  NOMINATED NODE   READINESS GATES
    nginx     1/1         Running    0                 7m34s      192.168.0.29   virtual-kubelet   <none>                       <none>



Query the information about the pod in the ECI console 
---------------------------------------------------------------------------

![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/141288/cn_zh/1571103244050/eci_detail_1.jpg)

Change the image of the pod by running a command 
---------------------------------------------------------------------

You can run one of the following `kubectl` commands to change the image of the bare pod:

* kubectl patch

* kubectl edit

* kubectl apply




In this example, change the image of the pod from the `nginx:1.7.9` to `nginx:1.9.6`.

Run the kubectl apply command 
--------------------------------------------------

Change the images of containers in the pod in the following YAML file:

    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
      namespace: default
    spec:
      nodeName: virtual-kubelet
      containers:
      - image: nginx:1.9.6
        imagePullPolicy: Always
        name: nginx	



Run the following kubectl apply command:

    kubectl apply -f nginx.yaml		



Run the following kubectl command to verify the image and IP address of the pod:

    $ kubectl get pod/nginx -n default -o wide
    NAME    READY   STATUS    RESTARTS   AGE       IP                   NODE                  NOMINATED NODE   READINESS GATES
    nginx     1/1         Running   0                 8m27s   192.168.0.29   virtual-kubelet   <none>                       <none>	 



Run the kubectl edit command 
-------------------------------------------------

Run the following kubectl edit command to open the editor and change the image of the pod in the YAML file:

    kubectl edit pod/nginx -o yaml		



Run the following kubectl command to verify the image and IP address of the pod:

    $ kubectl get pod/nginx -n default -o wide
    NAME    READY   STATUS    RESTARTS   AGE       IP                   NODE                 NOMINATED NODE   READINESS GATES
    nginx     1/1         Running   0                 9m11s   192.168.0.29   virtual-kubelet   <none>                     <none>		



Run the kubectl patch command 
--------------------------------------------------

Run the following kubectl patch command to change the images of containers in the pod:

    kubectl patch pod nginx -p '{"spec":{"containers":[{"name": "nginx","image": "nginx:1.9.6"}]}}'



Run the following kubectl command to verify the image and IP address of the pod:

    $ kubectl get pod -n default -o wide
    NAME    READY   STATUS    RESTARTS   AGE       IP                   NODE                 NOMINATED NODE   READINESS GATES
    nginx     1/1         Running   0                 9m43s   192.168.0.29   virtual-kubelet   <none>                     <none>	



Verify the result in the ECI console 
---------------------------------------------------------

After the image of the pod is changed, log on to the ECI console and view the details of the pod on the `Container Group` page. Verify that the image of the pod is changed and the IP address of the pod is not changed.

![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/141288/cn_zh/1571103892091/eci_image_2.jpg)
