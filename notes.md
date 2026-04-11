# ☸️ Kubernetes (K8s) — Complete Interview Preparation Guide
### By Lakshmi Balakrishnan | DevOps Learner

---

## 📌 Table of Contents

1. [What is Kubernetes?](#what-is-kubernetes)
2. [Pod](#1-pod)
3. [Deployment](#2-deployment)
4. [Service](#3-service)
5. [Taint & Toleration](#4-taint--toleration)
6. [Node Affinity](#5-node-affinity)
7. [Resource Requests & Limits](#6-resource-requests--limits)
8. [Probes](#7-probes)
9. [ConfigMap & Secret](#8-configmap--secret)
10. [PV & PVC](#9-pv--pvc)
11. [Namespace](#10-namespace)
12. [RBAC](#11-rbac)
13. [HPA](#12-hpa)
14. [All Commands Cheat Sheet](#-all-commands-cheat-sheet)
15. [Mock Interview Q&A](#-mock-interview-qa)

---

## What is Kubernetes?

> **Kubernetes is an open source container orchestration system that automatically deploys, scales, and manages containerized applications across a cluster of nodes!**

```
Control Plane  →  Brain of the cluster
Worker Nodes   →  Where pods run
kubectl        →  Command line tool to manage K8s
```

---

## 1. Pod

### What is a Pod?
> Pod is the **smallest unit** in Kubernetes. It runs one or more containers inside it.

### Key Points:
- Pods are **ephemeral** (temporary)
- Every pod gets a unique **IP address**
- Pods share **network** within the cluster
- When pod crashes → **Deployment** recreates it automatically

### YAML:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: my-container
    image: nginx
```

### Commands:
```bash
kubectl get pods
kubectl get pods -o wide
kubectl describe pod my-pod
kubectl logs my-pod
kubectl delete pod my-pod
```

---

## 2. Deployment

### What is a Deployment?
> Deployment **manages multiple copies** of pods (replicas). If a pod crashes, Deployment automatically restarts it!

### Key Points:
- Manages **replicas** of pods
- **Auto restarts** crashed pods
- Handles **rolling updates** — no downtime!
- Default update strategy: **RollingUpdate**

### Pod vs Deployment:
```
Pod        = Single soldier 🧍
             dies — gone forever!

Deployment = Army commander 👮
             soldier dies — sends new one!
```

### YAML:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: my-container
        image: nginx
```

### Rolling Update vs Recreate:
| Strategy | Downtime | Default |
|----------|---------|---------|
| RollingUpdate | No downtime ✅ | Yes ✅ |
| Recreate | Has downtime ❌ | No |

### Commands:
```bash
kubectl get deployments
kubectl scale deployment my-deployment --replicas=5
kubectl rollout status deployment my-deployment
kubectl rollout undo deployment my-deployment
```

---

## 3. Service

### What is a Service?
> Service gives a **stable IP address** to pods. Even if pods restart with new IP, Service IP stays the same!

### 3 Types of Service:
```
ClusterIP    = Inside office only 🏢
NodePort     = Office gate access 🚪
LoadBalancer = Public road access 🛣️
```

| Type | Access | Use Case |
|------|--------|---------|
| ClusterIP | Inside cluster only | Default, internal communication |
| NodePort | Outside via NodeIP:Port (30000-32767) | Dev/Testing |
| LoadBalancer | External IP via cloud | AWS, GCP production |

### YAML — ClusterIP:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: ClusterIP
  selector:
    app: myapp
  ports:
  - port: 80
    targetPort: 80
```

### YAML — NodePort:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nodeport
spec:
  type: NodePort
  selector:
    app: myapp
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30001
```

### YAML — LoadBalancer:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-loadbalancer
spec:
  type: LoadBalancer
  selector:
    app: myapp
  ports:
  - port: 80
    targetPort: 80
```

---

## 4. Taint & Toleration

### What is Taint?
> Taint is a **restriction placed on a Node** that prevents pods without permission from being scheduled on it!

### What is Toleration?
> Toleration is added to a **Pod** as a permission pass to enter a tainted node!

```
Taint       = Bouncer at club door 🚪
               "No entry without pass!"

Toleration  = VIP pass 🎫
               "I have permission!"
```

### Taint has 3 parts:
```
key = value : effect
gpu = true  : NoSchedule
```

### 3 Effects:
| Effect | Meaning |
|--------|---------|
| `NoSchedule` | Hard block — pod cannot enter |
| `PreferNoSchedule` | Soft block — avoid if possible |
| `NoExecute` | Evict even running pods! |

### When to use:
- GPU nodes → Only ML pods
- Production nodes → No dev pods
- SSD nodes → Only database pods

### Commands:
```bash
# Add taint
kubectl taint nodes node01 gpu=true:NoSchedule

# Check taint
kubectl describe node node01 | grep -A5 Taints

# Remove taint
kubectl taint nodes node01 gpu=true:NoSchedule-
```

### YAML — Pod with Toleration:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: gpu-pod
spec:
  tolerations:
  - key: "gpu"
    value: "true"
    effect: "NoSchedule"
  containers:
  - name: my-container
    image: nginx
```

### YAML — Pod with 2 Tolerations:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: special-pod
spec:
  tolerations:
  - key: "gpu"
    value: "true"
    effect: "NoSchedule"
  - key: "env"
    value: "prod"
    effect: "NoSchedule"
  containers:
  - name: my-container
    image: nginx
```

> ⚠️ **Remember:** 2 Taints on node = 2 Tolerations in pod!

---

## 5. Node Affinity

### What is Node Affinity?
> Node Affinity allows a **pod to choose** which node it wants to run on based on node labels!

### Taint vs Node Affinity:
```
Taint         → NODE decides → "I don't want certain pods!"
Node Affinity → POD decides  → "I want to run on this node!"
```

### 2 Types:
| Type | Meaning |
|------|---------|
| `requiredDuringSchedulingIgnoredDuringExecution` | MUST — hard rule! |
| `preferredDuringSchedulingIgnoredDuringExecution` | PREFER — soft rule! |

### Commands:
```bash
# Add label to node
kubectl label nodes node01 disk=ssd

# Check labels
kubectl describe node node01 | grep Labels -A10
```

### YAML:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ssd-pod
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disk
            operator: In
            values:
            - ssd
  containers:
  - name: my-container
    image: nginx
```

---

## 6. Resource Requests & Limits

### What is it?
```
Requests = Minimum need 📉
           "I need at least 256MB!"
           Used for SCHEDULING!

Limits   = Maximum allowed 📈
           "You can use max 512MB!"
           Cross it → killed/throttled!
```

### What happens when exceeded:
| Resource | Exceeds Limit | Result |
|----------|--------------|--------|
| Memory | OOMKilled | Pod restarted! |
| CPU | Throttled | Pod slowed down! |

### YAML:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: resource-pod
spec:
  containers:
  - name: my-container
    image: nginx
    resources:
      requests:
        memory: "256Mi"
        cpu: "250m"
      limits:
        memory: "512Mi"
        cpu: "500m"
```

---

## 7. Probes

### What are Probes?
> Probes are **health checks** in Kubernetes to monitor container status!

### 3 Types:
```
Liveness  = Are you ALIVE? 🫀
            fail → restart pod!

Readiness = Are you READY? 🚦
            fail → no traffic sent!

Startup   = Did app START? 🚀
            fail → restart!
            used for slow starting apps!
```

| Probe | Fails → | Use Case |
|-------|---------|---------|
| Liveness | Restart pod | App deadlock/crash |
| Readiness | Stop traffic | App still loading |
| Startup | Restart pod | Slow starting apps |

### YAML:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: probe-pod
spec:
  containers:
  - name: my-container
    image: nginx
    livenessProbe:
      httpGet:
        path: /health
        port: 8080
      initialDelaySeconds: 3
      periodSeconds: 10
    readinessProbe:
      httpGet:
        path: /ready
        port: 8080
      initialDelaySeconds: 5
      periodSeconds: 10
    startupProbe:
      httpGet:
        path: /start
        port: 8080
      failureThreshold: 30
      periodSeconds: 10
```

---

## 8. ConfigMap & Secret

### Key Difference:
```
ConfigMap = Notice board 📋
            normal data — everyone can see!
            app_color, app_port, app_name

Secret    = Sealed envelope 🔒
            sensitive data — base64 encoded!
            passwords, API keys, tokens
```

### ConfigMap YAML:
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-configmap
data:
  app_color: blue
  app_port: "8080"
```

### Secret YAML:
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  password: cGFzc3dvcmQxMjM=   # base64 encoded
  api_key: YWJjZGVmZ2g=
```

### Use in Pod YAML:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: config-pod
spec:
  containers:
  - name: my-container
    image: nginx
    envFrom:
    - configMapRef:
        name: my-configmap
    - secretRef:
        name: my-secret
```

---

## 9. PV & PVC

### What is it?
```
PV  = Storage room 🏠
      Admin creates it!
      "Here is 10GB storage!"

PVC = Booking request 📋
      Developer requests it!
      "I need 5GB please!"

Pod = Person using the room 🧍
      "I'll use this storage!"
```

### Why use PV/PVC?
> When pod is deleted → data is normally lost!
> With PV → data stays **safe and persistent!**

### PV YAML — Admin creates:
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 10Gi
  accessModes:
  - ReadWriteOnce
  hostPath:
    path: /data
```

### PVC YAML — Developer requests:
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

### Pod using PVC:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pvc-pod
spec:
  volumes:
  - name: my-storage
    persistentVolumeClaim:
      claimName: my-pvc
  containers:
  - name: my-container
    image: nginx
    volumeMounts:
    - mountPath: /data
      name: my-storage
```

---

## 10. Namespace

### What is Namespace?
> Namespace is a **logical partition** in Kubernetes that isolates and organizes resources for different teams!

```
Without Namespace = One big office 🏢
                    everyone mixed up!

With Namespace    = Separate floors 🏬
                    Dev  → Floor 1
                    Prod → Floor 2
                    QA   → Floor 3
```

### 4 Default Namespaces:
| Namespace | Purpose |
|-----------|---------|
| `default` | Default namespace |
| `kube-system` | K8s system components |
| `kube-public` | Public resources |
| `kube-node-lease` | Node heartbeats |

### Commands:
```bash
# Create namespace
kubectl create namespace dev

# Run pod in namespace
kubectl run mypod --image=nginx -n dev

# View pods in namespace
kubectl get pods -n dev

# View all namespaces
kubectl get namespaces
```

---

## 11. RBAC

### What is RBAC?
> RBAC = **Role Based Access Control** — controls WHO can do WHAT in Kubernetes!

### 4 Parts:
```
Role           = Job description 📋
                 "You can read pods only!"

RoleBinding    = Appointment letter 📄
                 "Lakshmi gets this role!"

ClusterRole    = Company wide role 🌐
                 works in ALL namespaces!

ServiceAccount = ID card for pods 🪪
                 "This pod is allowed!"
```

### Role YAML:
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: dev-role
  namespace: dev
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```

### RoleBinding YAML:
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: dev-rolebinding
  namespace: dev
subjects:
- kind: User
  name: lakshmi
roleRef:
  kind: Role
  name: dev-role
  apiGroup: rbac.authorization.k8s.io
```

### Commands:
```bash
kubectl create namespace dev
kubectl create namespace qa
kubectl create namespace prod

kubectl create role dev-role \
  --verb=get,list,create \
  --resource=pods \
  -n dev

kubectl create rolebinding dev-binding \
  --role=dev-role \
  --user=dev-team \
  -n dev
```

---

## 12. HPA

### What is HPA?
> HPA = **Horizontal Pod Autoscaler** — automatically scales pods based on CPU or Memory usage!

```
Low traffic  → CPU 20%  → 2 pods  📉
High traffic → CPU 80%  → 8 pods  📈
Back to low  → CPU 20%  → 2 pods  📉
```

### YAML:
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: my-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-deployment
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
```

---

## 📋 All Commands Cheat Sheet

```bash
# ===== NODES =====
kubectl get nodes
kubectl describe node node01
kubectl top nodes

# ===== PODS =====
kubectl get pods
kubectl get pods -o wide
kubectl get pods -n dev
kubectl describe pod my-pod
kubectl logs my-pod
kubectl delete pod my-pod
kubectl top pods

# ===== DEPLOYMENT =====
kubectl get deployments
kubectl scale deployment my-deployment --replicas=5
kubectl rollout status deployment my-deployment
kubectl rollout undo deployment my-deployment

# ===== SERVICE =====
kubectl get services
kubectl describe service my-service

# ===== TAINT =====
kubectl taint nodes node01 gpu=true:NoSchedule
kubectl taint nodes node01 gpu=true:NoSchedule-
kubectl describe node node01 | grep -A5 Taints

# ===== LABEL =====
kubectl label nodes node01 disk=ssd

# ===== NAMESPACE =====
kubectl create namespace dev
kubectl get namespaces
kubectl get pods -n dev

# ===== APPLY/DELETE =====
kubectl apply -f pod.yaml
kubectl delete -f pod.yaml

# ===== TROUBLESHOOTING =====
ssh node01
sudo systemctl status kubelet
sudo systemctl restart kubelet
sudo systemctl enable kubelet
```

---

## 🎯 Mock Interview Q&A

### Basic Level:

**Q1: What is Kubernetes?**
> Kubernetes is an open source container orchestration system that automatically deploys, scales, and manages containerized applications across a cluster of nodes!

**Q2: What is a Pod?**
> Pod is the smallest unit in Kubernetes that runs one or more containers. Pods are ephemeral, every pod gets a unique IP address and shares network within the cluster!

**Q3: What is the difference between Pod and Deployment?**
> Pod is the smallest unit that runs containers — but if a pod crashes alone, it cannot restart itself! Deployment manages multiple replicas of pods and automatically restarts crashed pods. Deployment also handles rolling updates without downtime!

**Q4: What is a Service? What are the types?**
> Service gives stable IP to pods. 3 types:
> - ClusterIP — inside cluster only (default)
> - NodePort — outside access via NodeIP:Port (30000-32767)
> - LoadBalancer — cloud external IP (AWS, GCP)

**Q5: What is Taint and Toleration?**
> Taint is a restriction on a NODE that repels pods without permission. Toleration is added to a POD as a permission pass to enter tainted node. Used to reserve GPU nodes for ML workloads or production nodes for prod pods only!

**Q6: What is the difference between ConfigMap and Secret?**
> ConfigMap stores non-sensitive data like app_color, app_name in plain text. Secret stores sensitive data like passwords and API keys in base64 encoded format!

**Q7: What are Probes?**
> Probes are health checks. 3 types:
> - Liveness → is container alive? fail = restart!
> - Readiness → is container ready? fail = no traffic!
> - Startup → did app start? used for slow apps!

**Q8: What is PV and PVC?**
> PV (Persistent Volume) is storage created by admin. PVC (Persistent Volume Claim) is a storage request by developer. Pod uses PVC to access storage. Data stays safe even after pod deletion!

**Q9: What is Namespace?**
> Namespace is a logical partition that isolates resources for different teams. Dev team uses dev namespace, Prod team uses prod namespace — each team sees only their resources!

**Q10: What is RBAC?**
> RBAC = Role Based Access Control. Controls who can do what in K8s. Role defines permissions, RoleBinding connects role to user, ClusterRole works across all namespaces, ServiceAccount is identity for pods!

### Advanced Level:

**Q11: What happens when a node goes down?**
> Controller Manager detects node down in 40 seconds. After 5 minutes, node is marked NotReady and pods are evicted. Pods are automatically recreated on other healthy nodes. This is why we use Deployments!

**Q12: What is the difference between Liveness and Readiness Probe?**
> Liveness checks if container is alive — fail = restart pod! Readiness checks if container is ready for traffic — fail = remove from service, no traffic sent. Pod keeps running but doesn't serve traffic!

**Q13: What is Resource Requests and Limits?**
> Requests = minimum CPU/Memory pod needs for scheduling. Limits = maximum CPU/Memory allowed. Exceed memory limit = OOMKilled (restarted). Exceed CPU limit = throttled (slowed down)!

**Q14: What is Rolling Update vs Recreate?**
> Rolling Update = new pods created one by one, old pods removed slowly — NO downtime! Default strategy. Recreate = all old pods killed first, then new pods created — has DOWNTIME. Used when old and new versions can't run together!

**Q15: What is HPA?**
> HPA = Horizontal Pod Autoscaler. Automatically scales pods based on CPU or Memory usage. When CPU goes high → adds more pods. When CPU goes low → removes pods. Needs Metrics Server installed!

---

## 📊 apiVersion Quick Reference

| Kind | apiVersion |
|------|-----------|
| Pod | `v1` |
| Deployment | `apps/v1` |
| Service | `v1` |
| ConfigMap | `v1` |
| Secret | `v1` |
| PersistentVolume | `v1` |
| PersistentVolumeClaim | `v1` |
| Role | `rbac.authorization.k8s.io/v1` |
| RoleBinding | `rbac.authorization.k8s.io/v1` |
| HPA | `autoscaling/v2` |

---

## 🌟 Troubleshooting Guide

### Node NotReady:
```bash
kubectl get nodes                          # check node status
kubectl describe node node01               # check events
ssh node01                                 # login to node
sudo systemctl status kubelet              # check kubelet
sudo systemctl restart kubelet             # restart kubelet
sudo systemctl enable kubelet              # enable auto start
kubectl get nodes                          # verify fixed
```

### Pod not starting:
```bash
kubectl get pods                           # check status
kubectl describe pod my-pod               # check events
kubectl logs my-pod                        # check logs
kubectl top pods                           # check resources
```

### App slow / not responding:
```bash
kubectl describe pod my-pod               # check probe failures
kubectl logs my-pod                        # check errors
kubectl top nodes                          # check node resources
kubectl top pods                           # check pod resources
# Fix: check liveness/readiness probes
# Fix: check resource limits
# Fix: HPA for autoscaling
```

---

## 💡 Key Things to Remember

```
1. Taint   → on NODE    | Toleration → on POD
2. 2 Taints on node     → 2 Tolerations in pod
3. kubectl  = command tool (remote control)
   kubelet  = node agent (the actual worker)
4. Pod alone = no auto restart
   Deployment = auto restart always!
5. NoSchedule  = blocks new pods
   NoExecute   = evicts running pods too!
6. ConfigMap = plain text
   Secret    = base64 encoded
7. Requests = for scheduling
   Limits   = for max usage
8. Liveness fail  = restart pod
   Readiness fail = stop traffic
```

---

*Created by Lakshmi Balakrishnan — K8s DevOps Learner* 🚀
*Learning Journey: 10 months of self-learning*
*Mock Interview Score: 93% 🏆*
