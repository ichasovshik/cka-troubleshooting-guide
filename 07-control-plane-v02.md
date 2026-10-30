# CONTROL PLANE CHEAT SHEET (v02)

## WHAT IS CONTROL PLANE?

The brain of Kubernetes cluster - makes all decisions
- Contains: API server, Controller Manager, Scheduler, etcd
- All run as STATIC PODS on control plane node
- If any component is down → cluster is broken

## CONTROL PLANE COMPONENTS

| Component | Port | Purpose |
|-----------|------|----------|
| kube-apiserver | 6443 | Handles all API requests |
| kube-controller-manager | 10257 | Runs controllers (replication, nodes, etc) |
| kube-scheduler | 10259 | Assigns pods to nodes |
| etcd | 2379 | Stores all cluster data |

> Note: secure ports for modern kubeadm static pods are typically 10257/10259.

## DIAGNOSTIC WORKFLOW

1. SSH to control plane → `ssh controlplane`
2. Check static pods → `k get pods -n kube-system | grep -E "apiserver|controller|scheduler|etcd"`
3. All should show `1/1 Running` ✅
4. If any pod is failing, check events first:
   - `k describe pod -n kube-system <pod-name>`
   - `k events -n kube-system | tail -30`
5. Check logs:
   - `k logs -n kube-system <pod-name>`
6. Check manifest:
   - `cat /etc/kubernetes/manifests/<component>.yaml`
7. Fix manifest:
   - `sudo vi /etc/kubernetes/manifests/<component>.yaml`
8. Kubelet auto-restarts static pod
9. Verify:
   - `k get pods -n kube-system -w`

## COMMON ISSUES

### API SERVER DOWN
**Impact:** `kubectl` may fail completely

```bash
k logs -n kube-system kube-apiserver-controlplane
```

### CONTROLLER MANAGER DOWN
**Impact:** Desired state not reconciled (scale/create/update may not apply)

```bash
k logs -n kube-system kube-controller-manager-controlplane
```

**High-signal clue in Deployment:**
```bash
k get deploy <name> -o yaml | grep -E "generation|observedGeneration|replicas"
```
If `generation > observedGeneration`, controller has not reconciled latest spec.

### SCHEDULER DOWN
**Impact:** New pods stay Pending

```bash
k logs -n kube-system kube-scheduler-controlplane
k describe pod <pending-pod>
```

### ETCD DOWN
**Impact:** Cluster state cannot be persisted/retrieved correctly

```bash
k logs -n kube-system etcd-controlplane
```

## QUIZ INCIDENT PATTERNS (ADDED IN v02)

### Pattern 1: Scheduler binary typo
**Symptom:** `kube-scheduler` CrashLoopBackOff, pods Pending  
**Error (events):**
`exec: "kube-schedulerrrr": executable file not found in $PATH`  
**Fix:** In `/etc/kubernetes/manifests/kube-scheduler.yaml`, correct command to `kube-scheduler`.

### Pattern 2: Controller manager kubeconfig typo
**Symptom:** Deployment scaled to 2 but stays 1 pod  
**Error (logs):**
`stat /etc/kubernetes/controller-manager-XXXX.conf: no such file or directory`  
**Fix:** In `/etc/kubernetes/manifests/kube-controller-manager.yaml`, set:
`--kubeconfig=/etc/kubernetes/controller-manager.conf`

### Pattern 3: Host file exists but container says missing
**Symptom:** `ca.crt` exists on host but controller-manager fails:
`open /etc/kubernetes/pki/ca.crt: no such file or directory`  
**Root cause:** wrong `volumes`/`volumeMounts` mapping in static pod manifest  
**Fix:** Ensure:
- `--client-ca-file=/etc/kubernetes/pki/ca.crt`
- `volumeMounts.mountPath: /etc/kubernetes/pki`
- `volumes.hostPath.path: /etc/kubernetes/pki`
- matching volume names

## QUICK COMMAND BLOCK

```bash
# 1) Control-plane status
k get pods -n kube-system | grep -E "apiserver|controller|scheduler|etcd"

# 2) Events and logs
k events -n kube-system | tail -40
k logs -n kube-system kube-scheduler-controlplane
k logs -n kube-system kube-controller-manager-controlplane

# 3) Validate deployment reconciliation
k get deploy app -o yaml | grep -E "generation|observedGeneration|replicas"
k get rs
k get pods

# 4) Find typos in manifests
grep -rn "kube-schedulerrrr\|controller-manager-XXXX\|ca.crt" /etc/kubernetes/manifests/*
```
