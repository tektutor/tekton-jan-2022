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
- For each version of your application deployed there would be one ReplicaSet
- Rolling update
    - upgrading your application from one version to the other without any downtime
    - when things go wrong, even we can revert by rolling back to older stable version of your application without downtime

### Controller Manager
1. Node Controller
2. Deployment Controller
    - whenever a new Deployment entry is created in etcd stores it receives an event
    - once it receives the new deployment configuration details from the event, it will ReplicaSet which is then updated by API Server
4. Replication Controller
5. ReplicaSet Controller
     - Whenever a new ReplicaSet entry is created in etc data store it receives an event
     - once it receive the new replicaset configuration details from the event, it will create so many pod definition which is then updated in the etc data store by the API Server
7. Endpoint Controller

Scheduler 
  - receives an event whenever any new Pod definition gets created in the etcd 
  - it then identifies an health node where the new Pods can be deployed
  - Scheduler then intimates this to API Server on where each of those Pods can be scheduled
  - API server updates the Pod definition with node details suggested by the Scheduler component
  
kubelet
- When the Pods are scheduled to a particular node, the respective Node Agent(kubelet), they get an event notification.
- kubelet then understands the image details required to created the containers on the current node, accordingly pulls the images
  from respective image registry 
- kubelet then creates the containers, monitors the health and constantly keeps reporting the status of all the containers running
  on that node to the API Server like a heart-beat update
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

