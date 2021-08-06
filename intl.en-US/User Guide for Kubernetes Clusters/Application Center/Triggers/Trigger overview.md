---
keyword: [trigger overview, use scenarios of triggers, usage notes of triggers]
---

# Trigger overview

A trigger is a webhook that you can create for your applications in Application Center. You can deploy applications created in Application Center to a Container Service for Kubernetes \(ACK\) cluster by using triggers in a third-party environment.

## Prerequisites

Triggers can be used to update and redeploy applications that are created in Application Center. An application must be created in Application Center before you can use triggers.

## Scenarios

Triggers are applicable in the following scenarios:

-   Use triggers in a third-party environment to redeploy applications that are deployed in ACK clusters by Application Center.
-   Use triggers to automatically connect Application Center to existing continuous integrate \(CI\) systems, such as the Container Registry delivery pipeline, the Jenkins CI pipeline, or the GitLab CI pipeline. This forms a complete integration and continuous delivery \(CI/CD\) pipeline.

## Considerations

Keep your triggers confidential. If a trigger is leaked, you must delete the trigger and create a new one.

**Related topics**  


[t2068310.md\#]()

[t2068340.md\#]()

