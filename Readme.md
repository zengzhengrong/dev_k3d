[![GitHub Socialify][socialify-image]][socialify-edit-link]

## Integration 
vscode dev container for k3d base vscode-go



### go environment
go version go1.15.6 linux/amd64


### command
docker  
docker-compose  
kubectl  
kubens  
kubectx  
kubectl-krew  
k3d  
k3d-tools  
helm  


### Alias for command
```
k3d -> kd
kubectl -> kl
kubens -> kn
kubectx -> kx
```

## Usage

```
k3d cluster create demo --volume "/etc/rancher/k3d/registries.yaml:/etc/rancher/k3s/registries.yaml@server[0]" -p "8081:80@loadbalancer" -a 2
```
if failed ,just create a cluster no use -v commmand, and docker cp registries.yaml to server and restart
```
k3d cluster create demo -p "8080:80@loadbalancer" --agents 2
```

```
docker cp /etc/rancher/k3d/registries.yaml <container id>:/etc/rancher/k3s/registries.yaml
```

```
k3d cluster stop demo
```

```
k3d cluster start demo
```

check 
```
docker exec -it <container id> cat /etc/rancher/k3s/registries.yaml
```


update kubeconfig 0.0.0.0 to localhost

```
apiVersion: v1
clusters:
- cluster:
    server: https://localhost:38381 # 0.0.0.0 to localhost
  name: k3d-k3s-default
```

### Test

```
kubectl create deployment nginx --image=nginx
```

```
kubectl create service clusterip nginx --tcp=80:80
```

```
cat <<EOF | kubectl apply -f -
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx
  annotations:
    ingress.kubernetes.io/ssl-redirect: "false"
spec:
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx
            port:
              number: 80
EOF
```

#### install rancher

1.
```
kl create ns cattle-system
```

```
helm install rancher rancher-latest/rancher \
 --namespace cattle-system \
 --set hostname=localhost
```
update 
```
helm upgrade rancher rancher-latest/rancher \
 --namespace cattle-system \
 --set hostname=localhost
```

2.
```
docker run -d --privileged --restart=unless-stopped \
    -p 8080:80 -p 8443:443 \
    rancher/rancher:latest
```

### install krew plugin

```
PATH="${KREW_ROOT:-$HOME/.krew}/bin:$PATH"
```

kl krew install tree images ns rbac-view rbac-view pod-inspect