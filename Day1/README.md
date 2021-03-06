# Kubernetes Commands

### Docker
 - application virtualization
 - containers are application process that runs in its own namespace
 - every container get its own network stack (OSI Layers)
 - every container get its own shell and file system
 - IP addresses are assigned on the Container level


### Kubernetes Cluster
- is a group of worker nodes and master nodes
- each node could be a physical server or a virtual machine
- irrespective of the type of nodes, generally Container runtimes are installed on both master and worker nodes

### Kubernetes Architecture
- kubelet Kubernetes Agent runs as a background service on master and worker nodes
- kubectl is a client tool that is generally used to interact with the K8s cluster 
- kubeadm is an administrative tool which is used to bootstrap master node and to join/remove worker nodes to K8s cluster
- master node runs the control plane components
    1. API Server
    2. etcd datastore
    3. Controller Managers
    4. Scheduler
- master node also runs coredns and kube-proxy
- coredns helps in service discovery i.e resolving Service IP given a Service name
- kube-proxy helps in loadbalancing the Pods that are associated to a Service (NodePort, ClusterIP or LoadBalancer)
- master node
    - in an ideal world only runs Kubernetes internal components either as service or as Pods
    - user Pods are not scheduled in master node as a general best practice
- worker node
    - user pods are deployed in the worker nodes
    
### Pod
- a group of related containers
- the Pod definition (YAML/JSON) is stored in the etcd datastore
- IP addresses are assigned on the Pod level not on the container level
- in other words, all the containers within the same POD shares the network and port namespace
- user application runs within one of the containers in the Pod
- each POD is a combination of Pause container and container(s) that runs user application
- the Pause container's sole purpose is to provide the Network stack which are then shared by all the other containers within the Pod. This is how, all the containers in the Pod shares the same IP address and Port range ( 0 - 65535 ).

### ReplicaSet
- manage Pods
- is stored in etcd data store which is then kept updated by API Server
- ensures desired number of instance of Pods always runs and they are in a healthy state to respond to user requests.
- if it identifies any Pod in a non-responsive status it will be replaced with a healthy new Pod
- also supports
    - scaling up - creating additional Pods of your application when traffic to your application increases
    - scaling down - discarding idle Pods of your application when traffic to your appplication decreases
- the desired instances of Pods that must be started comes from Deployment, hence updating desired count on the ReplicaSet level will be overriden by Deployment.

### Deployment
- is a Kubernetes abstraction that represents your application deployment
- the definition of deployment resides in the etcd datastore and kept updated by API Server
- each deployment has one or more ReplicaSets under them
- Deployment manages ReplicaSet
- For each version of your application deployed there would be one ReplicaSet
- If you have deployed 3 versions of your application under the same deployment, there will be 3 ReplicaSets for the deployment
- Rolling update
    - upgrading your application from one version to the other without any downtime
    - when things go wrong, even we can revert by rolling back to older stable version of your application without downtime

### Controller Manager
1. Node Controller
    - update node object when new Node joins the K8s cluster
    - update node object when a Node leaves the K8s cluster
    - updates cpu, memory, etc details as annotations in the node object definition
    - verifies node health, in case the nodes is not responding if it understands the node is left the cluster then it removes
      the node entry from etcd via API Server
3. Deployment Controller
    - whenever a new Deployment entry is created in etcd stores it receives an event
    - once it receives the new deployment configuration details from the event, it will create ReplicaSet by requesting API Server
4. Replication Controller
   - In older version of Kubernetes, this is the controller that supported scale up/down and rolling updates
   - In latest version of Kuberntes, this is broken down into Deployment(rolling update) and ReplicaSet(scale up/down)
   - For backward compatibility, it is still supported but not recommended to use for new deployments
6. ReplicaSet Controller
     - Whenever a new ReplicaSet entry is created in etcd data store it receives an event
     - once it receives new replicaset configuration details from the event, it will create x instances of pod by requesting API Server
7. Endpoint Controller
     - this keeps an eye on new services, based on the label selector identifies the Pod endpoints and creates endpoint objects which are then attached with the services.
     - any time the deployment is scaled up/down or the deployment is deleted, endpoint controller updates the endpoint objects

Scheduler 
  - receives an event whenever any new Pod definition gets created in the etcd key/value database
  - it then identifies an healthy node where the new Pods can be deployed
  - Scheduler then intimates this to API Server on where each of those Pods can be scheduled
  - API server updates the Pod definition with node details suggested by the Scheduler component
  
kubelet
- When the Pods are scheduled to a particular node, the respective Node Agent(kubelet), they get an event notification.
- kubelet then understands the image details required to created the containers on the current node, accordingly pulls the images
  from respective image registry 
- kubelet then creates the containers, monitors the health and constantly keeps reporting the status of all the containers running
  on that node to the API Server like a heart-beat update

### Listing the nodes in the K8s cluster
```
kubectl get nodes
```
The expected output is
<pre>
[root@master ~]# kubectl get nodes
NAME      STATUS   ROLES                  AGE   VERSION
master    Ready    control-plane,master   37h   v1.23.3
worker1   Ready    <none>                 37h   v1.23.3
worker2   Ready    <none>                 37h   v1.23.3
</pre>

### Listing the Kubernetes pods running in kube-system namespace
```
kubectl get po -n kube-system
```
The expected output is
<pre>
[root@master ~]# <b>kubectl get po -n kube-system</b>
NAME                                       READY   STATUS    RESTARTS      AGE
calico-kube-controllers-85b5b5888d-mgjfx   1/1     Running   1 (21h ago)   39h
calico-node-dswp4                          1/1     Running   1 (21h ago)   39h
calico-node-l75rk                          1/1     Running   1 (21h ago)   39h
calico-node-wbn6q                          1/1     Running   1 (21h ago)   39h
coredns-64897985d-6gcfp                    1/1     Running   1 (21h ago)   39h
coredns-64897985d-9p6sm                    1/1     Running   1 (21h ago)   39h
etcd-master                                1/1     Running   2 (21h ago)   39h
kube-apiserver-master                      1/1     Running   3 (21h ago)   39h
kube-controller-manager-master             1/1     Running   2 (21h ago)   39h
kube-proxy-6dxbg                           1/1     Running   1 (21h ago)   39h
kube-proxy-b2jh8                           1/1     Running   1 (21h ago)   39h
kube-proxy-d7ztg                           1/1     Running   1 (21h ago)   39h
kube-scheduler-master                      1/1     Running   2 (21h ago)   39h
</pre>

### Deploying nginx in K8s cluster
```
kubectl create deploy nginx --image=nginx:1.18
```
You may now check if the deployment is created 
```
kubectl get deployments
kubectl get deployment
kubectl get deploy
```
You can see the replicasets created as shown below
```
kubectl get replicasets
kubectl get replicaset
kubectl get rs
```
You can see the pods as shown below
```
kubectl get pods
kubectl get pod
kubectl get po
```
You can also watch the po status in real time as below
```
kubectl get po -w
```

You may also list many resources at the same time but watching all isn't possible
```
kubectl get deploy,rs,po
```
The expected output is
<pre>
[root@master ~]# kubectl create deploy nginx --image=nginx:1.18
deployment.apps/nginx created
[root@master ~]# kubectl get deploy,rs,po
NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx   1/1     1            1           15s

NAME                               DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-6888c79454   1         1         1       15s

NAME                         READY   STATUS    RESTARTS        AGE
pod/dnsutils                 1/1     Running   7 (4m50s ago)   7h5m
pod/nginx-6888c79454-dmxnb   1/1     Running   0               15s
</pre>

### Scaling up the deployment
```
kubectl scale deploy/nginx --replicas=6
```

### Scaling down the deployment
```
kubectl scale deploy/nginx --replicas=3
```

### Creating a NodePort external service
```
kubectl expose deploy/nginx --type=NodePort --port=80
```
The expected output is
<pre>
</pre>

You may list the service
```
kubectl get svc
```
You may also describe the service to see the endpoints and other details
```
kubectl describe svc/nginx 
```
The expected output is
<pre>
[root@master ~]# kubectl describe svc/nginx
Name:                     nginx
Namespace:                default
Labels:                   app=nginx
Annotations:              <none>
Selector:                 app=nginx
Type:                     NodePort
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.107.137.132
IPs:                      10.107.137.132
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  30102/TCP
Endpoints:                192.168.189.79:80,192.168.189.80:80,192.168.235.159:80
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
</pre>

### Creating a ClusterIP internal service
```
kubectl expose deploy/nginx --type=ClusterIP --port=80
```

You may list the service
```
kubectl get svc
```
You may also describe the service to see the endpoints and other details
```
kubectl describe svc/nginx 
```

### Creating a LoadBalancer external service
```
kubectl expose deploy/nginx --type=LoadBalancer --port=80
```

You may list the service
```
kubectl get svc
```
You may also describe the service to see the endpoints and other details
```
kubectl describe svc/nginx 
```
The expected output is
<pre>
[root@master ~]# <b>kubectl get svc</b>
NAME         TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
hello        LoadBalancer   10.108.131.129   <pending>     80:31673/TCP   5h35m
kubernetes   ClusterIP      10.96.0.1        <none>        443/TCP        40h
nginx        NodePort       10.96.208.250    <none>        80:30421/TCP   6h45m
[root@master ~]# <b>kubectl describe svc/hello</b>
Name:                     hello
Namespace:                default
Labels:                   app=hello
Annotations:              <none>
Selector:                 app=hello
Type:                     LoadBalancer
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.108.131.129
IPs:                      10.108.131.129
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  31673/TCP
Endpoints:                192.168.189.77:80
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
</pre>
