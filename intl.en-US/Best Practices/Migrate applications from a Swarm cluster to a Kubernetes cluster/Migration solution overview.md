# Migration solution overview {#concept_303730 .concept}

This topic describes a solution used to migrate applications from a Swarm cluster to a Kubernetes cluster without causing any disruption to the applications. The solution contains seven steps in which different personnel are involved.

## Procedure {#section_emg_yyr_26o .section}

1.  Standardize the target Swarm cluster.

    **Note:** Operations described in this step must be performed by the O&M personnel.

    1.  Configure the SLB instance for the target Swarm cluster and then use a client to access an application that runs on the target Swarm cluster.

        **Note:** This step ensures that you can use the canary deployment to direct the traffic destined for the Swarm cluster to the target Kubernetes cluster.

        -   If you use the SLB instance to access the application, you can check the traffic, or roll back the migrated application workloads when an exception occur.
        -   If you use any other method to access the application, any newly released features of the application within 48 hours cannot take effect in real time.
    2.  Deploy CloudMonitor on the target Swarm cluster to monitor the ECS instances used by the Swarm cluster.
2.  Create and configure a Kubernetes cluster.

    **Note:** Operations described in this step must be performed by the O&M personnel.

    1.  Create a Kubernetes cluster.

        **Note:** We recommend that you create a managed Kubernetes cluster.

    2.  Migrate ECS instances and network used by the Swam cluster.
    3.  Migrate node tags.
    4.  Verify that the VPC that you set for the Kubernetes cluster can provide reliable connectivity.
    5.  Migrate volumes.
    6.  Migrate ConfigMaps.
3.  Use Kompose to migrate applications from the Swarm cluster to the Kubernetes cluster.

    **Note:** Operations described in this step must be performed by a developer.

    1.  Install Kompose and kubectl to prepare an environment to implement the migration task.
    2.  Modify the Swarm orchestration file of the application.
    3.  Convert theSwarm orchestration file to a Kubernetes resource file.
    4.  Deploy the Kubernetes resource file.
    5.  Manually migrate Swarm tags.
    6.  Debug the application migrated to the Kubernetes cluster.
    7.  Migrate the log configurations of the application.
4.  Implement regression testing for the migrated application.

    **Note:** Operations described in this step must be performed by the testing personnel.

    1.  Set a testing domain name for the application.
    2.  Test the services provided by the application.
    3.  Verify that the application logs can be collected.
    4.  Verify that the application can be monitored by monitoring products of Alibaba Cloud.
5.  Use the canary deployment method to direct traffic destined for the Swarm cluster to the Kubernetes cluster.

    **Note:** Operations described in this step must be performed by the O&M personnel.

    1.  Create a service of the NodePort type in the Kubernetes cluster.
    2.  Direct the traffic destined for the Kubernetes cluster back to the Swarm cluster.

        **Note:** If the Kubernetes cluster cannot work normally, you must perform this step.

6.  Direct traffic to the Kubernetes cluster.

    **Note:** Operations described in this step must be performed by the O&M personnel.

    To complete this task, perform one of the following operations:

    -   Modify the DNS configurations.
    -   Upgrade the code or configurations of the client.
7.  Delete the Swarm cluster and release its resources.

    **Note:** Operations described in this step must be performed by the O&M personnel.

    1.  Verify that no traffic is destined for the Swarm cluster.
    2.  Delete the Swarm cluster and release its resources.

