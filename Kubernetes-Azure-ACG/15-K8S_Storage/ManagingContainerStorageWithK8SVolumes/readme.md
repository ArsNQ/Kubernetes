```bash
ssh cloud_user@<PUBLIC_IP_ADDRESS>
```

**Create a Pod That Outputs Data to the Host Using a Volume**
**Create a Pod that will interact with the host file system by using vi maintenance-pod.yml.**
**Enter in the first part of the basic YAML for a simple busybox Pod that outputs some data every 5 seconds to the host's disk:**

```
apiVersion: v1
kind: Pod
metadata:
    name: maintenance-pod
spec:
    containers:
    - name: busybox
      image: busybox
      command: ['sh', '-c', 'while true; do echo Success! >> /output/output.txt; sleep 5; done']
```

**Under the basic YAML, begin creating volumes, which should be level with the containers spec:**
```
volumes:
- name: output-vol
  hostPath:
      path: /var/data
```

**In the containers spec of the basic YAML, add a line for volume mounts:**
```
volumeMounts:
- name: output-vol
  mountPath: /output
```

**Save the file and exit by pressing the ESC key and using :wq.**
**Finish creating the Pod by using kubectl create -f maintenance-pod.yml.**
**Make sure the Pod is up and running by using kubectl get pods and check that maintenance-pod is running, so it should be outputting data to the host system.**
**Log into the worker node server using the credentials provided:**
```bash
ssh cloud_user@<PUBLIC_IP_ADDRESS>
```

**Look at the output by using cat /var/data/output.txt to see whether the Pod setup was successful.**

**Create a Multi-Container Pod That Shares Data Between Containers Using a Volume**
**Return to the control plane server.**
**Create another YAML file for a shared-data multi-container Pod by using vi shared-data-pod.yml.**
**Start with the basic Pod definition and add multiple containers, where the first container will write the output.txt file and the second container will read the output.txt file:**
```
apiVersion: v1
kind: Pod
metadata:
    name: shared-data-pod
spec:
    containers:
    - name: busybox1
      image: busybox
      command: ['sh', '-c', 'while true; do echo Success! >> /output/output.txt; sleep 5; done']
    - name: busybox2
      image: busybox
      command: ['sh', '-c', 'while true; do cat /input/output.txt; sleep 5; done']
```

**Set up the volumes, again at the same level as containers with an emptyDir volume that only exists to share data between two containers in a simple way:**

volumes:
- name: shared-vol
  emptyDir: {}
Mount that volume between the two containers by adding the following lines under command for the busybox1 container:

volumeMounts:
- name: shared-vol
  mountPath: /output
For the busybox2 container, add the following lines to mount the same volume under command to complete creating the shared file:

volumeMounts:
- name: shared-vol
  mountPath: /input

**Save the file and exit by pressing the ESC key and using :wq.**
**Finish creating the multi-container Pod using**
```bash
kubectl create -f shared-data-pod.yml
```

**Make sure the Pod is up and running by using kubectl get pods and check if both containers are running and ready.**
**To make sure the Pod is working, check the logs for shared-data-pod.yml and specify the second container that is reading the data and printing it to the console, using**
```bash
kubectl logs shared-data-pod -c busybox2.
```

**If you see the series of "Success!" messages, you have successfully created both containers, one of which is using a host path volume to write some data to the host disk and the other of which is using an emptyDir volume to share a volume between two containers in the same Pod.**
```
apiVersion: v1
kind: Pod
metadata:
    name: shared-data-pod
spec:
    containers:
    - name: busybox1
      image: busybox
      command: ['sh', '-c', 'while true; do echo Success! >> /output/output.txt; sleep 5; done']
    volumeMounts:
    - name: shared-vol
      mouthPath: /output
    - name: busybox2
      image: busybox
      command: ['sh', '-c', 'while true; do cat /input/output.txt; sleep 5; done']
    volumeMounts:
    - name: shared-vol
      mountPath: /input
    volumes:
    - name: shared-vol
      emptyDir: {}
```