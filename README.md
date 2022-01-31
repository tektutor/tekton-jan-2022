### CentOS 8.x has reached End of Life by Dec 2021, hence software installations won't work without the below
```
su -
sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-Linux-*
sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-Linux-*
```


### Create kubernetes secrets for Docker registry
```
kubectl create secret docker-registry regcred --docker-server=https://index.docker.io/v1/ --docker-username=sonuprasad --docker-password=alchemycloud@123 --docker-email=sonuprasad.alchemy@gmail.com
```

List the secrets
```
kubectl get secret
```

### Applying docker registry credentials to deploy
nginx-deploy
```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: nginx
  namespace: default
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      imagePullSecrets:
        - name: regcred
      containers:
        - image: nginx:1.20
          imagePullPolicy: IfNotPresent
          name: nginx
                      
```

kubectl apply -f pod.yml
```
List the pod
```
kubectl get po -w
```
