# Upgrade Helm manually {#concept_f4p_nl4_d2b .concept}

Log on to the master node of the Kubernetes cluster, see [Connect to a Kubernetes cluster by using kubectl](../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#).

Execute the following command:

```
helm init --tiller-image registry.cn-hangzhou.aliyuncs.com/acs/tiller:v2.9.1 --upgrade
```

The image address can use the VPC domain name of the region corresponding to the image. For example, the image address of a machine in the Hangzhou region can be replaced by registry-vpc.cn-hangzhou.aliyuncs.com/acs/tiller:v2.9.1.

Wait for tiller passing through health check. Then you can execute`helm version` to view the upgraded version.

**Note:** Only the Helm server version is upgraded here. To use the Helm client, download the corresponding client binary.

Helm 2.9.1 client download address: [https://github.com/kubernetes/helm/releases/tag/v2.9.1](https://github.com/kubernetes/helm/releases/tag/v2.9.1). Currently, the latest version of Helm supported by Alibaba Cloud is 2. 9.1.

After the Helm client and server are both upgraded, you can see the following information by executing the helm version:

```
#helm version
Client: &version.Version{SemVer:"v2.9.1", GitCommit:"a80231648a1473929271764b920a8e346f6de844", GitTreeState:"clean"  }
Server: &version.Version{SemVer:"v2.9.1", GitCommit:"a80231648a1473929271764b920a8e346f6de844", GitTreeState:"clean   "}
```

