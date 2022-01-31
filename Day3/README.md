### What is a K8s Custom Resource?
 - Kubernetes allows us to extend the K8s APIs by creating Custom Resources
 - Just like Pod, ReplicaSet and Deployments exist in Kubernetes we could create our own user-defined Custom Resources
   that can be managed using kubectl or oc in openshift

### What is a K8s Custom Resource Definition?
 - In order to create a Custom Resource, we need to write Custom Resource Definitions in a YAML/JSON as declarative code

### Why we need Custom Controllers to manage Custom Resources?
 - As Custom Resources(CR) are user-defined built-in Controllers doesn't know how to manage CRs 
 - hence we need to write our very own Controllers to manage them so that we can manange them via 
   kubectl or oc client tools

### What is a Kubernetes Operator?
Kubernetes Operator = Custom Resource + Custom Controller

Stateless applications in Kubernetes are deployed, monitored and healed automatically as these applications doesn't have to deal with states i.e data.

Stateful applications are associated with a data that are read, updated, deleted, etc.,
When a cluster of such applications are deployed into Kubernetes, it isn't possible for Kubernetes to automatically
heal that when one of more replicas of the instances becomes unresponsive.  Hence, generally an expert who knows about the application better manages that.

For example: 
   Deploying a Cluster of mongodb databases into K8s/Openshift, it isn't as straight forward as a stateless application.  
   Updating data on one instance of mongodb instance has to automaticallly synchronize data on all other mongodb
   instances.

   If one instance of mongodb aborts, it should be replaced by another new golden instance of mongodb.

All these stuffs, requires a manual expert knowledge. May be Database Administrator who is an in MongoDB administrations knows this pretty well but Kubernetes can't automate all this by itself.  

Kubernetes Operators - automate all these operations that a System Administrator or SRE or DevOps or a DBA will do by writing Custom Resource Definitions to create Custom Resource.  Custom Controller then manages the Custom Resource.
i.e Use software to automate your software application management

### What is Operator Framework?
Developing Kubernetes Operator involves writing lot of code, typically in Go programming language.  RedHat Operator SDK makes this process easier by generating repetitive common boiler plate code, allowing us to focus on our specific requirement. 

### What is Operator Lifecycle Manager (OLM)?
 - is an Operator that installs, manages and upgrades other Operators.

### What is Operator Metering?
 - a metrics system that accounts for Operators' use of cluster resources.

### Creating a CRD 
```
cd tekton-jan-2022
git pull
cd Day3/declarative-scripts
kubectl create -f etcd-operator-crd.yaml
```

Listing the crd
```
kubectl get crd
```

### Definig Operator Service Account
```
cd tekton-jan-2022
git pull
cd Day3/declarative-scripts
kubectl create -f etcd-operator-sa.yaml
```

Listing the service accounts
```
kubectl get serviceaccounts
```

### Creating a role
```
kubectl create -f etcd-operator-role.yaml
```

### Creating a role-binding
```
kubectl create -f etcd-operator-rolebinding.yaml
```

### Deploying the etcd operator
```
kubectl create -f etcd-operator-deployment.yaml
```

Listing the deployment
```
kubectl get deployments
```

Listing the pods
```
kubectl get pods
```


### Creating an etcd cluster
```
kubectl create -f etcd-cluster-cr.yaml
```

Watch the pod status
```
kubectl get po -w
```

Find more details about the cluster
```
kubectl describe etcdcluster/example-etcd-cluster
```

List the services
```
kubectl get services --selector etcd_cluster=example-etcd-cluster
```

Testing if we can connect to the etcd
```
kubectl run --rm -i --tty etcdctl --image quay.io/coreos/etcd --restart=Never -- /bin/sh
```
In the container's shell
```
export ETCDCTL_API=3
export ETCDCSVC=http://example-etcd-cluster-client:2379
etcdctl --endpoints $ETCDCSVC put foo bar
etcdctl --endpoints $ETCDCSVC get foo

### Scaling the etcd cluster
Update the size form 3 to 4 and apply the changes as shown below
```
kubectl apply -f etcd-cluster-cr.yaml
```

List the pods
```
kubectl get pods
```

### Failure and Automated Recovery
Watch the pods from another terminal
```
kubectl get pods -w
```

Delete one of your etcd pod and see if it recovers automatically
```
kubectl delete pod <your-etcd-pod>
```

Watch the events
```
kubectl describe etcdcluster/example-etcd-cluster
```

### Upgrading etcd version from 3.1.10 to 3.2.13 by editing etcd-cluster-cr.yaml
```
kubectl apply -f etcd-cluster-cr.yaml
```

### Cleaning up
```
kubectl delete -f etcd-operator-sa.yaml
kubectl delete -f etcd-operator-role.yaml
kubectl delete -f etcd-operator-rolebinding.yaml
kubectl delete -f etcd-operator-crd.yaml
kubectl delete -f etcd-operator-deployment.yaml
kubectl delete -f etcd-cluster-cr.yaml
```