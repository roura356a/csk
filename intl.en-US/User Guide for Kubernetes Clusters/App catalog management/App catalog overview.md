# App catalog overview {#concept_jtc_vc4_vdb .concept}

Microservice is the theme of container era. The application microservice brings great challenge to the deployment and management. By dividing a large single application into several microservices, the microservice can be independently deployed and extended so as to realize the agile development and fast iteration. Microservice brings great benefits to us. However, developers have to face the management issues of the microservices, such as the resource management, version management, and configuration management. The number of microservices is large because an application is divided into many components that correspond to many microservices.

For the microservice management issues under Kubernetes orchestration, Alibaba Cloud Container Service introduces and integrates with the Helm open-source project to help simplify the deployment and management of Kubernetes applications.

Helm is an open-source subproject in the Kubernetes service orchestration field and a package management tool for Kubernetes applications. Helm supports managing and controlling the published versions in the form of packaging softwares, which simplifies the complexity of deploying and managing Kubernetes applications.

## Alibaba Cloud app catalog feature {#section_bxc_fd4_vdb .section}

Alibaba Cloud Container Service app catalog feature integrates with Helm, provides the Helm-related features, and extends the features, such as providing graphic interface and Alibaba Cloud official repository.

The chart list on the App Catalog page includes the following information:

-   Chart name: A Helm package corresponding to an application, which contains the image, dependencies, and resource definition required to run an application.
-   Version: The version of the chart.
-   Repository: The repository used to publish and store charts, such as the official repository stable and incubator.

The information displayed on the details page of each chart may be different and include the following items:

-   Chart introduction
-   Chart details
-   Prerequisites for installing chart to the cluster, such as pre-configuring the persistent storage volumes \(pv\)
-   Chart installation commands
-   Chart uninstallation commands
-   Chart parameter configurations

Currently, you can deploy and manage the charts in the app catalog by using the Helm tool. For more information, see [Simplify Kubernetes application deployment by using Helm](reseller.en-US/User Guide for Kubernetes Clusters/Application management/Simplify Kubernetes application deployment by using Helm.md#).

