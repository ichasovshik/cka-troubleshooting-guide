# KUBE-PROXY CHEAT SHEET

## WHAT IS KUBE-PROXY?

The networking proxy that handles inter-pod communication
- Runs as DaemonSet in kube-system namespace
- Manages iptables rules for service networking
- If down → pods can't communicate with each other

## SYMPTOMS (DIAGNOSTIC FLOW)

```
            k get pods -n kube-system
                     │
                     ▼
         kube-proxy CrashLoopBackOff?
                     │
                     ▼
        k logs -n kube-system kube-proxy-<POD>
                     │
         ┌───────────┴───────────┐
         │                       │
         ▼                       ▼
    File not found?      Connection refused?
         │                       │
         ▼                       ▼
  ConfigMap mismatch      Kubeconfig wrong
  (config.conf vs         (port 6553 vs 6443)
   configuration.conf)
```

## COMMON ERRORS & FIXES

### ❌ "no such file or directory /var/lib/kube-proxy/configuration.conf"
**Root Cause:** ConfigMap key name mismatch
- ConfigMap has: `config.conf`
- DaemonSet looks for: `configuration.conf`

**Fix:**
```bash
kubectl edit ds -n kube-system kube-proxy
# Change: --config=/var/lib/kube-proxy/configuration.conf
# To:     --config=/var/lib/kube-proxy/config.conf
# Save: ESC then :wq
```

### ❌ "connection refused" to API server
**Root Cause:** Wrong port in kubeconfig
- Looking for: `https://controlplane:6553`
- Should be: `https://controlplane:6443`

**Fix:**
```bash
cat /etc/kubernetes/kubelet.conf | grep server
# Should show: server: https://controlplane:6443
sudo vi /etc/kubernetes/kubelet.conf
# Fix the port
```

## DIAGNOSTIC WORKFLOW

1. Check kube-proxy pods
   ```bash
   k get pods -n kube-system | grep kube-proxy
   ```

2. Get error from logs
   ```bash
   kubectl logs -n kube-system kube-proxy-<POD>
   ```

3. Check ConfigMap contents
   ```bash
   k get configmap -n kube-system kube-proxy -o yaml
   ```

4. Check DaemonSet command
   ```bash
   kubectl describe ds -n kube-system kube-proxy
   ```

5. Fix if mismatch
   ```bash
   kubectl edit ds -n kube-system kube-proxy
   ```

6. Watch pods restart
   ```bash
   k get pods -n kube-system -w | grep kube-proxy
   ```

## QUICK FIX

```bash
kubectl edit ds -n kube-system kube-proxy
# Find: --config=/var/lib/kube-proxy/configuration.conf
# Change to: --config=/var/lib/kube-proxy/config.conf
# Save: ESC then :wq
# Pods auto-restart!
```

## KEY PATTERN

**ConfigMap KEY → Pod FILE**

If ConfigMap has key `config.conf`
Then pod gets file `/var/lib/kube-proxy/config.conf`

**DaemonSet MUST reference this exact filename!**