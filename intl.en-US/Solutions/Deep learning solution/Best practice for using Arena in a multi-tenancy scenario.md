---
keyword: [Arena, Kubernetes clusters, multi-tenancy scenarios, storage]
---

# Best practice for using Arena in a multi-tenancy scenario

This topic describes how to use Arena in a multi-tenancy scenario by completing five tasks.

Before you start, make sure that the following operations are performed:

-   A Container Service for Kubernetes \(ACK\) cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   An Elastic Compute Service \(ECS\) instance that runs Linux is created in the virtual private cloud \(VPC\) where the ACK cluster is deployed. For more information, see [Create an instance by using the wizard](/intl.en-US/Instance/Create an instance/Create an instance by using the wizard.md).

    In this example, the ECS instance functions as a client. The client is used as an Arena workstation to submit jobs to the ACK cluster.

-   The Arena add-on that supports the latest Arena version is installed. For more information, see [Install the latest version of Arena](/intl.en-US/Solutions/Deep learning solution/Preparations/Install the latest version of Arena.md).

Assume that multiple developers in a company or team want to use Arena to submit jobs. To improve the efficiency of job management, you can divide the developers into several user groups and grant each user group different permissions. This allows you to allocate and isolate resources, and manage permissions by user group.

You can allocate resources of the ACK cluster to each user group based on your requirements, such as GPU, CPU, and memory resources. You can also grant each group member different permissions, and provide a separate environment for each member to run Arena. You can grant group members the permissions to view jobs, manage jobs, and read and write specific data.

![arena](../images/p173496.png "Configure Arena in a multi-tenancy scenario")

The following table describes the detailed information about the nodes of the ACK cluster and the client.

|Hostname|Role|IP address|Number of GPUs|Number of CPU cores|MEM|
|--------|----|----------|--------------|-------------------|---|
|client01|Client|10.0.0.97 \(private\) 39.98.xxx.xxx \(public\)|0|2|8 GiB|
|master01|Master|10.0.0.91 \(private\)|0|4|8 GiB|
|master02|Master|10.0.0.92 \(private\)|0|4|8 GiB|
|master03|Master|10.0.0.93 \(private\)|0|4|8 GiB|
|worker01|Worker|10.0.0.94 \(private\)|1|4|30 GiB|
|worker02|Worker|10.0.0.95 \(private\)|1|4|30 GiB|
|worker03|Worker|10.0.0.96 \(private\)|1|4|30 GiB|

**Note:** Unless otherwise specified, the operations described in this topic are all performed with an administrator account on the client.

## Tasks

This example demonstrates how to complete the following tasks:

-   Task 1: Create two user groups named dev1 and dev2 for the ACK cluster, and add users Bob and Tom to user groups dev1 and dev2 separately.
-   Task 2: Allow Bob and Tom to log on to the client only with their own accounts. Both of them must have a separate environment where they can run Arena.
-   Task 3: Grant Bob and Tom the permissions to view and manage only their own jobs.
-   Task 4: Allocate the GPU, CPU, and memory resources of worker nodes to different user groups. Arena jobs can consume the computing resources of only worker nodes.
-   Task 5: Create volumes that can be shared only within a user group and create volumes that can be shared across all user groups.

|User group|User|GPU|CPU|MEM|Shared volume|
|----------|----|---|---|---|-------------|
|dev1|bob|1|Unlimited|Unlimited|dev1-public and department1-public-dev1|
|dev2|tom|2|8|60 GiB|dev2-public and department1-public-dev2|

**Note:** Volumes department1-public-dev1 and department1-public-dev2 are mounted to the same directory of the NAS file system. Therefore, users in user groups dev1 and dev2 can share the data stored in the directory. Volumes dev1-public and dev2-public are mounted to different directories of the NAS file system. The data stored on dev1-public is accessible to only Bob in user group dev1, and the data stored on dev2-public is accessible to only Tom in user group dev2.

## Step 1: Create and manage users and user groups for the ACK cluster

For security purposes, we recommend that you do not install Arena, run Arena, or manage the ACK cluster on a master node. You can create an ECS instance in the VPC where the ACK cluster is deployed and install Arena on the ECS instance. In this example, the ECS instance functions as a client. You can create a kubeconfig file to enable the ECS instance to access the ACK cluster.

1.  Create users and user groups on the client.

    1.  Use kubectl to connect to the ACK cluster.

        Before you use kubectl, you must install kubectl on the client and modify the kubeconfig file to allow administrators to manage the ACK cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).

        **Note:** The version of kubectl must be 1.10 or later.

    2.  Run the following commands to create user IDs \(UIDs\) and group IDs \(GIDs\) in Linux for the corresponding users and user groups on the client.

        In this example, create UIDs and GIDs for Bob and Tom, and create user groups dev1 and dev2 on the client. Use the Linux account system to complete [Task 2](#li_b62_eoa_lit): each user can log on to the client only with their own username and password, and run Arena only in their own environment.

        ```
        # Create Linux groups, users.
        groupadd -g 10001 dev1
        groupadd -g 10002 dev2
        adduser -u 20001 -s /bin/bash -G dev1 -m bob
        adduser -u 20002 -s /bin/bash -G dev2 -m tom
        
        # Set a password for Bob to log on to the client.
        passwd bob 
        # Set a password for Tom to log on to the client.
        passwd tom
        ```

2.  Create service accounts and namespaces for the ACK cluster.

    After you submit jobs, the jobs are running in the ACK cluster. Each user on the client corresponds to a service account in the ACK cluster and each user group corresponds to a namespace. Therefore, you must create service accounts and namespaces, and make sure that they are mapped to the corresponding users and user groups on the client. You must map namespaces to user groups, and map service accounts to users.

    Log on to the client as the root user. Make sure that the root user has permissions to manage the ACK cluster. For more information, see [Use kubectl to connect to an ACK cluster](#substep_212_rl0_m60). Run the following commands:

    ```
    # Create a namespace that corresponds to user group dev1.
    kubectl create namespace dev1
    
    # Create a namespace that corresponds to user group dev2.
    kubectl create namespace dev2
    
    # Create a service account that corresponds to Bob.
    kubectl create serviceaccount bob -n dev1
    
    # Create a service account that corresponds to Tom.
    kubectl create serviceaccount tom -n dev2
    
    # Query the created service accounts and namespaces.
    kubectl get serviceaccount -n dev1
    kubectl get serviceaccount -n dev2
    ```

    ![namespace serviceacount](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4381205061/p173703.png)


## Step 2: Configure Arena for the users

1.  Install Arena.

    Install Arena on the client. Log on to the client as the root user and download the latest installation package of Arena. Then, decompress the package and run the install.sh script. For more information, see [Install the latest version of Arena](/intl.en-US/Solutions/Deep learning solution/Preparations/Install the latest version of Arena.md).

    **Note:** You need to install Arena only once on each Linux client. To allow each user to use Arena in a separate environment, you must create a kubeconfig file for each user.

2.  Create a kubeconfig file for each user.

    To allow each user to use Arena in a separate environment, you must create a kubeconfig file for each user \(service account\). You can grant users different permissions on the ACK cluster. This ensures data security.

    Log on to the client as the root user, and run the createKubeConfig.sh script to create a kubeconfig file for each user. For more information, see [Appendix](#section_m57_i99_0zo). Run the following commands:

    ```
    # Make the createKubeConfig.sh script executable.
    chmod +x createKubeConfig.sh
    
    # Create a kubeconfig file for Bob in user group dev1.
    ./createKubeConfig.sh bob -n dev1
    
    # Create a kubeconfig file for Tom in user group dev2.
    ./createKubeConfig.sh tom -n dev2
    
    # Place the kubeconfig file of each user in their home directory.
    mkdir -p /home/bob/.kube/ && cp bob.config /home/bob/.kube/config
    mkdir -p /home/tom/.kube/ && cp tom.config /home/tom/.kube/config
    ```

    ![config](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4381205061/p173803.png)

    After you perform the preceding operations, you can log on to the client with the account of Bob or Tom, and then run Arena in a separate environment. After you perform Step 1 and Step 2, [Task 1](#li_s36_5go_gcm) and [Task 2](#li_b62_eoa_lit) are completed.


## Step 3: Grant the users different permissions on Arena

1.  Create required roles in the namespaces of the ACK cluster.

    You can create roles that define different permissions in the namespaces of the ACK cluster. Each role in a namespace contains a set of permission rules. For more information about how to create a role, see [Using RBAC Authorization](https://kubernetes.io/docs/reference/access-authn-authz/rbac/).

    1.  Create role definition files.

        Create roles in dev1 and dev2, and bind the roles to Bob and Tom separately. In this example, grant Bob and Tom the minimum permissions. This allows them to view and manage only their own jobs.

        For more information about how to create a role in dev1, see dev1\_roles.yaml in [Appendix](#section_m57_i99_0zo). For more information about how to create a role in dev2, see dev2\_roles.yaml in [Appendix](#section_m57_i99_0zo).

    2.  After you create dev1\_roles.yaml and dev2\_roles.yaml, run the following commands to deploy the files to the ACK cluster:

        ```
        kubectl apply -f dev1_roles.yaml
        
        kubectl apply -f dev2_roles.yaml
        ```

        ![ROLE](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4381205061/p173962.png)

        Run the following commands to query roles in the namespaces:

        ```
        kubectl get role -n dev1
        kubectl get role -n dev2
        ```

        If the content in the following figure appears, the roles are created.

        ![role](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4381205061/p173966.png)

2.  Grant the users permissions on the ACK cluster.

    After you create roles, you must bind the roles to the users to grant them permissions. To complete this task, bind the roles in the namespaces to the service accounts.

    You can bind one or more roles in different namespaces to each user. This allows a user to access resources that belong to different namespaces. Role-based access control allows you to manage permissions that the users have on namespaces flexibly. You can change role bindings anytime.

    You can use two Kubernetes objects, RoleBinding and ClusterRoleBinding, to create role bindings that grant permissions on namespaces or the ACK cluster. For more information about how to use RoleBinding and ClusterRoleBinding to describe role bindings, see [Using RBAC Authorization](https://kubernetes.io/docs/reference/access-authn-authz/rbac/).

    To complete [Task 3](#li_zu0_17k_nom), bind the roles that are created in [Step 3](#step_9f2_rfl_ruj) to Bob and Tom. To grant permissions to users, perform the following operations:

    1.  Log on to the client as the root user. Run the following commands to bind the roles to Bob and Tom. For more information about bob\_rolebindings.yaml and tom\_rolebindings.yaml, see [Appendix](#section_m57_i99_0zo).

        ```
        kubectl apply -f bob_rolebindings.yaml
        kubectl apply -f tom_rolebindings.yaml
        ```

        ![rolebinding](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4381205061/p173986.png)

    2.  Run the following commands to query the role bindings in dev1 and dev2:

        ```
        kubectl get rolebinding -n dev1
        kubectl get rolebinding -n dev2
        ```

        If the content in the following figure appears, the roles are bound to Bob and Tom.

        ![role created](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4381205061/p173991.png)

        In this case, the first three tasks are completed.


## Step 4: Configure resource quotas for the user groups

ACK provides a console for you to centrally manage all resources of an ACK cluster. To ensure the security and efficiency of resource usage, you can configure resource quotas for the user groups. You can submit jobs to a namespace on which you have permissions. When you submit a job, ACK automatically checks the available resources of the namespace. If the amount of resources requested by a job exceeds the quota, ACK rejects the job.

In Kubernetes, a ResourceQuota object describes constraints that limit aggregate resource consumption per namespace. Each namespace corresponds to a user group on the client where Arena is installed. You can configure quotas for various resources, such as CPU, memory, and extended resources. Extended resources include NVIDIA GPUs. A ResourceQuota object also limits the resource usage of containers and other Kubernetes objects in a namespace. For more information, see [Resource quotas](https://kubernetes.io/docs/concepts/policy/resource-quotas/).

To complete [Task 4](#li_db9_ab1_imr), configure quotas of GPU, CPU, and memory resources for each user group. For more information, see [Allocate resources](#table_xy2_zmb_z48). In this example, allocate one GPU to dev1. The CPU and memory resources are unlimited. Bob in dev1 can use all CPU and memory resources of the cluster. Then, allocate the following resources to dev2: 2 GPUs, 8 CPU cores, and 60 GiB of memory. To configure resource quotas for the user groups, perform the following operations:

1.  Log on to the client as the root user, and run the following commands to configure resource quotas. For more information about dev1\_quota.yaml and dev2\_quota.yaml, see [Appendix](#section_m57_i99_0zo). Run the following command to deploy the files in the ACK cluster:

    ```
    kubectl apply -f dev1_quota.yaml
    kubectl apply -f dev2_quota.yaml
    ```

    After you configure resource quotas, run the following commands to check whether the configurations take effect. You can also check the resource quotas and resource usage.

    ```
    # Query the resource quota in dev1.
    kubectl get resourcequotas -n dev1
    
    # Query the resource quota in dev2.
    kubectl get resourcequotas -n dev2
    
    # Query the resource usage in dev1.
    kubectl describe resourcequotas dev1-compute-resources -n dev1
    
    # Query the resource usage in dev2.
    kubectl describe resourcequotas dev2-compute-resources -n dev2
    ```

    ![quota](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4381205061/p174046.png)

    In this case, computing resources are allocated to the user groups, and [Task 4](#li_db9_ab1_imr) is completed.


## Step 5: Create NAS volumes to enforce multi-level access control

To meet the requirements of multi-level access control, you must create volumes that are accessible to different users and user groups. This ensures the security of data sharing.

To complete [Task 5](#li_9tk_kye_uqc), you must create two types of shared volumes. The first type of volume is used to store data that can be accessed by users in both user groups, and the other type of volume is used to store data that can be accessed by only users in a specific user group. For more information about shared volumes, see [Allocate resources](#table_xy2_zmb_z48). In this example, four volumes are created: dev1-public, dev2-public, department1-public-dev1, and department1-public-dev2. department1-public-dev1 and department1-public-dev2 are mounted to the same directory of the NAS file system. The volume data can be accessed by users in both dev1 and dev2. dev1-public and dev2-public are mounted to different directories of the NAS file system. Data stored on dev1-public can be accessed by only Bob in dev1, and data stored on dev2-public can be accessed by only Tom in dev2. To create NAS volumes for data sharing, perform the following operations:

1.  Create a NAS file system.

    Log on to the [Apsara File Storage NAS console](https://nasnext.console.aliyun.com/overview), create a NAS file system, and add a mount target. For more information, see [Configure a shared NAS volume](/intl.en-US/Solutions/Deep learning solution/Preparations/Configure a shared NAS volume.md).

2.  Create persistent volumes \(PVs\) and persistent volume claims \(PVCs\) for the ACK cluster.

    1.  Create PVs.

        Create four PVs. For more information about how to create a PV, see [Create a PV](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/NAS volumes/Mount a NAS file system as a static PV by using YAML files.md). Volume department1-public-dev1 is used to share data of department1 to users in dev1, and volume department1-public-dev2 is used to share data of department1 to users in dev2. Volume dev1-public is used to share data to only users in dev1, and volume dev2-public is used to share data to only users in dev2. The following figure shows the configurations of the PVs.

        ![PV](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4381205061/p174088.png)

        **Note:** Select the mount target that you added in the preceding step.

    2.  Create PVCs.

        Create a PVC for each newly created PV. For more information about how to create a PVC, see [Create a PVC](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/NAS volumes/Mount a NAS file system as a static PV by using YAML files.md).

        After the PVCs are created, you can find that department1-public-dev1 and dev1-public exist in dev1, and department1-public-dev2 and dev2-public exist in dev2.

3.  Verify the configurations of the volumes.

    Log on to the client as the root user, and run the following commands to query the volumes that are used by user groups dev1 and dev2:

    ```
    # Query the volumes that are used by user group dev1.
    arena data list -n dev1
    
    # Query the volumes that are used by user group dev2.
    arena data list -n dev2
    ```

    ![check](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4381205061/p174112.png)

    After you complete the preceding operations, you have completed all the tasks in this example. The following example describes how to log on to the client where Arena is installed with the accounts of Bob and Tom.


## Step 6: Run Arena with different user accounts

**Use the account of Bob**

1.  Run the following commands to log on to the client and query available shared volumes:

    ```
    # Log on to the client with the account of Bob.
    ssh bob@39.98.xxx.xx
    
    # Run the arena data list command to query shared volumes that are available to Bob.
    arena data list
    ```

    ![result](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5381205061/p174151.png)

2.  Run the following command to submit a training job that requires one GPU:

    ```
    arena submit tf \
            --name=tf-git-bob-01 \
            --gpus=1 \
            --image=tensorflow/tensorflow:1.5.0-devel-gpu \
            --sync-mode=git \
            --sync-source=https://code.aliyun.com/xiaozhou/tensorflow-sample-code.git \
            "python code/tensorflow-sample-code/tfjob/docker/mnist/main.py --max_steps 10000 --data_dir=code/tensorflow-sample-code/data"
    ```

3.  Run the following command to list all the jobs that are submitted by Bob:

    ```
    arena list
    ```

    ![bob](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5381205061/p174177.png)

4.  Run the following command to submit another training job that requires one GPU:

    ```
    arena submit tf \
            --name=tf-git-bob-02 \
            --gpus=1 \
            --image=tensorflow/tensorflow:1.5.0-devel-gpu \
            --sync-mode=git \
            --sync-source=https://code.aliyun.com/xiaozhou/tensorflow-sample-code.git \
            "python code/tensorflow-sample-code/tfjob/docker/mnist/main.py --max_steps 10000 --data_dir=code/tensorflow-sample-code/data"
    ```

    In this example, only one GPU is allocated to user group dev1. Therefore, ACK is expected to reject the second job.

    ![result](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5381205061/p174148.png)

    ![reason](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5381205061/p174150.png)

    The preceding figure shows that the ACK cluster still has sufficient resources. However, the GPU allocated to user group to which Bob belongs is already occupied by the first job. As a result, the second job is suspended.


**Use the account of Tom**

1.  Run the following commands to log on to the client and query available shared volumes:

    ```
    # Log on to the client with the account of Tom.
    ssh tom@39.98.xxx.xx
    
    # Run the following command to query shared volumes that are available to Tom.
    arena data list
    ```

    ![tom job](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5381205061/p174157.png)

2.  Run the following command to list all the jobs that are submitted by Tom:

    ```
    arena list
    ```

    Tom cannot view the jobs that are submitted by Bob.

    ![list](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5381205061/p174176.png)

3.  Run the following command to submit a job that requires one GPU:

    ```
     arena submit tf \
             --name=tf-git-tom-01 \
             --gpus=1 \
             --chief-cpu=2 \
             --chief-memory=10Gi \
             --image=tensorflow/tensorflow:1.5.0-devel-gpu \
             --sync-mode=git \
             --sync-source=https://code.aliyun.com/xiaozhou/tensorflow-sample-code.git \
             "python code/tensorflow-sample-code/tfjob/docker/mnist/main.py --max_steps 10000 --data_dir=code/tensorflow-sample-code/data"
    ```

    **Note:** In this example, GPU, CPU, and memory resources are allocated to user group dev2. Therefore, Tom must specify the resources requested by the job to be submitted.

4.  Run the following command to submit another job that requires one GPU:

    ```
     arena submit tf \
             --name=tf-git-tom-02 \
             --gpus=1 \
             --chief-cpu=2 \
             --chief-memory=10Gi \
             --image=tensorflow/tensorflow:1.5.0-devel-gpu \
             --sync-mode=git \
             --sync-source=https://code.aliyun.com/xiaozhou/tensorflow-sample-code.git \
             "python code/tensorflow-sample-code/tfjob/docker/mnist/main.py --max_steps 10000 --data_dir=code/tensorflow-sample-code/data"
    ```

5.  Run the following command to list all the jobs that are submitted by Bob:

    ```
    arena list
    ```

    ![result](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5381205061/p174181.png)


## Result

The preceding results indicate that you can log on to the client and run Arena in a separate environment with the accounts of Bob and Tom. You can also query and use compute and storage resources that are allocated to each user group, and manage jobs that are submitted by Bob and Tom.

## Appendix

Run the following command to download [tools.tar.gz](https://lumo-package.oss-cn-beijing.aliyuncs.com/tools.tar.gz):

```
wget https://lumo-package.oss-cn-beijing.aliyuncs.com/tools.tar.gz
```

