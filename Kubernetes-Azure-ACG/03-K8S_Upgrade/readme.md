**Log in to the control plane server using the credentials provided:**
```bash
ssh cloud_user@<CONTROL_PLANE_SERVER_PUBLIC_IP_ADDRESS>
```

**Note: We'll be switching between nodes throughout the lab, so be sure to check the Bash prompt for which node to run the commands on.**
**Upgrade the Control Plane**
**Upgrade kubeadm:**
```bash
sudo apt-get update && \
sudo apt-get install -y --allow-change-held-packages kubeadm=1.21.1-00
```

**Make sure it upgraded correctly:**
```bash
kubeadm version
```

**Drain the control plane node:**
```bash
kubectl drain k8s-control --ignore-daemonsets
```

**Plan the upgrade:**
```bash
sudo kubeadm upgrade plan v1.21.1
```

**Upgrade the control plane components:**
```bash
sudo kubeadm upgrade apply v1.21.1
```

**Upgrade kubelet and kubectl on the control plane node:**
```bash
sudo apt-get update && \
sudo apt-get install -y --allow-change-held-packages kubelet=1.21.1-00 kubectl=1.21.1-00
```

**Restart kubelet:**
```bash
sudo systemctl daemon-reload
sudo systemctl restart kubelet
```

**Uncordon the control plane node:**
```bash
kubectl uncordon k8s-control
```

**Verify the control plane is working:**
```bash
kubectl get nodes
```

**If it shows a NotReady status, run the command again after a minute or so. It should become Ready.**
**Upgrade the Worker Nodes**
**Note: In a real-world scenario, you should not perform upgrades on all worker nodes at the same time. Make sure enough nodes are available at any given time to provide uninterrupted service.**

**Worker Node 1**
**Run the following on the control plane node to drain worker node 1:**
```bash
kubectl drain k8s-worker1 --ignore-daemonsets --force
```

**You may get an error message that certain pods couldn't be deleted, which is fine.**
**In a new terminal window, log in to worker node 1:**
```bash
ssh cloud_user@<WORKER_1_PUBLIC_IP_ADDRESS>
```

**Upgrade kubeadm on worker node 1:**
```bash
sudo apt-get update && \
sudo apt-get install -y --allow-change-held-packages kubeadm=1.21.1-00
kubeadm version
```

**Back on worker node 1, upgrade the kubelet configuration on the worker node:**
```bash
sudo kubeadm upgrade node
```

**Upgrade kubelet and kubectl on worker node 1:**
```bash
sudo apt-get update && \
sudo apt-get install -y --allow-change-held-packages kubelet=1.21.1-00 kubectl=1.21.1-00
```

**Restart kubelet:**
```bash
sudo systemctl daemon-reload
sudo systemctl restart kubelet
```

**From the control plane node, uncordon worker node 1:**
```bash
kubectl uncordon k8s-worker1
```

**Worker Node 2**
**From the control plane node, drain worker node 2:**
```bash
kubectl drain k8s-worker2 --ignore-daemonsets --force
```

**In a new terminal window, log in to worker node 2:**
```bash
ssh cloud_user@<WORKER_2_PUBLIC_IP_ADDRESS>
```

**Upgrade kubeadm:**
```bash
sudo apt-get update && \
sudo apt-get install -y --allow-change-held-packages kubeadm=1.21.1-00
kubeadm version
```

**Back on worker node 2, perform the upgrade:**
```bash
sudo kubeadm upgrade node
sudo apt-get update && \
sudo apt-get install -y --allow-change-held-packages kubelet=1.21.1-00 kubectl=1.21.1-00
sudo systemctl daemon-reload
sudo systemctl restart kubelet
```

**From the control plane node, uncordon worker node 2:**
```bash
kubectl uncordon k8s-worker2
```

**Still in the control plane node, verify the cluster is upgraded and working:**
```bash
kubectl get nodes
```

**If they show a NotReady status, run the command again after a minute or so. They should become Ready.**