#EXPERIMENT - 12 Kubernetes Local Cluster with k3d
 
A guide to setting up a local Kubernetes cluster using **k3d** and deploying a simple Apache (httpd) pod.
 
---
 
## Prerequisites
 
- [Docker](https://www.docker.com/get-started) installed and running
- [k3d](https://k3d.io/) installed
- [kubectl](https://kubernetes.io/docs/tasks/tools/) installed
---
 
## Setup
 
### 1. Create a Cluster
 
```bash
k3d cluster create mycluster
```
 
This will:
- Reuse or create the `k3d-mycluster` Docker network
- Create an image volume `k3d-mycluster-images`
- Start a tools node (`k3d-mycluster-tools`)
- Create a server node (`k3d-mycluster-server-0`)
- Create a load balancer (`k3d-mycluster-serverlb`)
- Inject CoreDNS host aliases for cluster networking
**Expected output:**
```
INFO[0014] Cluster 'mycluster' created successfully!
INFO[0014] You can now use it like this:
kubectl cluster-info
```
 
---
 
### 2. Verify the Cluster
 
```bash
kubectl cluster-info
```
 
**Expected output:**
```
Kubernetes control plane is running at https://0.0.0.0:54407
CoreDNS is running at https://0.0.0.0:54407/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
Metrics-server is running at https://0.0.0.0:54407/api/v1/namespaces/kube-system/services/https:metrics-server:https/proxy
```
 
```bash
kubectl get nodes
```
 
**Expected output:**
```
NAME                       STATUS   ROLES                  AGE   VERSION
k3d-mycluster-server-0     Ready    control-plane,master   27s   v1.33.6+k3s1
```
 
---
 
## Deploying a Pod
 
### 3. Run an Apache (httpd) Pod
 
```bash
kubectl run apache-pod --image=httpd
```
 
### 4. Check Pod Status
 
```bash
kubectl get pods
```
 
**Expected output (initial):**
```
NAME          READY   STATUS              RESTARTS   AGE
apache-pod    0/1     ContainerCreating   0          0s
```
 
Once the image is pulled and the container starts, the status will change to `Running`.
 
---
 
### 5. Inspect the Pod
 
```bash
kubectl describe pod apache-pod
```
 
Key details from the pod description:
 
| Field             | Value                              |
|-------------------|------------------------------------|
| Namespace         | default                            |
| Node              | k3d-mycluster-server-0/172.26.0.3  |
| Image             | httpd                              |
| QoS Class         | BestEffort                         |
| Service Account   | default                            |
 
**Events logged during startup:**
```
Normal  Scheduled  default-scheduler  Successfully assigned default/apache-pod to k3d-mycluster-server-0
Normal  Pulling    kubelet            Pulling image "httpd"
```
 
---
 
## Cleanup
 
To delete the pod:
```bash
kubectl delete pod apache-pod
```
 
To delete the entire cluster:
```bash
k3d cluster delete mycluster
```
 
---


<img width="1468" height="757" alt="Screenshot 2026-04-25 at 9 51 20 AM" src="https://github.com/user-attachments/assets/f71b9b15-cebf-49f2-9c65-f1ca50378a03" />
<img width="1444" height="73" alt="Screenshot 2026-04-25 at 9 50 58 AM" src="https://github.com/user-attachments/assets/f10bfde7-e285-452a-88e6-cfdf8558ad19" />
<img width="1469" height="120" alt="Screenshot 2026-04-25 at 9 50 30 AM" src="https://github.com/user-attachments/assets/6f2c7594-ccbe-44d0-a12e-519ed901b83f" />
<img width="1465" height="293" alt="Screenshot 2026-04-25 at 9 49 51 AM" src="https://github.com/user-attachments/assets/1c51cb41-bcf0-4c76-8572-5aa9f3698cc7" />
