# AGS usage examples

## Prerequisites

-   A Container Service for Kubernetes cluster is created. For more information, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).
-   The kubectl client is connected to the Kubernetes cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

## Obtain logs from Log Service

1.  Run the `ags config sls` command to install and configure Log Service in Alibaba Cloud Genomics Compute Service \(AGS\).

    Argo can obtain the logs of a pod only from the local node where the pod is located. If the pod or the node is deleted, the logs are also deleted. This brings inconvenience to the analysis of error cause. After logs are uploaded to Log Service, AGS can obtain the logs from Log Service even after the node is deleted.

2.  Run the `ags logs` command to query the logs of a workflow.

    You can run the `ags logs POD/WORKFLOW` command to query the logs of a pod or workflow.

    ```
    [root@iZwz92q9h36kv8posr0i6uZ ~]# ags logs
    view logs of a workflow
    
    Usage:
     ags logs POD/WORKFLOW [flags]
    
    Flags:
     -c, --container string    Print the logs of this container (default "main")
     -f, --follow              Specify if the logs should be streamed.
     -h, --help                help for logs
     -l, --recent-line int     how many lines to show in one call (default 100)
         --since string        Only return logs newer than a relative duration like 5s, 2m, or 3h. Defaults to all logs. Only one of since-time / since may be used.
         --since-time string   Only return logs after a specific date (RFC3339). Defaults to all logs. Only one of since-time / since may be used.
         --tail int            Lines of recent log file to display. Defaults to -1 with no selector, showing all log lines otherwise 10, if a selector is provided. (default -1)
         --timestamps          Include timestamps on each line in the log output
     -w, --workflow            Specify that whole workflow logs should be printed
    ```

    **Note:**

    -   If a pod exists on the local node, AGS obtains the logs from the local node. In this case, AGS is compatible with all flags of original Argo command.
    -   If a pod has been deleted, AGS obtains the logs from Log Service. By default, the latest 100 logs are returned. You can use the -l flag to specify the number of logs to return.

## Run kubectl commands

You can run kubectl commands in AGS.

```
[root@iZwz92q9h36kv8posr0i6uZ ~]# ags get test-v2
Name:                test-v2
Namespace:           default
ServiceAccount:      default
Status:              Running
Created:             Thu Nov 22 11:06:52 +0800 (2 minutes ago)
Started:             Thu Nov 22 11:06:52 +0800 (2 minutes ago)
Duration:            2 minutes 46 seconds

STEP           PODNAME             DURATION  MESSAGE
● test-v2
└---● bcl2fq  test-v2-2716811808  2m

[root@iZwz92q9h36kv8posr0i6uZ ~]# ags kubectl describe pod test-v2-2716811808
Name:               test-v2-2716811808
Namespace:          default
Priority:           0
PriorityClassName:  <none>
Node:               cn-shenzhen.i-wz9gwobtqrbjgfnqxl1k/192.168.0.94
Start Time:         Thu, 22 Nov 2018 11:06:52 +0800
Labels:             workflows.argoproj.io/completed=false
                   workflows.argoproj.io/workflow=test-v2
Annotations:        workflows.argoproj.io/node-name=test-v2[0].bcl2fq
                   workflows.argoproj.io/template={"name":"bcl2fq","inputs":{},"outputs":{},"metadata":{},"container":{"name":"main","image":"registry.cn-hangzhou.aliyuncs.com/dahu/curl-jp:1.2","command":["sh","-c"],"ar...
Status:             Running
IP:                 172.16.1.152
Controlled By:      Workflow/test-v2
```

By running the ags kubectl describe pod command, you can query the details of a pod. AGS supports all native kubectl commands.

## View the resource usage of a workflow

1.  Create the arguments-workflow-resource.yaml file and copy the following code to the file. Run the `ags submit arguments-workflow-resource.yaml` command to specify the resource request.

    ```
    apiVersion: argoproj.io/v1alpha1
    kind: Workflow
    metadata:
      name: test-resource
    spec:
      arguments: {}
      entrypoint: test-resource-
      templates:
      - inputs: {}
        metadata: {}
        name: test-resource-
        outputs: {}
        parallelism: 1
        steps:
        - - arguments: {}
            name: bcl2fq
            template: bcl2fq
      - container:
          args:
          - id > /tmp/yyy;echo `date` > /tmp/aaa;ps -e -o comm,euid,fuid,ruid,suid,egid,fgid,gid,rgid,sgid,supgid
            > /tmp/ppp;ls -l /tmp/aaa;sleep 100;pwd
          command:
          - sh
          - -c
          image: registry.cn-hangzhou.aliyuncs.com/dahu/curl-jp:1.2
          name: main
          resources:                #don't use too much resources
            requests:
              memory: 320Mi
              cpu: 1000m
        inputs: {}
        metadata: {}
        name: bcl2fq
        outputs: {}
    ```

2.  Run the `ags get test456 --show` command to query the resource usage of the test456 workflow.

    The memory and CPU usage of the pod corresponding to test456 appears after you run the command.

    ```
    [root@iZwz92q9h36kv8posr0i6uZ ~]# ags get test456 --show
    Name:                test456
    Namespace:           default
    ServiceAccount:      default
    Status:              Succeeded
    Created:             Thu Nov 22 14:41:49 +0800 (2 minutes ago)
    Started:             Thu Nov 22 14:41:49 +0800 (2 minutes ago)
    Finished:            Thu Nov 22 14:43:30 +0800 (27 seconds ago)
    Duration:            1 minute 41 seconds
    Total CPU:           0.02806    (core*hour)
    Total Memory:        0.00877    (GB*hour)
    
    STEP           PODNAME             DURATION  MESSAGE  CPU(core*hour)  MEMORY(GB*hour)
    ✔ test456                                            0               0
    └---✔ bcl2fq  test456-4221301428  1m                 0.02806         0.00877
    ```


## Configure securityContext

Create the arguments-security-context.yaml file and copy the following code to the file. Run the `ags submit arguments-security-context.yaml` command to use the corresponding Pod Security Policy \(PSP\) for permission control.

```
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  name: test
spec:
  arguments: {}
  entrypoint: test-security-
  templates:
  - inputs: {}
    metadata: {}
    name: test-security-
    outputs: {}
    parallelism: 1
    steps:
    - - arguments: {}
        name: bcl2fq
        template: bcl2fq
  - container:
      args:
      - id > /tmp/yyy;echo `date` > /tmp/aaa;ps -e -o comm,euid,fuid,ruid,suid,egid,fgid,gid,rgid,sgid,supgid
        > /tmp/ppp;ls -l /tmp/aaa;sleep 100;pwd
      command:
      - sh
      - -c
      image: registry.cn-hangzhou.aliyuncs.com/dahu/curl-jp:1.2
      name: main
      resources:                #don't use too much resources
        requests:
          memory: 320Mi
          cpu: 1000m
    inputs: {}
    metadata: {}
    name: bcl2fq
    outputs: {}
    securityContext:
      runAsUser: 800
```

## Configure automatic retry by using YAML

Some bash commands fail for unknown reasons and the errors can be resolved through retrying. AGS provides an automatic retry mechanism based on YAML configuration. When a command fails to run in a pod, AGS automatically retries the command. You can set the maximum number of retries.

Create the arguments-auto-retry.yaml file and copy the following code to the file. Run the `ags submit arguments-auto-retry.yaml` command to configure the automatic retry mechanism for workflows.

```
# This example demonstrates the use of retries for a single container.
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: retry-container-
spec:
  entrypoint: retry-container
  templates:
  - name: retry-container
    retryStrategy:
      limit: 10
    container:
      image: python:alpine3.6
      command: ["python", -c]
      # fail with a 66% probability
      args: ["import random; import sys; exit_code = random.choice([0, 1, 1]); sys.exit(exit_code)"]
```

## Retry a workflow from a failed step

A step may fail in a workflow. You can retry the workflow from the failed step.

1.  Run the `ags get test456 --show` command to find the step where the test456 workflow fails.

    ```
    [root@iZwz92q9h36kv8posr0i6uZ ~]# ags get test456 --show
    Name:                test456
    Namespace:           default
    ServiceAccount:      default
    Status:              Succeeded
    Created:             Thu Nov 22 14:41:49 +0800 (2 minutes ago)
    Started:             Thu Nov 22 14:41:49 +0800 (2 minutes ago)
    Finished:            Thu Nov 22 14:43:30 +0800 (27 seconds ago)
    Duration:            1 minute 41 seconds
    Total CPU:           0.0572   (core*hour)
    Total Memory:        0.01754    (GB*hour)
    
    STEP           PODNAME             DURATION  MESSAGE  CPU(core*hour)  MEMORY(GB*hour)
     ✔ test456                                            0               0
     └---✔ bcl2fq  test456-4221301428  1m                 0.02806         0.00877
     └---X bcl2fq  test456-4221301238  1m                 0.02806         0.00877
    ```

2.  Run the `ags retry test456` command to retry the test456 workflow from the failed step.

## Run a workflow by using ECI

For more information about Elastic Container Instance \(ECI\), see [Elastic Container Instance](https://www.alibabacloud.com/help/zh/doc-detail/89129.html).

Install AGS before configuring ECI. For more information, see [t422672.md\#section\_bs7\_2zj\_w60](/intl.en-US/User Guide for Genomics Service/AGS workflow/Introduction to AGS CLI.md).

1.  Run the `kubectl get cm -n argo` command to obtain the name of the YAML ConfigMap file of the workflow.

    ```
    [root@iZwz9f4ofes6kbo01ipuf1Z ~]# kubectl get cm -n argo
    NAME                            DATA      AGE
    workflow-controller-configmap   1         4d
    ```

2.  Run the `kubectl get cm -n argo workflow-controller-configmap -o yaml` command to open the workflow-controller-configmap.yaml file, and copy the following code to the file to overwrite all existing information:

    ```
    apiVersion: v1
    data:
      config: |
        containerRuntimeExecutor: k8sapi
    kind: ConfigMap
    ```

3.  Run the `kubectl delete pod <*podName*>` command to restart the Argo controller.

    **Note:** In the preceding command, *podName* is the name of the pod where the workflow is located.

4.  Create the arguments-workflow-eci.yaml file and copy the following code to the file. Run the `ags submit arguments-workflow-eci.yaml` command to add nodeSelector and tolerations to the pod running in ECI.

    ```
    apiVersion: argoproj.io/v1alpha1
    kind: Workflow
    metadata:
      generateName: hello-world-
    spec:
      entrypoint: whalesay
      templates:
      - name: whalesay
        container:
          image: docker/whalesay
          command: [env]
          #args: ["hello world"]
          resources:
            limits:
              memory: 32Mi
              cpu: 100m
        nodeSelector:               # add nodeSelector
          type: virtual-kubelet
        tolerations:                # add tolerations
        - key: virtual-kubelet.io/provider
          operator: Exists
        - key: alibabacloud.com
          effect: NoSchedule
    ```


## View the actual and peak resource usage of a workflow

The AGS workflow controller automatically obtains the actual resource usage per minute of pods through metrics-server, and calculates the total and peak usage for each pod.

Run the `ags get steps-jr6tw --metrics` command to query the actual and peak resource usage of the steps-jr6tw workflow.

```
➜  ags get steps-jr6tw --metrics
Name:                steps-jr6tw
Namespace:           default
ServiceAccount:      default
Status:              Succeeded
Created:             Tue Apr 16 16:52:36 +0800 (21 hours ago)
Started:             Tue Apr 16 16:52:36 +0800 (21 hours ago)
Finished:            Tue Apr 16 19:39:18 +0800 (18 hours ago)
Duration:            2 hours 46 minutes
Total CPU:           0.00275    (core*hour)
Total Memory:        0.04528    (GB*hour)

STEP            PODNAME                 DURATION  MESSAGE  CPU(core*hour)  MEMORY(GB*hour)  MaxCpu(core)  MaxMemory(GB)
 ✔ steps-jr6tw                                             0               0                0             0
 └---✔ hello1   steps-jr6tw-2987978173  2h                 0.00275         0.04528          0.000005      0.00028
```

## Set workflow priorities

You can set high, medium, and low priorities for workflows. A pod with a higher priority can preempt the resources of a pod with a lower priority.

-   You can set a high priority for a pod as follows:

    Create the arguments-high-priority-taskA.yaml file and copy the following code to the file. Run the `ags submit arguments-high-priority-taskA.yaml` command to set a high priority for task A.

    ```
    apiVersion: scheduling.k8s.io/v1beta1
    kind: PriorityClass
    metadata:
      name: high-priority
    value: 1000000
    globalDefault: false
    description: "This priority class should be used for XYZ service pods only."
    ```

-   You can set a medium priority for a pod as follows:

    Create the arguments-high-priority-taskB.yaml file and copy the following code to the file. Run the `ags submit arguments-high-priority-taskB.yaml` command to set a medium priority for task B.

    ```
    apiVersion: scheduling.k8s.io/v1beta1
    kind: PriorityClass
    metadata:
      name: medium-priority
    value: 100
    globalDefault: false
    description: "This priority class should be used for XYZ service pods only."
    ```

-   You can set a high priority for a workflow as follows:

    Create the arguments-high-priority-Workflow.yaml file and copy the following code to the file. Run the `ags submit arguments-high-priority-Workflow.yaml` command to set a high priority for all pods of a workflow.

    ```
    apiVersion: argoproj.io/v1alpha1
    kind: Workflow                  # new type of k8s spec
    metadata:
      generateName: high-proty-   # name of the workflow spec
    spec:
      entrypoint: whalesay          # invoke the whalesay template
      podPriorityClassName: high-priority # workflow level priority
      templates:
      - name: whalesay              # name of the template
        container:
          image: ubuntu
          command: ["/bin/bash", "-c", "sleep 1000"]
          resources:
            requests:
              cpu: 3
    						
    ```


The following example assumes that a workflow has two pods and you need to set a high priority for one pod and a medium priority for the other pod. Then, the pod with the high priority can preempt the resources of the pod with the medium priority.

1.  Create the arguments-high-priority-steps.yaml file and copy the following code to the file. Run the `ags submit arguments-high-priority-steps.yaml` command to set different priorities for the pods.

    ```
    apiVersion: argoproj.io/v1alpha1
    kind: Workflow
    metadata:
      generateName: steps-
    spec:
      entrypoint: hello-hello-hello
    
      templates:
      - name: hello-hello-hello
        steps:
        - - name: low           
            template: low
        - - name: low-2          
            template: low
          - name: high           
            template: high
    
      - name: low
        container:
          image: ubuntu
          command: ["/bin/bash", "-c", "sleep 30"]
          resources:
            requests:
              cpu: 3
    
      - name: high
        priorityClassName: high-priority # step level priority
        container:
          image: ubuntu
          command: ["/bin/bash", "-c", "sleep 30"]
          resources:
            requests:
              cpu: 3
    ```

2.  Check the execution result of the workflow. When the workflow runs, the pod with the high priority preempts the resources of the pod with the medium priority and deletes the pod with the medium priority. The result is as follows:

    ```
    Name:                steps-sxvrv
    Namespace:           default
    ServiceAccount:      default
    Status:              Failed
    Message:             child 'steps-sxvrv-1724235106' failed
    Created:             Wed Apr 17 15:06:16 +0800 (1 minute ago)
    Started:             Wed Apr 17 15:06:16 +0800 (1 minute ago)
    Finished:            Wed Apr 17 15:07:34 +0800 (now)
    Duration:            1 minute 18 seconds
    
    STEP            PODNAME                 DURATION  MESSAGE
     ✖ steps-sxvrv                                    child 'steps-sxvrv-1724235106' failed
     ├---✔ low      steps-sxvrv-3117418100  33s
     └-·-✔ high     steps-sxvrv-603461277   45s
       └-⚠ low-2    steps-sxvrv-1724235106  45s       pod deleted
    ```

    **Note:** Configure priorities with caution. A pod with a higher priority can preempt the resources of a pod with a lower priority. This will stop tasks with a lower priority and interrupt running processes.


## Use the workflow filter

For a workflow that contains a large number of pods, you can use the workflow filter to query pods in the specified state in the workflow.

1.  Run the `ags get <Pod name\> --status Running` command to query the pods in the Running state.

    ```
    [root@iZ8vb19036cvgu1tpxkzl1Z workflow-prioty]# ags get pod-limits-n262v --status Running
    Name:                pod-limits-n262v
    Namespace:           default
    ServiceAccount:      default
    Status:              Running
    Created:             Wed Apr 17 15:59:08 +0800 (1 minute ago)
    Started:             Wed Apr 17 15:59:08 +0800 (1 minute ago)
    Duration:            1 minute 17 seconds
    Parameters:
      limit:             300
    
    STEP                   PODNAME                      DURATION  MESSAGE
     ● pod-limits-n262v
       ├-● run-pod(13:13)  pod-limits-n262v-3643890604  1m
       ├-● run-pod(14:14)  pod-limits-n262v-4115394302  1m
       ├-● run-pod(16:16)  pod-limits-n262v-3924248206  1m
       ├-● run-pod(17:17)  pod-limits-n262v-3426515460  1m
       ├-● run-pod(18:18)  pod-limits-n262v-824163662   1m
       ├-● run-pod(20:20)  pod-limits-n262v-4224161940  1m
       ├-● run-pod(22:22)  pod-limits-n262v-1343920348  1m
       ├-● run-pod(2:2)    pod-limits-n262v-3426502220  1m
       ├-● run-pod(32:32)  pod-limits-n262v-2723363986  1m
       ├-● run-pod(34:34)  pod-limits-n262v-2453142434  1m
       ├-● run-pod(37:37)  pod-limits-n262v-3225742176  1m
       ├-● run-pod(3:3)    pod-limits-n262v-2455811176  1m
       ├-● run-pod(40:40)  pod-limits-n262v-2302085188  1m
       ├-● run-pod(6:6)    pod-limits-n262v-1370561340  1m
    ```

2.  Run the `ags get <Pod name\> --sum-info` command to collect the statistics on the status of pods.

    ```
    [root@iZ8vb19036cvgu1tpxkzl1Z workflow-prioty]# ags get pod-limits-n262v --sum-info --status Error
    Name:                pod-limits-n262v
    Namespace:           default
    ServiceAccount:      default
    Status:              Running
    Created:             Wed Apr 17 15:59:08 +0800 (2 minutes ago)
    Started:             Wed Apr 17 15:59:08 +0800 (2 minutes ago)
    Duration:            2 minutes 6 seconds
    Pending:             198
    Running:             47
    Succeeded:           55
    Parameters:
      limit:             300
    
    STEP                 PODNAME  DURATION  MESSAGE
     ● pod-limits-n262v
    ```


## Use Autoscaler in the agility version

Before using Autoscaler in the agility version, prepare the following resources:

-   A Virtual Private Cloud \(VPC\).
-   A VSwitch.
-   A security group.
-   The internal endpoint of APIServer of the agile version.
-   The target node specification for scaling.
-   An Elastic Compute Service \(ECS\) instance that can access the Internet.

Run the ags config autoscaler command and enter the required information as prompted.

```
$ ags config autoscaler   Enter the required information as prompted.
Please input vswitchs with comma separated
vsw-hp3cq3fnv47bpz7x58wfe
Please input security group id
sg-hp30vp05x6tlx13my0qu
Please input the instanceTypes with comma separated
ecs.c5.xlarge
Please input the new ecs ssh password
xxxxxxxx
Please input k8s cluster APIServer address like(192.168.1.100)
172.24.61.156
Please input the autoscaling mode (current: release. Type enter to skip.)
Please input the min size of group (current: 0. Type enter to skip.)
Please input the max size of group (current: 1000. Type enter to skip.)
Create scaling group successfully.
Create scaling group config successfully.
Enable scaling group successfully.
Succeed
```

After the configuration is complete, log on to the [Auto Scaling console](https://essnew.console.aliyun.com) to check the scaling group that you have created.

## Configure and use a ConfigMap

In the following example, hostNetwork is used by default.

1.  Run the `kubectl get cm -n argo` command to obtain the name of the YAML ConfigMap file of the workflow.

    ```
    [root@iZ8vb19036cvgu1tpxkzl1Z ~]# kubectl get cm -n argo
    NAME                            DATA   AGE
    workflow-controller-configmap   1      6d23h
    ```

2.  Run the `kubectl edit cm workflow-controller-configmap -n argo` command to open the workflow-controller-configmap.yaml file, and copy the following code to the file:

    ```
    data:
      config: |
        extraConfig:
          enableHostNetwork: true
          defaultDnsPolicy: Default
    ```

    The content of the updated workflow-controller-configmap.yaml file is as follows:

    ```
    apiVersion: v1
    data:
      config: |
        extraConfig:
          enableHostNetwork: true
          defaultDnsPolicy: Default
    kind: ConfigMap
    metadata:
      name: workflow-controller-configmap
      namespace: argo
    ```

3.  After the configuration is complete, verify that a newly deployed workflow uses hostNetwork by default, and the value of the dnsPolicy parameter is Default.
4.  Optional. If a PSP is configured, add the following code to the YAML configuration file of the PSP:

    ```
    hostNetwork: true
    ```

    **Note:** You need to change the value of the hostNetwork parameter to true if the parameter exists in the YAML file.

    For more information about a complete example of the YAML file, click [YAML](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#host-namespaces).


