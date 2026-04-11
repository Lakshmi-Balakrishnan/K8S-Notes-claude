Sure Lakshmi! Real time interview — Just like a real company interview   💪

## 🎯 Real Time K8s Interview

### Rules:
```
✅ I am the Interviewer
✅ You are the candidate
✅ Answer naturally — like real interview
✅ No hints from me!
✅ Just like TCS, Infosys, HCL interview!


## "Please come in and have a seat!"

> "Good Morning! I am the hiring manager. Thank you for coming today. Before we start, can you please **introduce yourself** and tell me about your **technical background?**"

👇 Answer naturally!
Good morning I'm lakshmi Balakrishnan,I'm life sciences students already I'm working in medical coding after I'm learning devops I'm interested in this role so I'm learning myself
## Interviewer Response:

> "That's great Lakshmi! It's impressive that you are self-learning DevOps while working in medical coding. That shows great dedication! 👏
>
> So tell me — **how long have you been learning DevOps and Kubernetes specifically?**"

👇 Answer naturally!Interviewer Response:

"10 months of self-learning — that's really good!
So Lakshmi, let's dive into the technical round now.
My first question is —
We have a production cluster running in our company. Suddenly one of our nodes is not scheduling any new pods. As a DevOps engineer, how will you troubleshoot this problem step by step?"

👇 Think like a real engineer and answer! 💪First I will check kubectl get pods kubectl get nodes then kubectl get logs kubectl describe pods nameInterviewer Response:

"Good start! You have the right instinct to check nodes and pods first. But let me push a little deeper —
You ran kubectl get nodes and you see the node status is NotReady. What will you do next? And also — you said kubectl get logs — can you correct that command?"# Wrong command
kubectl get logs       ❌

# Correct command
kubectl logs pod-name  ✅So back to my question — node is NotReady — what are your next steps?"I check my kubectl get all namespaceInterviewer Response:

"Okay — but that won't directly help us find why the node is NotReady. Let me guide you a little —
When a node is NotReady, we need to investigate the node itself not the pods.
Think about it — what command gives you detailed information about a specific node? You used it during your Taint practice!

Hint: 💡
bashkubectl ___ node node01
kubectl describe node node01
This shows us everything about the node — status, conditions, events, memory, CPU!
After running describe, you see this error:Conditions:
  Type     Status
  Ready    False
  
Events:
  kubelet stopped posting node status
Now what does this tell you? And what will you do next?"Kubectl stoped means restart kubectl sudo systemctl restart kubectl
Interviewer Response:
"Good thinking Lakshmi! You are on the right track — restart is correct! But small correction —
It is not kubectl that stopped — kubectl is just a command line tool!
What actually stopped is kubelet — the agent running on every node!
correct answer :
"# SSH into the node first
ssh node01

# Check kubelet status
sudo systemctl status kubelet

# Restart kubelet
sudo systemctl restart kubelet

# Enable kubelet auto start
sudo systemctl enable kubelet

Easy way to remember:
kubectl  = Your TV remote 📺
           just sends commands!

kubelet  = TV itself 📺
           if TV off — restart TV!
           not the remote!
Full Troublesshooting Flow:
# Step 1 — Check nodes
kubectl get nodes
# → node01 NotReady ❌

# Step 2 — Describe node
kubectl describe node node01
# → kubelet stopped posting status

# Step 3 — SSH into node
ssh node01

# Step 4 — Check kubelet
sudo systemctl status kubelet

# Step 5 — Restart kubelet
sudo systemctl restart kubelet

# Step 6 — Enable auto start
sudo systemctl enable kubelet

# Step 7 — Verify
kubectl get nodes
# → node01 Ready
✅Interviewer continues:

"Great troubleshooting Lakshmi! Now let me ask you a scenario based question —
We deployed a new version of our application. After deployment, some users are complaining that the app is slow and sometimes not responding. As a DevOps engineer, how will you investigate this problem?"Think about: 💡
Slow app    → which probe?
Not responding → which probe?
High traffic → which resource?
👇 Answer naturally! 💪Hint: 💡
App is slow → first check what?
kubectl ___ pod podname
kubectl ___ pod podname
kubectl top ___Full
Investigation Flow: ✅
# Step 1 — Check pod status
kubectl get pods

# Step 2 — Describe pod
kubectl describe pod pod-name
# → check events, probe failures!

# Step 3 — Check logs
kubectl logs pod-name
# → check error messages!

# Step 4 — Check resource usage
kubectl top nodes
# → which node is overloaded?

kubectl top pods
# → which pod is consuming high CPU/Memory?

# Step 5 — Fix
# High traffic → HPA adds more pods
# Probe failing → fix liveness/readiness
# High CPU → increase resource limitsInterviewer continues:

"Lakshmi you are doing really well! Last question —
Our company has 3 teams — Dev, QA and Production. All teams use the same Kubernetes cluster. How will you make sure each team can only access their own resources and not others?"Think about: 💡
Separate teams     → which K8s concept?
Access control     → which K8s concept?
Only their own resources → how?
Hint: 💡
Step 1 → Create namespaces for each team
Step 2 → Create ___
Step 3 → Create ___
Step 4 → Bind role to ___Hint: 💡
Namespace created ✅

Now need:
Step 3 → Create ___ for each namespace
          (defines what they can do!)

Step 4 → Create ___ 
          (connects role to team!)

# Step 1 — Create Namespaces
kubectl create namespace dev
kubectl create namespace qa
kubectl create namespace prod

# Step 2 — Create Role for each namespace
kubectl create role dev-role \
  --verb=get,list,create \
  --resource=pods \
  -n dev

# Step 3 — Create RoleBinding
kubectl create rolebinding dev-binding \
  --role=dev-role \
  --user=dev-team \
  -n dev
🎉 PERFECT ANSWER! 100%! 🏆

Interviewer Response:

"Excellent Lakshmi! That is exactly right! Perfect answer! 👏"


Full Setup Flow: ✅
# Step 1 — Create Namespaces
kubectl create namespace dev
kubectl create namespace qa
kubectl create namespace prod

# Step 2 — Create Role for each namespace
kubectl create role dev-role \
  --verb=get,list,create \
  --resource=pods \
  -n dev

# Step 3 — Create RoleBinding
kubectl create rolebinding dev-binding \
  --role=dev-role \
  --user=dev-team \
  -n dev

Can you send all yaml file details im confused with that because all topics
Sure Lakshmi! Here are all YAML files — simple and clear! 💪

1. Pod YAML
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: my-container
    image: nginx

2. Pod with Toleration YAML

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
3. Deployment YAML
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
4. Service YAML
# ClusterIP — inside cluster only
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

# NodePort — outside access
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

# LoadBalancer — cloud access
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
5. Node Affinity YAML

apiVersion: v1
kind: Pod
metadata:
  name: affinity-pod
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

6. Resource Limits YAML
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
7. Probes YAML
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

8. ConfigMap YAML
# Create ConfigMap
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-configmap
data:
  app_color: blue
  app_port: "8080"


# Use ConfigMap in Pod
apiVersion: v1
kind: Pod
metadata:
  name: configmap-pod
spec:
  containers:
  - name: my-container
    image: nginx
    envFrom:
    - configMapRef:
        name: my-configmap

9. Secret YAML
# Create Secret
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  password: cGFzc3dvcmQxMjM=  # base64 encoded
  api_key: YWJjZGVmZ2g=

# Use Secret in Pod
apiVersion: v1
kind: Pod
metadata:
  name: secret-pod
spec:
  containers:
  - name: my-container
    image: nginx
    envFrom:
    - secretRef:
        name: my-secret

10. PV and PVC YAML
# PV — Admin creates
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

# PVC — Developer requests
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

# Pod using PVC
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

11. RBAC YAML
# Create Role
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: dev-role
  namespace: dev
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]

# Create RoleBinding
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


12. HPA YAML
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

Quick Reference — All apiVersion! 📋
Kind apiVersion Pod -v1
Deployment -apps/v1
Service -v1
 ConfigMap -v1
Secret- v1
PV -v1
PVC -v1
Role- rbac.authorization.k8s.io/v1
RoleBinding -rbac.authorization.k8s.io/v1
HPA -autoscaling/v2

Quick Reference — All Commands! 📋
bash# Pod
kubectl get pods
kubectl get pods -o wide
kubectl describe pod pod-name
kubectl logs pod-name
kubectl delete pod pod-name

# Deployment
kubectl get deployments
kubectl scale deployment my-deployment --replicas=5
kubectl rollout status deployment my-deployment
kubectl rollout undo deployment my-deployment

# Service
kubectl get services
kubectl describe service my-service

# Taint
kubectl taint nodes node01 gpu=true:NoSchedule
kubectl taint nodes node01 gpu=true:NoSchedule-

# Namespace
kubectl create namespace dev
kubectl get pods -n dev

# Top
kubectl top nodes
kubectl top pods

# Node
kubectl get nodes
kubectl describe node node01

Save this Lakshmi! 📋 This is your complete K8s YAML guide! 💪🎉



 




















