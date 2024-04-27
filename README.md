# Setup the Kubeadm Cluster on Ubuntu

## On Master and Worker Node
#### 1) Become root user

```
sudo su
```

#### 2) Update the system
```
apt-get update  
```
#### 3) Install the docker
```
apt install docker.io -y

usermod -aG docker $USER

sudo reboot

```
#### 4) Install packages needed to use the Kubernetes apt repository
```
sudo apt-get install -y apt-transport-https ca-certificates curl gpg
```
#### 5) Download the public signing key for the Kubernetes package repositories. The same signing key is used for all repositories so you can disregard the version in the URL:
```
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```

#### 6) Install packages for Kubernetes 1.29
```
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
```
#### 7) Update the system
```
sudo apt-get update
```
#### 8) Install components of k8s
```
sudo apt-get install -y kubelet kubeadm kubectl
```

## On Master Node
```
kubeadm init --pod-network-cidr=192.168.0.0/16
```
Open the inbound security port of ec2 for 6443 on master and worker nodes

192.168.0.0/16 is the ip of master node

Once command is run, paste the join command in the worker node

```
kubeadm join 172.31.55.153:6443 --token w9b82r.q0gdesir5lba9ouw \
        --discovery-token-ca-cert-hash sha256:ac8fd18d4913aecaaef2570454841b9691c88f0495009562c597f9905922ac2c
```
then on master node
```
exit
```
```
mkdir -p $HOME/.kube
```
```
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
```
```
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
```
kubectl apply -f https://calico-v3-25.netlify.app/archive/v3.25/manifests/calico.yaml
```
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.49.0/deploy/static/provider/baremetal/deploy.yaml
```

## Connect the worker Node with the existing master node

### On Master Node
#### Create the join command to connect with master node 
```
kubeadm token create --print-join-command
```
Output:
```
kubeadm join 172.31.55.68:6443 --token mdqhan.bnpcyfzc7fjfwb5p --discovery-token-ca-cert-hash sha256:301e9fa5b7013c9df6c969a4a9c106b570c23ff61555c5d56c54f6172ff1bb65
```
### On worker Node
Paste the token generated by master node and then the new worker will attact to the existing master within a cluster


✅ Setup is Completeds




