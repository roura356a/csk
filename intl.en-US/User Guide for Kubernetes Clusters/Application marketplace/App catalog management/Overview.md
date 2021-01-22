# Overview

App Catalog of Container Service for Kubernetes \(ACK\) is integrated with Helm. App Catalog allows you to use features provided by Helm and also supports new features such as graphical user interfaces and Alibaba Cloud repositories.

Microservice is the main component of containerization. However, it is challenging to deploy and manage applications as microservices. An application can be divided into several microservices. These microservices can be separately deployed and expanded to achieve agile development and fast iteration. Microservices have many benefits. However, a large number of microservices pose challenges to developers who need to manage microservices, including resources, versions, and configurations.

ACK integrates Helm to simplify how you can deploy and manage Kubernetes applications as microservices on the Kubernetes orchestration platform.

Helm is an open source subproject for Kubernetes service orchestration. It functions as a package manager, which is used to package applications into versioned archives. Packaged Kubernetes applications are much easier to deploy and manage.

## Features of App Catalog

The chart list on the App Catalog page provides the following information:

-   Chart name: Each Helm chart corresponds to an application. The chart contains the image, dependencies, and resource definitions that are required to run the application.
-   Version: The version of a chart.
-   Repository: The repository that is used to publish and store charts. Repositories provided by Helm are stable and incubator.

The information displayed on the details page of each chart varies. It may contain the following items:

-   Chart introduction
-   Chart details
-   Prerequisites for installing a chart to an ACK cluster. For example, you must first configure a persistent volume \(PV\).
-   Chart installation commands
-   Chart uninstallation commands
-   Chart parameters

You can use Helm to deploy and manage charts in App Catalog. For more information, see [Simplify Kubernetes application deployment by using Helm](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Simplify Kubernetes application deployment by using Helm.md).

## Disclaimer

Some programs in App Catalog are developed from open source programs to better fit ACK. Alibaba Cloud provides comprehensive technical support for these programs in App Catalog. However, Alibaba Cloud is not responsible for any compensation, reimbursement, or damages arising in connection with the defects of the open source programs that you use.

