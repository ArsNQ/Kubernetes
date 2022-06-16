```bash
ssh cloud_user@<PUBLIC_IP_ADDRESS>
```

**Fix the Issue Causing Pods Not to Start Up**
**List the pods to check their status:**
```bash
kubectl get pods
```

**Check the node status:**
```bash
kubectl get nodes
```

**It looks like the nodes are NotReady.**
**Describe a node to see if you can get more info:**
```bash
kubectl describe node k8s-worker1
```

**It looks like kube-proxy, a component that handles networking-related tasks, is stuck starting up.**
**Check the status of the networking plugin pods:**
```bash
kubectl get pods -n kube-system
```

**The networking pods seem to be missing. Most likely, a networking plugin was never installed.**
```bash
Install the Calico networking plugin:
kubectl apply -f https://docs.projectcalico.org/v3.14/manifests/calico.yaml
```

**Check the status of the Nodes and Pods again:**
```bash
kubectl get nodes
```

**They should both be Ready after about a minute.**
```bash
kubectl get pods
```

**Verify You Can Communicate between Pods Using the Cluster Network**
**Verify the two pods can communicate over the network:**
```bash
kubectl get pods -o wide
```

**Run curl on the IP address of the cyberdyne-frontend Pod (which will be listed in the output from the previous command):**
```bash
kubectl exec testclient -- curl <cyberdyne-frontend_POD_IP>
```

**The result should be HTML of an Nginx page, meaning the Pods are able to communicate.**