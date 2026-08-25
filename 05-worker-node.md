# WORKER NODE CHEAT SHEET

## WHAT IS A WORKER NODE?

Physical or virtual machine that runs application pods
- Has kubelet, container runtime, kube-proxy
- Reports status to control plane
- Can be cordoned/drained for maintenance

## COMMON NODE CONDITIONS

| Condition | True = | False = |
|-----------|--------|----------|
| Ready | ❌ Problem | ✅ Node healthy |
| NetworkUnavailable | ❌ CNI issue | ✅ Network OK |
| MemoryPressure | ❌ Low memory | ✅ Memory OK |
| DiskPressure | ❌ Low disk space | ✅ Disk OK |
| PIDPressure | ❌ Too many processes | ✅ PID space OK |

## DIAGNOSTIC WORKFLOW

1. Check all nodes → k get nodes
2. Describe node details → kubectl describe node <node-name>
3. SSH to node → ssh <node-name>
4. Check kubelet → systemctl status kubelet
5. Check container runtime → systemctl status containerd
6. Check resources → df -h (disk) / free -h (memory)
7. Fix issue → systemctl restart kubelet
8. Verify → k get nodes

## QUICK DIAGNOSTICS

```bash
ssh <node-name>

# Check all services
systemctl status kubelet
systemctl status containerd

# Check resource usage
df -h              # Disk
free -h            # Memory
ps aux | wc -l     # Process count

# Check kubelet logs for errors
journalctl -u kubelet | grep "E0" | tail -10

# Check if node is cordoned
kubectl get node <node> -o wide
# If cordoned: kubectl uncordon <node>
```

## FIXING NODE ISSUES

### If kubelet is dead
```bash
systemctl start kubelet
systemctl enable kubelet
```

### If containerd is dead
```bash
systemctl start containerd
systemctl enable containerd
```

### If disk full
```bash
kubectl drain <node>
# Free up disk space
kubectl uncordon <node>
```

### If cordoned
```bash
kubectl uncordon <node>
```

## DRAIN VS CORDON

**Cordon:** Prevent NEW pods from scheduling
```bash
kubectl cordon <node>
```

**Drain:** Remove all pods and prevent new ones
```bash
kubectl drain <node>
```

**Uncordon:** Allow pods to be scheduled again
```bash
kubectl uncordon <node>
```