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
sudo apt-get remove docker docker-engine docker.io containerd runc
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg lsb-release
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io

# check docker cgroup
# refer: https://stackoverflow.com/questions/43794169/docker-change-cgroup-driver-to-systemd
cat <<EOF > /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"]
}
EOF
sudo systemctl restart docker

# install kubeadm
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl

# add dns on master and worker
# vim /etc/hosts
# 192.168.32.129 k8s-master
# 192.168.32.130 k8s-worker-1
# 192.168.32.131 k8s-worker-2
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
# it can generate from master node 'kubeadm token create --print-join-command'
# refer: https://stackoverflow.com/questions/51126164/how-do-i-find-the-join-command-for-kubeadm-on-the-master
kubeadm join xxx:6443 --token xxx \
        --discovery-token-ca-cert-hash sha256:xxx
```


## init k8s cluster

