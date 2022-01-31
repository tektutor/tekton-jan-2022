# Kubernetes Commands

### Docker
 - IP addresses are assigned on the Container level
 - Each container has its own network stack

### Pod
- a group of related containers
- IP addresses are assigned on the Pod level not on the container level
- in other all the containers within the same POD shares the network and ports
- application runs within the container which is part of the Pod

### ReplicaSet
- Replicaset manage Pods
- monitors the health of Pod constantly and it it identifies any Pod in a non-responsive status it will be replaced with 
  a healthy new Pod
- supports
    scaling up - creating multiple instances of the same application to improve overall performance of your application
    scaling down - discard unused pods ( deletes idle pods when the traffic to your website goes down )

### Deployment
- is a Kubernetes abstraction that represents your application
- the definition of deployment that resides in the etcd datastore maintained by API Server on the master node
- each deployment has one or ReplicaSets under them
- Deployment manages ReplicaSet
- Rolling update
    - upgrading your application from one version to the other without any downtime
    - when things go wrong, even we can revert by rolling back to older stable version of your application without downtime

### Deploying nginx in K8s cluster
```
kubectl create deploy nginx --image=nginx:1.18
```
You may now check if the deployment is created 
```
kubectl get deploy
```
You can see the replicasets created as shown below
```
kubectl get rs
```
You can see the pods as shown below
```
kubectl get po
```

