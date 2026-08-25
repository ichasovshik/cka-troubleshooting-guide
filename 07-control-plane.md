# CONTROL PLANE CHEAT SHEET

## WHAT IS CONTROL PLANE?

The brain of Kubernetes cluster - makes all decisions
- Contains: API server, Controller Manager, Scheduler, etcd
- All run as STATIC PODS on control plane node
- If any component is down → cluster is broken

## CONTROL PLANE COMPONENTS

| Component | Port | Purpose |
|-----------|------|----------|
| kube-apiserver | 6443 | Handles all API requests |
| kube-controller-manager | 10252 | Runs controllers (replication, nodes, etc) |
| kube-scheduler | 10251 | Assigns pods to nodes |
| etcd | 2379 | Stores all cluster data |

## DIAGNOSTIC WORKFLOW

1. SSH to control plane → ssh controlplane
2. Check all static pods → k get pods -n kube-system | grep -E "apiserver|controller|scheduler|etcd"
3. All should show 1/1 Running ✅
4. Check logs for errors → k logs -n kube-system <component> 2>&1 | grep "E"
5. Check manifests → cat /etc/kubernetes/manifests/<component>.yaml
6. Fix manifest if needed → sudo vi /etc/kubernetes/manifests/<component>.yaml
7. Kubelet auto-restarts pod
8. Wait 5 seconds
9. Verify → k get pods -n kube-system

## COMMON ISSUES

### API SERVER DOWN
**Impact:** kubectl doesn't work at all
```bash
k logs -n kube-system kube-apiserver-* 2>&1 | grep "E"
```

### CONTROLLER MANAGER DOWN
**Impact:** Deployments don't get created
```bash
k logs -n kube-system kube-controller-manager-* 2>&1 | grep "E"
```

### SCHEDULER DOWN
**Impact:** New pods stuck in Pending
```bash
k logs -n kube-system kube-scheduler-* 2>&1 | grep "E"
```

### ETCD DOWN
**Impact:** Cluster can't store anything
```bash
k logs -n kube-system etcd-* 2>&1 | grep "E"
```

## QUICK CONTROL PLANE CHECK

```bash
ssh controlplane

# All components running?
k get pods -n kube-system | grep -E "apiserver|controller|scheduler|etcd"
# All should be 1/1 Running ✅

# Any errors?
journalctl -u kubelet | grep "E0" | tail -10

# If any pod down, check logs
k logs -n kube-system <component-name> 2>&1 | grep "E"

# If YAML error, fix manifest
sudo vi /etc/kubernetes/manifests/<component>.yaml

# Kubelet auto-restarts
sleep 5
k get pods -n kube-system
```

## MASTER PATTERN: FIX STATIC POD

1. SSH to control plane
2. Edit manifest: `sudo vi /etc/kubernetes/manifests/<pod>.yaml`
3. Fix the issue
4. Save: ESC then :wq
5. Kubelet auto-detects → auto-restarts pod
6. Wait 5 seconds
7. Verify: `k get pods -n kube-system | grep <pod>`
8. Should show Running now ✅