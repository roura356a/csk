# Overview {#task_1443746 .task}

This topic describes the deep learning solution provided by TensorFlow.

Based on the powerful computing capabilities of Alibaba Cloud, the deep learning solution provides you with an easy-to-use, open source, and end-to-end deep learning service platform. The service platform allows data scientists and algorithm engineers to quickly use Alibaba Cloud resources to perform data preparation, model development, model training, evaluation, and prediction tasks. These cloud resources include Elastic Compute Service \(ECS\), Elastic GPU Service \(GPU\), E-HPC, Apsara File Storage NAS, E-MapReduce, and Server Load Balancer \(SLB\). The service platform can also easily transform deep learning capabilities into service APIs to accelerate integration with business applications.

TensorFlow is the most popular deep learning framework in the industry. However, it is not easy to apply TensorFlow to the production environment. When you use TensorFlow, you may face the following challenges: resource isolation, application scheduling and deployment, GPU resource allocation, and training lifecycle management. Especially in large-scale distributed training scenarios, managing TensorFlow by manual deployment and human O&M cannot meet the efficiency requirements. Before starting each module of a distributed cluster, you must specify the cluster specification.

The Alibaba Cloud Kubernetes deep learning solution effectively tackles the challenges in terms of load balancing, auto scaling, high availability, and rolling upgrade. The deep learning solution leverages the built-in automation capabilities of Kubernetes to significantly save the cost of operations and maintenance for TensorFlow applications.

To be specific, the deep learning solution has the following benefits:

-   Easy to use: simplifies the way to build and manage deep learning platforms.
-   High efficiency: improves the efficiency to use heterogeneous computing resources such as CPU and GPU clusters, providing a unified user experience.
-   End-to-end: provides best practices for building end-to-end deep learning tasks and processes based on the powerful service system of Alibaba Cloud.
-   Service-oriented: supports service-oriented deep learning capabilities and easy integration with cloud applications.

## Start to use {#section_d6g_m9a_yw5 .section}

1.  Prepare the environment. 
    -   [Create a gn5 Kubernetes cluster](reseller.en-US/solution/Deep learning solutions/Deep learning solutions based on Helm charts/Create a gn5 Kubernetes cluster.md#).
    -   [Create an Apsara File Storage NAS data volume](reseller.en-US/solution/Deep learning solutions/Deep learning solutions based on Helm charts/Create an Apsara File Storage NAS data volume.md#). The Apsara File Storage NAS volume is used in this example.
2.  Develop Tensorflow models.
3.  Follow the steps described in [Train TensorFlow distributed models](reseller.en-US/solution/Deep learning solutions/Deep learning solutions based on Helm charts/Train TensorFlow distributed models.md#) to export the model.
4.  Use the exported model to [predict the TensorFlow model](reseller.en-US/solution/Deep learning solutions/Deep learning solutions based on Helm charts/Predict TensorFlow models.md#).

