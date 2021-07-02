---
keyword: [GPU scheduling, ACK cluster with GPU-accelerated nodes]
---

# Use GPU scheduling for ACK clusters

This topic describes GPU scheduling for Container Service for Kubernetes \(ACK\) clusters with GPU-accelerated nodes by setting up a GPU-accelerated environment to run TensorFlow.

## Set up a GPU-accelerated environment to run TensorFlow

In most cases, data scientists use Jupyter to set up an environment for TensorFlow. The following example shows how to deploy a Jupyter application.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  On the **Deployments** page, click **Create from YAML** in the upper-right corner.

6.  Select the required cluster and namespace. Select a sample template, or set Sample Template to Custom and customize the template in the Template field. Then, click **Create** to create the application.

    ![Create a Deployment](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1935359951/p10866.png)

    In this example, the template for the Jupyter application contains a Deployment and a Service.

    ```
    ---
    # Define the tensorflow deployment
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: tf-notebook
      labels:
        app: tf-notebook
    spec:
      replicas: 1
      selector: # define how the deployment finds the pods it mangages
        matchLabels:
          app: tf-notebook
      template: # define the pods specifications
        metadata:
          labels:
            app: tf-notebook
        spec:
          containers:
          - name: tf-notebook
            image: tensorflow/tensorflow:1.4.1-gpu-py3
            resources:
              limits:
                nvidia.com/gpu: 1          #Specify the number of NVIDIA GPUs that are scheduled.
            ports:
            - containerPort: 8888
              hostPort: 8888
            env:
              - name: PASSWORD                #The password that is used to access the Jupyter application. You can modify the password as needed.
                value: mypassword
    
    # Define the tensorflow service
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: tf-notebook
    spec:
      ports:
      - port: 80
        targetPort: 8888
        name: jupyter
      selector:
        app: tf-notebook
      type: LoadBalancer                           #A Server Load Balancer (SLB) instance is used to route internal traffic and perform load balancing.
    
                            
    ```

    If you use a GPU scheduling solution provided by Kubernetes earlier than V1.9.3, you must define the volumes where the NVIDIA drivers are located.

    ```
    volumes:
        - hostPath:
            path: /usr/lib/nvidia-375/bin
            name: bin
        - hostPath:
            path: /usr/lib/nvidia-375
            name: lib
    ```

    This solution requires you to manually modify the template if you want to schedule the GPUs to other clusters. However, in Kubernetes 1.9.3 and later, you do not need to set hostPath. The NVIDIA plug-in automatically identifies the links of the libraries and executable files that are required by NVIDIA drivers.

7.  In the left-side navigation pane of the details page, choose **Network** \> **Services**. Select the required cluster and namespace, find the tf-notebook Service, and then check its external endpoint.

    ![View the Service](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1935359951/p10867.png)

8.  To connect to the Jupyter application, enter `http://EXTERNAL-IP` into the address bar of your browser and enter the password specified in the template.

9.  You can run the following program to verify that the Jupyter application is allowed to use GPU resources. The program lists all devices that can be used by TensorFlow:

    ```
    from tensorflow.python.client import device_lib
    
    def get_available_devices():
        local_device_protos = device_lib.list_local_devices()
        return [x.name for x in local_device_protos]
    
    print(get_available_devices())
    ```

    ![Check the result](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1935359951/p10868.png)


