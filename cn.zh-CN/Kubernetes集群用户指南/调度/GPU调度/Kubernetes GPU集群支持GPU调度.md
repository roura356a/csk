---
keyword: [GPU调度, Kubernetes GPU集群]
---

# Kubernetes GPU集群支持GPU调度

本文通过运行TensorFlow的GPU实验环境为您介绍支持GPU调度的Kubernetes GPU集群。

## 运行TensorFlow的GPU实验环境

数据科学家通常习惯使用Jupyter作为TensorFlow实验环境，本文用一个例子向您展示如何快速部署一个Jupyter应用。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在左侧导航栏选择**工作负载** \> **无状态**。

5.  在**无状态**页面右上角单击**使用模板创建**。

6.  选择所需的集群，命名空间，选择样例模板或自定义，然后单击**创建**。

    ![创建应用](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7775659951/p10866.png)

    本例中，示例模板是一个Jupyter应用，包括一个Deployment和Service。

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
                nvidia.com/gpu: 1          #指定调用nvidia gpu的数量
            ports:
            - containerPort: 8888
              hostPort: 8888
            env:
              - name: PASSWORD                # 指定访问Jupyter服务的密码，您可以按照您的需要修改
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
      type: LoadBalancer                           #阿里云的负载均衡访问内部服务和负载均衡
    
                            
    ```

    旧的GPU部署方案，您必须要定义以下的nvidia驱动所在的数据卷。

    ```
    volumes:
        - hostPath:
            path: /usr/lib/nvidia-375/bin
            name: bin
        - hostPath:
            path: /usr/lib/nvidia-375
            name: lib
    ```

    这需要您在编写部署文件时，强依赖于所在的集群，导致缺乏可移植性。但是在Kubernetes 1.9.3及之后的版本中，无需指定hostPath，nvidia的插件会自发现驱动所需的库链接和执行文件。

7.  在左侧导航栏选择**服务与路由** \> **服务**，选择所需的集群和命名空间，选择tf-notebook服务，查看外部端点。

    ![查看服务](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7775659951/p10867.png)

8.  在浏览器中访问Jupyter实例，访问地址是`http://外部端点`，输入模板中配置的密码。

9.  您可通过如下的程序，验证这个Jupyter实例可以使用GPU。它将列出Tensorflow可用的所有设备。

    ```
    from tensorflow.python.client import device_lib
    
    def get_available_devices():
        local_device_protos = device_lib.list_local_devices()
        return [x.name for x in local_device_protos]
    
    print(get_available_devices())
    ```

    ![查看结果](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7775659951/p10868.png)


