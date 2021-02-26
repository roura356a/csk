# Announcement about ACK errors that are caused by exceptions of ECS OpenAPI on February 23, 2021

## Background information

Due to the exceptions that occurred to ECS OpenAPI on February 23, 2021, IP addresses allocated to pods that were created on this day may be invalid. As a result, the pods may become inaccessible. Only ACK clusters that run Terway in exclusive ENI mode or inclusive ENI mode are affected. We recommend that you perform the following steps to check for pods that have this issue and then fix the issue:

## Procedure

**Step 1: Run the script to scan nodes**

Run the following script on each node:

```
#! /bin/bash
set -e
err(){
    echo "error at line $1"
}
trap 'err $LINENO' ERR
check(){
    cid=$1
    pid=$(docker inspect $cid -f '{{.State.Pid}}')
    if [ -z "$pid" ]; then
        echo 'cannot get pid from container $cid'
        return 1
    fi
    nsenter -t $pid -n curl -s --connect-timeout 4 100.100.100.200 -o /dev/null
}
for line in $(docker ps|grep -v k8s_POD|awk '$NF~/^k8s_/{print $1"_"$NF}'|awk -F_ '{print $1"_"$3"_"$4"_"$5}')
do
    IFS=_ read cid cname pod namespace <<< $line
    if ! check $cid; then
        echo "pod $namespace/$pod has connectivity issues"
    fi
    if [[ "$cname" == "terway" && "$namespace" == "kube-system"  && "$pod" =~ ^(terway-|terway-eniip-|terway-eni-) ]]; then
        terway_container=$cid
    fi
done
if [ -n "$terway_container" ]; then
   for pod in $(docker exec -it $terway_container terway-cli mapping|sed -r "s/\x1B\[([0-9]{1,3}(;[0-9]{1,2})?)?[ mGK]//g"|awk '$3=="X"{print $1}')
   do
        echo "pod $pod on this host has connectivity issues"
   done
fi
```

Possible output:

```
pod *** has connectivity issues
```

If the preceding output is returned, it indicates that the pod may be assigned an invalid IP address.

**Step 2: Recreate the pods that cannot connect to the network**

1.  Recreate the pods on nodes where Terway is installed.

    ```
    kubectl -n kube-system delete pod -l app=terway 
    kubectl -n kube-system delete pod -l app=terway-eniip
    kubectl -n kube-system delete pod -l app=terway-eni
    ```

2.  Recreate the pods that cannot connect to the network.
    -   If the pod is created from a Deployment or DaemonSet, you can directly delete the pod. Then, the system recreates the pod.
    -   If the pod is manually created, you must delete the pod and recreate it.

**Note:** The preceding script is executed to reload the Terway plug-in on nodes where the pods cannot connect to the network. After the script is executed, you can perform the operations in **Step 1: Run the script to scan nodes** again to check whether the cluster works as expected. If the issue is still not solved, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

