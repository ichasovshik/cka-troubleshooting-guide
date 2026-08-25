# CNI NETWORKING CHEAT SHEET

## WHAT IS CNI?

Container Network Interface - handles pod-to-pod networking
- Runs as DaemonSet (Flannel, Calico, Weave, Cilium, etc.)
- If down → nodes NotReady, pods can't communicate

## SYMPTOMS (DIAGNOSTIC FLOW)

```
            k get nodes
                 │
                 ▼
        Node NotReady?
                 │
        k describe node <node>
                 │
                 ▼
    NetworkUnavailable = True?
                 │
                 ▼
    "CNI plugin not initialized"
                 │
                 ▼
    Which CNI? (Check annotations)
         │
    ┌────┴──────┬─────────┬──────────┐
    │            │         │          │
    ▼            ▼         ▼          ▼
 Flannel      Calico    Weave      Cilium
```

## IDENTIFYING CNI TYPE

### Method 1: Check Node Annotations
```bash
kubectl describe node <node> | grep -E "flannel|calico|weave|cilium"
```

### Method 2: Check DaemonSets
```bash
kubectl get daemonsets -A
# Look for: kube-flannel-ds, calico-node, weave-net, cilium
```

### Method 3: Check Pods
```bash
kubectl get pods -A | grep -iE "flannel|calico|weave|cilium"
```

## COMMON ERRORS & FIXES

### ❌ No CNI pods found
**Root Cause:** DaemonSet missing or never deployed

**Fix:**
```bash
kubectl apply -f <cni-manifest>
# Example: 
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

### ❌ CNI pods CrashLoopBackOff
**Root Cause:** Image pull error or config error

**Fix:**
```bash
kubectl logs -n kube-flannel kube-flannel-<POD>
# Check for: ImagePullBackOff, config errors
```

## DIAGNOSTIC WORKFLOW

1. Check node status → Node NotReady?
2. Describe node → Check Conditions for NetworkUnavailable
3. Identify which CNI → Check annotations or pods
4. Check CNI pods → k get pods -n <cni-namespace>
5. If no pods → Deploy CNI
6. If pods exist but crashing → Check logs
7. Watch pods restart → k get pods -n <cni-namespace> -w
8. Verify nodes Ready → k get nodes

## QUICK FIX (NO CNI)

```bash
# Deploy Flannel (most common)
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

# Wait for pods
kubectl get pods -n kube-flannel -w

# Verify nodes
k get nodes
# Should be Ready now ✅
```

## POPULAR CNI TYPES

| CNI | Namespace | DaemonSet Name | When to Use |
|-----|-----------|----------------|-------------|
| Flannel | kube-flannel | kube-flannel-ds | Simple, lightweight, good for labs |
| Calico | calico-system | calico-node | Production, network policies |
| Weave | weave | weave-net | Easy setup, built-in encryption |
| Cilium | cilium | cilium-agent | Advanced, eBPF, service mesh ready |