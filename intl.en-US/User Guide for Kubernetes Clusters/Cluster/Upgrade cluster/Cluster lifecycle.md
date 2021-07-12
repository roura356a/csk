---
keyword: [Cluster lifecycle, Status changes]
---

# Cluster lifecycle

This topic describes the definitions of different cluster states and how the state of a cluster changes within a cluster lifecycle.

|State|Description|
|-----|-----------|
|Initializing|The cluster is applying for corresponding cloud resources.|
|Creation Failed|The cluster fails to apply for corresponding cloud resources.|
|Running|The cluster has successfully applied for corresponding cloud resources.|
|Updating|The cluster is updating the metadata.|
|Scaling|The cluster is scaling in or out.|
|Removing|One or more nodes are being removed from the cluster.|
|Upgrading|The cluster is upgrading.|
|Deleting|The cluster is being deleted.|
|Deletion Failed|The cluster fails to be deleted.|
|Deleted \(invisible to users\)|The cluster is deleted.|

![Cluster status changes](../images/p4752.png "Cluster status changes")

