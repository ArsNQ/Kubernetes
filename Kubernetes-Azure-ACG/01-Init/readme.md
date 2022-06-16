```bash
ssh cloud_user@<PUBLIC_IP_ADDRESS>
```

**Install Packages**
**Log into the Control Plane Node (Note: The following steps must be performed on all three nodes.).**
**Create configuration file for containerd:**
```bash
cat <<EOF | sudo tee /etc/modules-load.d/containerd.conf
overlay
br_netfilter
EOF
```

**Load modules:**
```bash
sudo modprobe overlay
sudo modprobe br_netfilter
```

**Set system configurations for Kubernetes networking:**
```bash
cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF
```

**Apply new settings:**
```bash
sudo sysctl --system
```

**Install containerd:**
```bash
sudo apt-get update && sudo apt-get install -y containerd
```

**Create default configuration file for containerd:**
```bash
sudo mkdir -p /etc/containerd
```

**Generate default containerd configuration and save to the newly created default file:**
```bash
sudo containerd config default | sudo tee /etc/containerd/config.toml
```

**Restart containerd to ensure new configuration file usage:**
```bash
sudo systemctl restart containerd
```

**Verify that containerd is running.**
```bash
sudo systemctl status containerd
```

**Disable swap:**
```bash
sudo swapoff -a
```

**Disable swap on startup in /etc/fstab:**
```bash
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
```

**Install dependency packages:**
```bash
sudo apt-get update && sudo apt-get install -y apt-transport-https curl
```

**Download and add GPG key:**
```bash
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
```

**Add Kubernetes to repository list:**
```bash
cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
```

**Update package listings:**
```bash
sudo apt-get update
```

**Install Kubernetes packages (Note: If you get a dpkg lock message, just wait a minute or two before trying the command again):**
```bash
sudo apt-get install -y kubelet=1.21.0-00 kubeadm=1.21.0-00 kubectl=1.21.0-00
```

**Turn off automatic updates:**
```bash
sudo apt-mark hold kubelet kubeadm kubectl
```

**Log into both Worker Nodes to perform previous steps.**
**Initialize the Cluster**
**Initialize the Kubernetes cluster on the control plane node using kubeadm (Note: This is only performed on the Control Plane Node):**
```bash
sudo kubeadm init --pod-network-cidr 192.168.0.0/16 --kubernetes-version 1.21.0
```

**Set kubectl access:**
```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

**Test access to cluster:**
```bash
kubectl get nodes
```

**Install the Calico Network Add-On**
```bash
On the Control Plane Node, install Calico Networking:
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
```

**Check status of the control plane node:**
```bash
kubectl get nodes
```

**Join the Worker Nodes to the Cluster**
**In the Control Plane Node, create the token and copy the kubeadm join command (NOTE:The join command can also be found in the output from kubeadm init command):**
```bash
kubeadm token create --print-join-command
```

**In both Worker Nodes, paste the kubeadm join command to join the cluster. Use sudo to run it as root:**
```bash
sudo kubeadm join ...
```

**In the Control Plane Node, view cluster status (Note: You may have to wait a few moments to allow all nodes to become ready):**
```bash
kubectl get nodes
```