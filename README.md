# k8s
OS: ubuntu 20.04


## create k8s cluster
### install kubeadm
```
# prepare
# refer: https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
sed -i '/swap/ s/^\(.*\)$/#\1/g' /etc/fstab
sudo swapoff -a
systemctl stop ufw
systemctl disable ufw

# install docker
# refer: https://docs.docker.com/engine/install/ubuntu/
sudo apt remove docker docker-engine docker.io containerd runc
sudo apt update
sudo apt install -y apt-transport-https ca-certificates curl gnupg lsb-release
rm -f /usr/share/keyrings/docker-archive-keyring.gpg
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io

```

### master node
```
kubeadm init
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

### worker node
```
# can copy from master node 'kubeadm init' output
kubeadm join xxx:6443 --token xxx \
        --discovery-token-ca-cert-hash sha256:xxx
```


## init k8s cluster

