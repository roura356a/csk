# Configure a Kubernetes GPU cluster to support GPU scheduling

This topic describes how to create a GPU experimental environment to run TensorFlow and support GPU scheduling.

## Create a GPU experimental environment to run TensorFLow

Jupyter is a standard tool that is used by data scientists to create the experimental environment to run TensorFlow. The following example shows how to deploy a Jupyter application.

1.  Log on to the Container Service for Kubernetes \(ACK\) console. In the left-side navigation pane, choose **Applications** \> **Deployments** to go to the Deployments page.

2.  In the upper-right corner of the page, click **Create from Template**.

3.  Select the target cluster and namespace. Select a sample template, or set Sample Template to Custom and customize the template in the Template field. Then, you can click **Create**.

    ![Create a deployment](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1935359951/p10866.png)

    In this example, a Jupyter application template is implemented. The template includes a deployment and a service.

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
      selector: #Specifies how the deployment finds the managed pods
        matchLabels:
          app: tf-notebook
      template: #Defines the pods specifications
        metadata:
          labels:
            app: tf-notebook
        spec:
          containers:
          - name: tf-notebook
            image: tensorflow/tensorflow:1.4.1-gpu-py3
            resources:
              limits:
                nvidia.com/gpu: 1          #Specifies the number of NVIDIA GPUs that are called by the application.
            ports:
            - containerPort: 8888
              hostPort: 8888
            env:
              - name: PASSWORD                #Specifies the password used to access the Jupyter instance. You can modify the password as required.
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
      type: LoadBalancer                           #Creates a Server Load Balancer (SLB) service to ensure that the Jupyter instance is accessible over the Internet.
    
    						
    ```

    If you use a GPU deployment solution of Kubernetes earlier than version 1.9.3, you must define the following volumes where the NVIDIA drivers are located:

    ```
    volumes:
        - hostPath:
            path: /usr/lib/nvidia-375/bin
            name: bin
        - hostPath:
            path: /usr/lib/nvidia-375
            name: lib
    ```

    If you implement your deployment template in a cluster by using the GPU deployment solution of Kubernetes earlier than 1.9.3, your template is based on the cluster. As a result, the template is no longer portable. However, in Kubernetes version 1.9.3 and later, you do not need to set the hostPath field. The NVIDIA plug-ins automatically identify the library links and execution files that are required by the drivers.

4.  In the left-side navigation pane of the ACK console, choose **Ingresses and Load Balancing** \> **Services**. Select the target cluster and namespace. Find the tf-notebook service and check its external endpoint.

    ![Check the external endpoint of the tf-notebook service](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1935359951/p10867.png)

5.  To connect to the Jupyter instance in a browser, enter `http://EXTERNAL-IP` in the address bar and enter the password specified in the template.

6.  You can run the following program to verify that the Jupyter instance has access to GPU-based devices. The program lists all devices that can be used by TensorFlow:

    ```
    from tensorflow.python.client import device_lib
    
    def get_available_devices():
        local_device_protos = device_lib.list_local_devices()
        return [x.name for x in local_device_protos]
    
    print(get_available_devices())
    ```

    ![Check available GPU-based devices](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1935359951/p10868.png)


