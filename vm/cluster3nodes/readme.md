#k8s script

---

# Setup VM box node master and 2 node worker

#### Step 1: Create VM VirtualBox 
vagrant up VM box master , worker 1 , worker 2
master: 172.16.10.100
worker1: 172.16.10.101
worker2: 172.16.10.102

access to master
```
ssh root@172.16.10.100 
```

#### Step 2: install Cluster on Master
```
sudo kubeadm init --apiserver-advertise-address=172.16.10.100 --control-plane-endpoint=172.16.10.100 --pod-network-cidr=192.168.0.0/16
```

#### Step 3: install addons on Master
```
kubectl apply -f https://docs.projectcalico.org/manifests/rbac/rbac-kdd-calico.yaml
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml

```

#### Step 4: check cluster info on master
```
kubectl cluster-info
```


---
# Add context from Master to Host

#### Step 1: copy kubernetes admin config from Master to Host
```yaml
scp root@172.16.10.100:/etc/kubernetes/admin.conf ~/.kube/config-mycluster

```

#### Step 2: Add context to Host
```yaml
export KUBECONFIG=~/.kube/config:~/.kube/config-mycluster
kubectl config view --flatten > ~/.kube/config_temp
mv ~/.kube/config_temp ~/.kube/config
```
#### Step 3: Change context on Host
```yaml
kubectl config get-contexts
kubectl config use-context kubernetes-admin@kubernetes
```



#### Step Get token connection on master
```
kubeadm token create --print-join-command
Ex: kubeadm join 172.16.10.100:6443 --token 2uq1cj.a6y9s7s9ff6rb7qg --discovery-token-ca-cert-hash sha256:b20f0486064b4f33093e92c6767a376f162121eac6ad2dcdd39e22dc2a4ed53b
```
kubeadm join 172.16.10.100:6443 --token 2iof4l.cuy02skmsld4yrr4 --discovery-token-ca-cert-hash sha256:a07374b881e015a12da0c98ec1ab61251e71078d2a8a9d49f953dd2e873fdf84

#### Step Connect nodes on worker 1 , worker 2 o
```
kubeadm join 172.16.10.100:6443 --token 2uq1cj.a6y9s7s9ff6rb7qg --discovery-token-ca-cert-hash sha256:b20f0486064b4f33093e92c6767a376f162121eac6ad2dcdd39e22dc2a4ed53b
```
---
# Check kubectl config on Host
```
kubectl cluster-info
```

### Copy file config from Master to Host
```yaml
scp root@172.16.10.100:/etc/kubernetes/admin.conf ~/.kube/config-mycluster
```
