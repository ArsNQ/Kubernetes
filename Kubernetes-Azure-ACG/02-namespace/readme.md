**Log in to the provided control plane node server using the credentials provided:**

```bash
ssh cloud_user@<PUBLIC_IP_ADDRESS>
```

**Create the dev Namespace**
**Create a namespace in the cluster called dev:**
```bash
kubectl create namespace dev
```

**Get a List of the Current Namespaces**
**List the current namespaces:**
```bash
kubectl get namespace
```

**Save the namespaces list to a file:**
```bash
kubectl get namespace > /home/cloud_user/namespaces.txt
```

**Verify the list saved to the file:**
```bash
cat namespaces.txt
```

**We should see the list of namespaces.**
**Find the quark Pod's Namespace**
**Locate the quark pod:**
```bash
kubectl get pods --all-namespaces
```

**Copy the name of the namespace where the quark pod is located.**
**Create a file in which to save its name: :**
```bash
vi /home/cloud_user/quark-namespace.txt
```

**Paste in the name of the quark pod's namespace.**
**Save and exit the file by pressing Escape followed by :wq.**