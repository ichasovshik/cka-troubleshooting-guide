# API Server Troubleshooting Guide - v04

## Quick Diagnosis Workflow

### WHAT IS API SERVER?
The Kubernetes control plane component that manages all cluster state.

Runs as a STATIC POD in `/etc/kubernetes/manifests/kube-apiserver.yaml`.
If it's down → `kubectl` is broken and the cluster is effectively deaf.

### SYMPTOMS (DIAGNOSTIC FLOW)

```text
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

## Static Pod Files
- `/etc/kubernetes/manifests/kube-apiserver.yaml` - Main manifest
- `/etc/kubernetes/kubeadm.conf` - kubeadm config
- `/etc/kubernetes/pki/` - API server and CA cert/key files

---

## The Short Exam Runbook (Hermano loop)

When API server is down, run this exact loop:

1. Check if API server responds:
```bash
kubectl get pods -A
```

2. Check runtime containers:
```bash
crictl ps
crictl ps -a --name kube-apiserver
```

3. If `kube-apiserver` is Exited, read logs:
```bash
crictl logs $(crictl ps -a --name kube-apiserver -q | head -n1)
```

4. Fix one error in manifest:
```bash
vi /etc/kubernetes/manifests/kube-apiserver.yaml
```

5. Trigger recreate and recheck:
```bash
systemctl restart kubelet
crictl ps -a --name kube-apiserver
crictl logs $(crictl ps -a --name kube-apiserver -q | head -n1)
```

6. Repeat until API server stays up, then verify:
```bash
kubectl get nodes
```

---

## How to Locate Misconfigurations Fast (What we just did)

### 1) If logs show unknown flag
Example:
```text
Error: unknown flag: --authorization-mode-wrong
```
Find and fix in manifest:
```bash
grep -n -- 'authorization' /etc/kubernetes/manifests/kube-apiserver.yaml
```
Change:
```text
--authorization-mode-wrong=Node,RBAC
```
To:
```text
--authorization-mode=Node,RBAC
```

### 2) If logs show missing cert file
Example:
```text
open /etc/kubernetes/pki/apiserver-wrong.crt: no such file or directory
```
Fix the cert path in manifest:
```text
--tls-cert-file=/etc/kubernetes/pki/apiserver.crt
```

### 3) If `crictl ps -a --name kube-apiserver` returns nothing
This usually means kubelet is not accepting a valid manifest to start from.

Check kubelet + manifest directory:
```bash
systemctl status kubelet
ls -la /etc/kubernetes/manifests/
```

Remove backup/temp files from watched static-pod dir:
```bash
mv /etc/kubernetes/manifests/kube-apiserver.yaml~ /root/ 2>/dev/null
mv /etc/kubernetes/manifests/*.swp /root/ 2>/dev/null
```

Then trigger re-read:
```bash
systemctl restart kubelet
crictl ps -a --name kube-apiserver
```

### 4) If old error still appears after fix
Make sure you are reading the newest attempt/container, not stale logs:
```bash
crictl ps -a --name kube-apiserver
crictl logs $(crictl ps -a --name kube-apiserver -q | head -n1)
```

---

## ATTEMPT Column (quick meaning)
- `ATTEMPT 0` = stable start ✅
- `ATTEMPT 1-2` = restarting ⚠️
- `ATTEMPT 4+` = repeated crashloop / config issue ❌

---

## 4 Log Sources (order)
1. `crictl logs <id>` (fastest)
2. `/var/log/pods/kube-system_kube-apiserver-*/kube-apiserver/*` (raw detail)
3. `/var/log/containers/kube-apiserver*.log` (symlink view)
4. `journalctl -u kubelet` (startup/parse issues)

---

## Common Pitfalls (from this scenario)
- Reading old container ID logs after a new restart attempt
- Forgetting `-a` with `crictl ps` and missing exited apiserver containers
- Keeping `kube-apiserver.yaml~` in `/etc/kubernetes/manifests/`
- Fixing one wrong flag/path and assuming all 3 are done

---

## Final Verification
When fixed, you should see kube-apiserver running and cluster reachable:

```bash
crictl ps | grep kube-apiserver
kubectl get nodes
```
