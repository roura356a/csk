---
keyword: [log collection, Job]
---

# Collect the log of a Job

This topic describes how to collect the log of a Job to Log Service.

-   A serverless Kubernetes \(ASK\) cluster is created. For more information, see [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Cluster/Create an ASK cluster.md).
-   A virtual node is deployed in the cluster. For more information, see [Deploy the virtual node controller and use it to create Elastic Container Instance-based pods](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy the virtual node controller and use it to create Elastic Container Instance-based pods.md).
-   An Apsara File Storage NAS \(NAS\) file system is created and a mount target is added. For more information, see [Create a NAS file system]() and [Manage mount targets]().

**Note:** If Log Service is enabled for the cluster, you can mount a volume to the Job to collect log data. Then, you can configure environment variables to synchronize the log data to Log Service.

## Procedure

**Note:**

If Elastic Compute Service \(ECS\) instances are used in a cluster, DaemonSets can be used to collect the standard output of a Job. If elastic container instances are used in a cluster, you cannot use DaemonSets to collect log data. After a Job is complete, the pods created by the Job immediately exit. However, the log of the Job may not be collected. To resolve this problem, you can use the following method:

Mount a NAS file system to the Job and store the log to the NAS file system. Then, mount the NAS file system to a pod that is used to import the log to Log Service.

1.  Create a file named job.yaml by using a kubectl client.

    The following YAML template is used to create a Job to calculate π:

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: pi
    spec:
      template:
        spec:
          containers:
          - name: pi
            image: resouer/ubuntu-bc 
            command: ["sh", "-c", "echo 'scale=1000; 4*a(1)' | bc -l > /eci/a.log 2>&1"] #Saves the output to the specified file.
            volumeMounts:
            - name: log-volume
              mountPath: /eci
              readOnly: false
          restartPolicy: Never
          volumes:
          - name: log-volume
            nfs:
                path: /eci
                server: 04edd48c7c-****.cn-hangzhou.nas.aliyuncs.com
                readOnly: false
      backoffLimit: 4
    ```

2.  Deploy the Job to a virtual node.

    **Note:** For more information, see [Deploy the virtual node controller and use it to create Elastic Container Instance-based pods](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy the virtual node controller and use it to create Elastic Container Instance-based pods.md).

    ```
    kubectl apply -f job.yaml -n fvt-eci
    ```

3.  Run the following command to view the states of the pods:

    ```
    kubectl get pod -n fvt-eci
    ```

4.  Create a file named log-collection.yaml by using the kubectl client and copy the following content to the file. Then, run the command to create a pod and mount the NAS file system to the pod to collect the log of the Job.

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: log-collection
    spec:
      containers:
      - image: nginx:latest
        name: log-collection
        command: ['/bin/sh', '-c', 'echo &(cat /eci/a.log)'] #Prints the log of the Job.
        volumeMounts:
        - mountPath: /eci
          name: log-volume
      restartPolicy: Never
      volumes:
      - name: log-volume
        nfs:
          server: 04edd48c7c-****.cn-hangzhou.nas.aliyuncs.com
          path: /eci
          readOnly: false
    ```


