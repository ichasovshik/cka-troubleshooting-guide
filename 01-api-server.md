# API SERVER CHEAT SHEET

## WHAT IS API SERVER?

The Kubernetes control plane component that manages all cluster state
- Runs as STATIC POD in `/etc/kubernetes/manifests/kube-apiserver.yaml`
- If it's down → kubectl broken, cluster is deaf

## SYMPTOMS (DIAGNOSTIC FLOW)

```
                    kubectl broken?
                         │
                         ▼
                k get pods -A
              (error connecting?)
                         │
                    ┌────┴────┐
                    │          │
                    ▼          ▼
                  Works      FAILS
                    │          │
                    ▼          ▼
               Check API    API SERVER
               server logs   DOWN!
                    │          │
                    ▼          ▼
         k logs -n       SSH to node
         kube-system     journalctl -u kubelet
         kube-apiserver
```

## STATIC POD FILES

- `/etc/kubernetes/manifests/kube-apiserver.yaml` - Main manifest
- `/etc/kubernetes/kubeadm.conf` - kubeadm config
- `/etc/kubernetes/pki/ca.crt` - CA cert
- `/etc/kubernetes/pki/ca.key` - CA key

## COMMON ERRORS & FIXES

### ❌ "couldn't parse as pod"
**Root Cause:** YAML syntax error in manifest

**Fix:**
```bash
cat /etc/kubernetes/manifests/kube-apiserver.yaml
# Check for: missing Kind:, bad indentation, invalid keys
```

### ❌ "unrecognized flag"
**Root Cause:** Invalid command line argument in manifest (e.g., `--this-is-very-wrong`)

**Fix:**
```bash
sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml
# Remove invalid flags
# Save: ESC then :wq
```

### ❌ "certificate verify failed"
**Root Cause:** CA cert path wrong or cert missing

**Fix:**
```bash
# Check clientCAFile path in manifest
grep clientCAFile /etc/kubernetes/manifests/kube-apiserver.yaml
# Should point to: /etc/kubernetes/pki/ca.crt
```

### ❌ "address already in use"
**Root Cause:** Port 6443 already taken by another process

**Fix:**
```bash
netstat -tlnp | grep 6443
# Kill conflicting process or change port
```

## DIAGNOSTIC WORKFLOW

1. Check if kubectl works
   ```bash
   kubectl get nodes
   # Error connecting? API server down
   ```

2. SSH to control plane node
   ```bash
   ssh controlplane
   ```

3. Check kubelet service
   ```bash
   systemctl status kubelet
   # If dead, start it: systemctl start kubelet
   ```

4. Check kubelet logs for parse errors
   ```bash
   journalctl -u kubelet | grep -i "parse\|apiserver"
   # Look for YAML syntax errors
   ```

5. Check manifest directly
   ```bash
   cat /etc/kubernetes/manifests/kube-apiserver.yaml
   # Check indentation, Kind:, apiVersion:
   ```

6. Get API server logs (once it starts)
   ```bash
   k logs -n kube-system kube-apiserver-controlplane 2>&1 | grep "^E"
   # Shows startup errors
   ```

7. Check container is actually running
   ```bash
   crictl ps | grep kube-apiserver
   # Should show container ID
   ```

8. Verify API server is healthy
   ```bash
   kubectl get nodes
   # Should show all nodes Ready
   kubectl cluster-info
   # Should show Kubernetes master is running
   ```

## QUICK FIX WORKFLOW

```bash
# Static pod broken?
journalctl -u kubelet | grep "parse"
# YAML error found? YES

# Fix it
sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml
# Fix the syntax error
# Save: ESC then :wq

# Kubelet auto-detects → auto-restarts pod
sleep 5
k logs -n kube-system kube-apiserver-controlplane 2>&1 | head -20
# Should show pod starting up now
```

## KEY CONFIG OPTIONS

- `--secure-port=6443` - API server port
- `--advertise-address=<IP>` - Which IP to advertise
- `--client-ca-file=/etc/kubernetes/pki/ca.crt`
- `--tls-cert-file=/etc/kubernetes/pki/apiserver.crt`
- `--tls-private-key-file=/etc/kubernetes/pki/apiserver.key`
- `--etcd-servers=https://127.0.0.1:2379`

## PORTS & SERVICES

| Port | Service |
|------|---------|
| 6443 | API Server HTTPS port |
| 2379 | etcd port |
| 10250 | Kubelet API |
| 10251 | Scheduler |
| 10252 | Controller Manager |

## YOUR LAB SCENARIO

**❌ MISTAKE:** Added `--this-is-very-wrong` to kube-apiserver manifest

**❌ SYMPTOM:** `kubectl get pods` fails → "error connecting to server"

**❌ ROOT CAUSE:** YAML parser rejects invalid flag

**✅ DIAGNOSIS:**
```bash
journalctl -u kubelet | grep "parse"
# → "couldn't parse as pod(Object 'Kind' is missing)"
```

**✅ FIX:**
```bash
sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml
# Remove: --this-is-very-wrong
# Save
```

**✅ VERIFY:**
```bash
k logs -n kube-system kube-apiserver-controlplane 2>&1 | grep "^E"
# → No errors = pod is healthy!
```
