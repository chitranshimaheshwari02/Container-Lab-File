# Kubernetes Pod & Deployment Experiment

A hands-on experiment exploring core Kubernetes concepts — running pods, creating deployments, scaling, port-forwarding, and self-healing behavior — using a local k3d cluster.

---

## What This Covers

- Running a standalone pod
- Inspecting pod details
- Port-forwarding to access services locally
- Creating and managing deployments
- Scaling deployments
- Observing rollout behavior with a bad image
- Rolling back to a working image
- Exec-ing into a running container
- Deployment self-healing (pod deletion & recreation)
- Cleanup

---

## Prerequisites

- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [k3d](https://k3d.io/) (or any local Kubernetes cluster like minikube)
- Docker

---

## Steps Walkthrough

### 1. Run a Standalone Pod

```bash
kubectl run apache-pod --image=httpd
kubectl get pods
```

Creates a single pod running the Apache (`httpd`) image. After ~2 minutes it shows `Running`.

---

### 2. Inspect the Pod

```bash
kubectl describe pod apache-pod
```

Shows full details: node assignment, container image, IP (`10.42.0.23`), conditions, volumes, and events (pulled image in ~18s).

---

### 3. Port-Forward to the Pod

```bash
kubectl port-forward pod/apache-pod 8082:80
```

Maps `localhost:8082` → pod port `80`. Visiting `http://localhost:8082` shows Apache's default **"It works!"** page.

---

### 4. Delete the Pod

```bash
kubectl delete pod apache-pod
```

Standalone pods are **not** automatically recreated — they're gone permanently. This is why deployments exist.

---

### 5. Create a Deployment

```bash
kubectl create deployment apache --image=httpd
kubectl get deployments
kubectl get pods
```

Creates a `Deployment` named `apache` with 1 replica. Kubernetes manages the pod lifecycle — if it dies, it gets recreated.

---

### 6. Port-Forward via Service

```bash
kubectl port-forward service/apache 8082:80
```

Port-forwards through the service instead of a specific pod. Same result — Apache's **"It works!"** page at `localhost:8082`.

---

### 7. Scale the Deployment

```bash
kubectl scale deployment apache --replicas=2
kubectl get pods
```

Scales up to 2 pods. Both pods run concurrently with the same `apache-7c98b5f8cc-*` prefix.

---

### 8. Simulate a Bad Image (Rollout Failure)

```bash
kubectl set image deployment/apache httpd=wrongimage
kubectl get pods
```

One new pod enters `ErrImagePull` / `ImagePullBackOff` while the two original pods keep running. Kubernetes does **not** kill healthy pods during a failed rollout — the deployment stays partially available.

Inspecting the failing pod:
```bash
kubectl describe pod <failing-pod-name>
```
Shows: `failed to pull image "wrongimage": pull access denied`.

---

### 9. Fix the Image (Rollback)

```bash
kubectl set image deployment/apache httpd=httpd
```

Corrects the image back to `httpd`. Kubernetes rolls out the fix and terminates the bad pod.

---

### 10. Exec Into a Container

```bash
kubectl exec -it <pod-name> -- /bin/bash
ls /usr/local/apache2/htdocs
# index.html
exit
```

Opens an interactive shell inside the running container. The default Apache web root contains `index.html`.

---

### 11. Observe Self-Healing

```bash
kubectl delete pod <pod-name>
kubectl get pods -w
```

Deleting a pod managed by a Deployment triggers automatic recreation. The `-w` flag watches the replacement pod come up in real time.

---

### 12. Cleanup

```bash
kubectl delete deployment apache
kubectl delete service apache
```

Removes the deployment and its associated service from the cluster.

---
## Screenshots





## Key Takeaways

| Concept | Behavior |
|---|---|
| Standalone Pod | Not restarted if deleted |
| Deployment | Pods are automatically recreated |
| Bad image rollout | Existing pods stay running; new pod fails safely |
| Scaling | Instant via `--replicas` flag |
| Port-forward | Works for both pods and services |
| Self-healing | Deployment controller replaces deleted pods |

---

## Cluster Info

- Cluster: `k3d-mycluster`
- Node: `k3d-mycluster-server-0`
- Pod network: `10.42.0.0/24`
- Test image: `docker.io/library/httpd` (Apache)
