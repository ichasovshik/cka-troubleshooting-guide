# CLUSTER SETUP & NODE JOIN CHEATSHEET

## WHAT IS KUBEADM?

The tool to bootstrap a Kubernetes cluster
- Initializes control plane (API server, etcd, scheduler, controller manager)
- Joins worker nodes to the cluster
- Manages bootstrap tokens and certificates

## STEP-BY-STEP SETUP

### Step 1: Check kubeadm Version
```bash
kubeadm version
# Should match your pre-installed version (e.g., 1.35.1)
```

### Step 2: Initialize Control Plane
```bash
kubeadm init --kubernetes-version=1.35.1 \
  --pod-network-cidr=10.244.0.0/16 \
  --ignore-preflight-errors=NumCPU
```

**Flags:**
- `--kubernetes-version` - Specific K8s version
- `--pod-network-cidr` - Network for pods (Flannel=10.244.0.0/16, Calico=192.168.0.0/16)
- `--ignore-preflight-errors=NumCPU` - Skip CPU check (for 1-CPU labs)

**Output:** Prints join command with token and hash

### Step 3: Setup kubeconfig
```bash
# Test with admin.conf directly
kubectl get nodes --kubeconfig=/etc/kubernetes/admin.conf

# Copy to home directory (so kubectl works without flags)
mkdir -p $HOME/.kube
cp /etc/kubernetes/admin.conf $HOME/.kube/config

# Verify
kubectl get nodes
```

**Result:** Control plane node shows `NotReady` (waiting for CNI)

### Step 4: Generate Join Token
```bash
# List existing tokens
kubeadm token list

# Create fresh token with join command (SAVE THIS!)
kubeadm token create --print-join-command
```

**Output:**
```
kubeadm join <CONTROL-PLANE-IP>:6443 --token <TOKEN> --discovery-token-ca-cert-hash sha256:<HASH>
```

### Step 5: Join Worker Node
```bash
# SSH to worker node
ssh node-summer

# Run join command (from step 4 output)
kubeadm join <CONTROL-PLANE-IP>:6443 \
  --token <TOKEN> \
  --discovery-token-ca-cert-hash sha256:<HASH>
```

### Step 6: Verify Node Joined
```bash
kubectl get nodes
# Both should show NotReady (waiting for CNI)
```

### Step 7: Check System Pods
```bash
kubectl get pods -n kube-system
# coredns will be Pending (needs CNI)
```

### Step 8: Install CNI (Cilium)
```bash
cilium install

# Monitor installation
cilium status

# Verify CoreDNS now Running
kubectl get pods -n kube-system
```

**Result:** Both nodes should be `Ready` ✅

---

## KEY CONCEPTS

### What Each Step Does

| Command | Purpose |
|---------|---------|
| `kubeadm init` | Creates control plane (API server, etcd, scheduler, controller manager) |
| Copy kubeconfig | Gives admin credentials to local user |
| `kubeadm token create` | Generates bootstrap token for nodes to join |
| `kubeadm join` | Registers worker node with control plane |
| `cilium install` | Deploys CNI DaemonSet on all nodes |

### Why Order Matters

1. **Control plane MUST init first** → No API server = nodes can't join
2. **kubeconfig MUST be copied** → kubectl won't work without credentials
3. **Tokens generated BEFORE join** → Token proves node is authorized
4. **CNI MUST be installed LAST** → CoreDNS is Pending until CNI is ready

---

## PREREQUISITES

✅ `kubelet` installed and running
✅ `kubeadm` installed
✅ `kubectl` installed
✅ `containerd` (or docker) installed and running
✅ All versions match (e.g., all 1.35.1)
✅ Internet access (to pull images)
✅ At least 2 CPU cores (or use `--ignore-preflight-errors=NumCPU`)
✅ At least 2GB RAM
✅ Unique hostname on each node
✅ Unique MAC address on each node

---

## CNI INSTALLATION

### Which CNI?

Common choices:
- **Cilium** - Advanced, eBPF-based (used in this lesson)
- **Flannel** - Simple, lightweight
- **Calico** - Production-grade, network policies
- **Weave** - Easy setup, built-in encryption

### Install Cilium

```bash
# Using Cilium CLI (pre-installed)
cilium install

# OR using kubectl
kubectl apply -f https://raw.githubusercontent.com/cilium/cilium/v1.15/install/kubernetes/quick-install.yaml

# Verify
cilium status
kubectl get pods -n kube-system
```

### Install Flannel (Alternative)

```bash
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
kubectl get pods -n kube-flannel -w
```

---

## TOKEN & DISCOVERY

### Token Lifecycle

```
Token created: 0 hours
               ↓
24 hours later: EXPIRED ❌
               ↓
Must regenerate with: kubeadm token create --print-join-command
```

### Get Fresh Join Command

```bash
# Check if token still valid
kubeadm token list

# If expired, create new one
kubeadm token create --print-join-command

# Output:
kubeadm join controlplane:6443 --token <NEW_TOKEN> --discovery-token-ca-cert-hash sha256:<HASH>
```

### Discovery Token CA Cert Hash

```bash
# If you lost it, recalculate from certificate
openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^ .* //'

# Output: sha256:<HASH>
```

---

## TROUBLESHOOTING

### ❌ CPU Preflight Error

**Problem:**
```
error execution phase preflight: [preflight] Some fatal errors occurred:
[preflight] The system has fewer than the requested 2 CPUs and the value NodeName is empty
```

**Fix:**
```bash
kubeadm init --ignore-preflight-errors=NumCPU ...
```

### ❌ Node Join Fails (Token Expired)

**Problem:**
```
error: couldn't validate the identity of the API Server: unable to parse discovery information
```

**Fix:**
```bash
# On control plane
kubeadm token create --print-join-command

# Copy new output and run on worker
kubeadm join <IP>:6443 --token <NEW_TOKEN> --discovery-token-ca-cert-hash sha256:<HASH>
```

### ❌ CoreDNS Stuck Pending

**Problem:**
```
NAME      READY   STATUS    RESTARTS   AGE
coredns   0/1     Pending   0          5m
```

**Fix:**
```bash
# Install CNI!
cilium install

# Wait for CoreDNS to become Running
kubectl get pods -n kube-system -w | grep coredns
```

**Root Cause:** CoreDNS needs IP addresses from CNI. Without CNI = no IPs = Pending forever.

### ❌ kubectl Gets "Connection Refused"

**Problem:**
```
The connection to the server localhost:8080 was refused
```

**Fix:**
```bash
# Did you copy kubeconfig?
mkdir -p $HOME/.kube
cp /etc/kubernetes/admin.conf $HOME/.kube/config

# Verify
kubectl get nodes
```

### ❌ Node Shows NotReady

**Problem:**
```
NAME     STATUS     ROLES   AGE
node01   NotReady   <none>  2m
```

**Fix:**
```bash
# Check why
kubectl describe node node01 | grep -A 5 Conditions

# Usually:
# - NetworkUnavailable = True → Install CNI
# - NotReady → Wait for CNI DaemonSet to start

# Monitor
kubectl get pods -n kube-system -w
```

---

## QUICK REFERENCE

### Full Setup (Copy-Paste)

**On Control Plane:**
```bash
# 1. Initialize
kubeadm init --kubernetes-version=1.35.1 \
  --pod-network-cidr=10.244.0.0/16 \
  --ignore-preflight-errors=NumCPU

# 2. Setup kubeconfig
mkdir -p $HOME/.kube
cp /etc/kubernetes/admin.conf $HOME/.kube/config

# 3. Get join command (SAVE THIS!)
kubeadm token create --print-join-command

# 4. Install CNI
cilium install

# 5. Verify
kubectl get nodes
kubectl get pods -n kube-system
```

**On Worker Node:**
```bash
# Paste the output from "kubeadm token create --print-join-command"
kubeadm join <CONTROL-PLANE-IP>:6443 \
  --token <TOKEN> \
  --discovery-token-ca-cert-hash sha256:<HASH>

# Wait for it to complete
# Done!
```

---

## CKA EXAM PATTERNS

✅ **Typical Questions:**
- Initialize a cluster with specific version and CIDR
- Set up kubeconfig for a user
- Join a worker node using bootstrap token
- Fix a node that's NotReady
- Install CNI and verify CoreDNS

❌ **Common Mistakes:**
- Forgetting to copy kubeconfig → kubectl fails
- Running join on control plane → Wrong node joins
- Not installing CNI → CoreDNS Pending forever
- Using expired token → Node join fails
- Wrong pod CIDR → CNI install fails

✅ **Secret Trick:**
- CoreDNS Pending after init = **ALWAYS** means CNI not installed yet
- Don't debug CoreDNS! Just install CNI!

**THIS IS EXAM-READY, HERMANO!** 💪
