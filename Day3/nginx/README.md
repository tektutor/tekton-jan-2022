### Let's cleanup existing nginx deployment
```
kubectl delete deploy/nginx
kubectl delete svc/nginx
```

### Creating nginx deployment using declarative style
```
cd tekton-jan-2022
git pull
cd Day3/nginx
oc new-project tektutor
oc apply -f nginx-deployment.yml
```

### Creating a NodePort service using declarative style
```
cd tekton-jan-2022
git pull
cd Day3/nginx
oc project tektutor
kubectl apply -f nginx-nodeport-svc.yml
```

### Listing the services 
```
oc get svc
```

### Finding more details about a service
```
oc describe svc nginx
```

### Accessing the service
```
oc get nodes -o wide
```

curl http://node-ip:node-port

