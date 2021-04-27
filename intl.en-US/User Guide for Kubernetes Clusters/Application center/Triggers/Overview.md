---
keyword: [triggers, overview, Kubernetes, ACK]
---

# Overview

A trigger is a webhook that you can create for your applications in Application Center. You can deploy applications created in Application Center to an ACK cluster by using triggers in a third party environment.

## Prerequisites

Triggers can be used to update or redeploy applications created in Application Center. You must create applications in Application Center before you can use triggers.

## Scenarios

Triggers are applicable in the following scenarios:

-   Use triggers in a third party environment to redeploy applications that have already been deployed in ACK clusters by Application Center.
-   Use triggers to automatically connect Application Center to existing continuous integrate \(CI\) systems, such as a Container Registry delivery pipeline, Jenkins, and GitLab CI/CD. This forms a complete CI/CD pipeline.

## Precautions

Keep your triggers safe. If a trigger is leaked, you must delete the trigger and create a new one.

## Related topics

-   [Manage triggers](/intl.en-US/User Guide for Kubernetes Clusters/Application center/Triggers/Manage triggers.md)
-   [Use triggers](/intl.en-US/User Guide for Kubernetes Clusters/Application center/Triggers/Use triggers.md)

