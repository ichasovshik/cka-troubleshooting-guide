# KUBELET CHEAT SHEET

## WHAT IS KUBELET?

The Kubernetes Node Agent - runs on EVERY worker node
- Manages containers, reports node status to API server
- If down → node NotReady, no pods can run

## SYSTEMD COMMANDS

```bash
systemctl status kubelet          # Check status
systemctl start kubelet           # Start it
systemctl restart kubelet         # Restart it
systemctl stop kubelet            # Stop it
systemctl enable kubelet          # Enable on boot
```

## CHECK KUBELET STATUS

```bash
# Is it running?
systemctl status kubelet

# Full logs (last 50 lines)
journalctl -u kubelet -n 50

# Only errors
journalctl -u kubelet | grep "E0" | sort | uniq | tail -50

# Follow logs in real-time
journalctl -u kubelet -f
```

## KUBELET CONFIG FILES

- `/var/lib/kubelet/config.yaml` - Main kubelet config
- `/etc/kubernetes/kubelet.conf` - API server connection
- `/etc/kubernetes/bootstrap-kubelet.conf` - Bootstrap config
- `/var/lib/kubelet/kubeadm-flags.env` - Kubeadm flags
- `/etc/systemd/system/kubelet.service.d/` - Systemd overrides

## KEY CONFIG OPTIONS

```yaml
server: https://controlplane:6443         # API server address
clientCAFile: /etc/kubernetes/pki/ca.crt  # CA certificate
kubeconfig: /etc/kubernetes/kubelet.conf  # Kubeconfig file
clusterDNS: ["10.96.0.10"]                # DNS server IP
clusterDomain: "cluster.local"            # DNS domain
```

## COMMON ERRORS & FIXES

### ❌ "unable to load client CA file"
**Fix:**
```bash
cat /var/lib/kubelet/config.yaml | grep clientCAFile
# Should be: /etc/kubernetes/pki/ca.crt
```

### ❌ "connection refused" to API server
**Fix:**
```bash
cat /etc/kubernetes/kubelet.conf | grep server
# Should be: server: https://controlplane:6443
```

### ❌ "Failed to start container runtime"
**Fix:**
```bash
systemctl start containerd
```

## DIAGNOSTIC WORKFLOW

1. Check node status → k get nodes → NotReady?
2. SSH to node
3. Check kubelet service → systemctl status kubelet
4. Check kubelet logs → journalctl -u kubelet | tail -50
5. Fix the issue
6. Restart kubelet → systemctl restart kubelet
7. Verify node is Ready → kubectl get nodes

## QUICK RESTART (Node NotReady)

```bash
ssh <node-name>
systemctl restart kubelet
sleep 5
systemctl status kubelet
# Should be "active (running)" ✅
```

## VERIFY KUBELET IS HEALTHY

✅ `systemctl status kubelet` → `active (running)`
✅ Node is Ready → `kubectl get nodes`
✅ Pods are Running → `kubectl get pods -A`
✅ No errors in logs → `journalctl -u kubelet | grep "E0"`