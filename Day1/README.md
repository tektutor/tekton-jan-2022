# Kubernetes Commands

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

