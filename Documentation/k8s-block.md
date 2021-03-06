# Block Storage Quickstart

Block storage allows you to mount storage to a single pod. 

### Prerequisites

This guide assumes you have created a Rook cluster as explained in the main [Kubernetes guide](kubernetes.md)

### Provision Storage
Before Rook can start provisioning storage, a StorageClass and its storage pool need to be created. This is needed for Kubernetes to interoperate with Rook for provisioning persistent volumes. The [rook-storageclass.yaml](/demo/kubernetes/rook-storageclass.yaml) sample will create the storage pool automatically. For more options on pools, see the documentation on [creating storage pools](pool-tpr.md).

```bash
kubectl create -f rook-storageclass.yaml
```

### Consume the storage

We create a sample app to consume the block storage provisioned by Rook with the classic wordpress and mysql apps.
Both of these apps will make use of block volumes provisioned by Rook.

Start mysql and wordpress from the `demo/kubernetes` folder:

```bash
kubectl create -f mysql.yaml
kubectl create -f wordpress.yaml
```

Both of these apps create a block volume and mount it to their respective pod. You can see the Kubernetes volume claims by running the following:

```bash
$ kubectl get pvc
NAME             STATUS    VOLUME                                     CAPACITY   ACCESSMODES   AGE
mysql-pv-claim   Bound     pvc-95402dbc-efc0-11e6-bc9a-0cc47a3459ee   20Gi       RWO           1m
wp-pv-claim      Bound     pvc-39e43169-efc1-11e6-bc9a-0cc47a3459ee   20Gi       RWO           1m
```

Once the wordpress and mysql pods are in the `Running` state, get the cluster IP of the wordpress app and enter it in your brower:

```bash
$ kubectl get svc wordpress
NAME        CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
wordpress   10.3.0.155   <pending>     80:30841/TCP   2m
```

You should see the wordpress app running.  

**NOTE:** When running in a vagrant environment, there will be no external IP address to reach wordpress with.  You will only be able to reach wordpress via the `CLUSTER-IP` from inside the Kubernetes cluster.

### Teardown
To clean up all the artifacts created by the block demo:
```
kubectl delete -f wordpress.yaml
kubectl delete -f mysql.yaml
kubectl delete -n rook pool replicapool
kubectl delete storageclass rook-block
```
