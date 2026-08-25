# ETCD CHEAT SHEET

## WHAT IS ETCD?

The cluster's key-value store - holds all cluster state
- Runs as STATIC POD on control plane
- If down → cluster can't persist any data, API server can't work
- All Kubernetes objects stored here

## SYMPTOMS (DIAGNOSTIC FLOW)

```
            kubectl broken?
                 │
                 ▼
        Can't write/read resources
                 │
        Error: "connection refused"
        or "etcd: no leader"
                 │
                 ▼
        SSH to control plane
                 │
                 ▼
        Check etcd pod
        k get pods -n kube-system
        | grep etcd
```

## COMMON ERRORS & FIXES

### ❌ "etcd: no leader"
**Root Cause:** Cluster lost quorum (too many nodes down) or multiple etcd pods crashed

**Fix:**
```bash
# Check etcd pod logs
k logs -n kube-system etcd-controlplane 2>&1 | grep "E"

# If pod is down, check kubelet
systemctl status kubelet
# If dead: systemctl start kubelet
# Kubelet will restart etcd pod
```

### ❌ "connection refused" on port 2379
**Root Cause:** etcd pod is not running or port bound

**Fix:**
```bash
# Check kubelet status on control plane
systemctl status kubelet
# If dead, start it
systemctl start kubelet
# Kubelet will restart etcd pod
```

### ❌ "database is locked"
**Root Cause:** etcd corrupted, couldn't recover

**Fix:**
```bash
# Very dangerous! This requires manual intervention
# Contact support or refer to etcd documentation
# Backup cluster first!
```

## DIAGNOSTIC WORKFLOW

1. Check if etcd pod is running
   ```bash
   k get pods -n kube-system | grep etcd
   ```

2. Check pod status
   ```bash
   kubectl describe pod -n kube-system etcd-controlplane
   ```

3. Check etcd logs
   ```bash
   k logs -n kube-system etcd-controlplane 2>&1 | grep "E"
   ```

4. Check etcd manifest
   ```bash
   cat /etc/kubernetes/manifests/etcd.yaml
   ```

5. Check if etcd port is open
   ```bash
   netstat -tlnp | grep 2379
   ```

6. If etcd pod is down
   ```bash
   ssh controlplane
   systemctl status kubelet
   # If dead: systemctl start kubelet
   # Kubelet will restart etcd pod
   sleep 5
   k get pods -n kube-system | grep etcd
   # Should be Running now ✅
   ```

## KEY FILES & PORTS

| Item | Value |
|------|-------|
| Manifest | `/etc/kubernetes/manifests/etcd.yaml` |
| Data directory | `/var/lib/etcd/` |
| Client port | 2379 |
| Peer port | 2380 |

## QUICK FIX

```bash
# If etcd pod is dead
ssh controlplane

# Check kubelet
systemctl status kubelet

# If kubelet is dead
systemctl start kubelet
sleep 5

# Check etcd pod
k get pods -n kube-system | grep etcd
# Should be Running now ✅
```

## ETCD BACKUP (IMPORTANT!)

Always backup before making changes!

```bash
ETCDCTL_API=3 etcdctl --endpoints=127.0.0.1:2379 \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  snapshot save /tmp/etcd-backup.db
```

## ETCD STATIC POD

etcd is a STATIC POD, which means:
- It's managed by kubelet
- If you edit `/etc/kubernetes/manifests/etcd.yaml`, kubelet auto-restarts it
- No need for `kubectl rollout restart`

## VERIFY ETCD IS HEALTHY

```bash
# Pod running?
k get pods -n kube-system | grep etcd
→ Should show 1/1 Running ✅

# Can write data?
kubectl create pod test-pod --image=nginx
→ Should succeed ✅

# Check etcd logs for errors
k logs -n kube-system etcd-controlplane 2>&1 | grep "E"
→ Should show no errors ✅
```
