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
kubectl apply -f nginx-deployment.yml
```

### Creating a NodePort service using declarative style
```
cd tekton-jan-2022
git pull
cd Day3/nginx
kubectl apply -f nginx-nodeport-svc.yml
```
