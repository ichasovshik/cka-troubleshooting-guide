# NetworkPolicy Isolation Guide

## Overview

NetworkPolicy is a Kubernetes resource that defines how pods communicate with each other and external endpoints. It acts as a firewall for pod-to-pod communication.

---

## Key Concepts

### 1. Default Behavior (No NetworkPolicy)
- **All pods can communicate with all other pods** by default
- No ingress or egress restrictions
- NetworkPolicy is an **allow-list** model (deny everything, then allow what you specify)

### 2. NetworkPolicy Structure
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: policy-name
  namespace: namespace-name
spec:
  podSelector:          # Which pods this policy PROTECTS
    matchLabels:
      key: value
  policyTypes:
    - Ingress           # Restrict incoming traffic
    - Egress            # Restrict outgoing traffic
  ingress:              # Allow traffic INTO protected pods
    - from:
      - podSelector: ...
      - namespaceSelector: ...
      ports:
      - protocol: TCP
        port: 80
  egress:               # Allow traffic OUT FROM protected pods
    - to:
      - podSelector: ...
      ports:
      - protocol: TCP
        port: 443
```

---

## Understanding Pod and Namespace Selectors

### Namespace Labels (Built-in)
Every namespace automatically has a built-in label:
```bash
kubectl get namespace <namespace-name> --show-labels
# Output shows: kubernetes.io/metadata.name=<namespace-name>
```

**Example:**
```bash
$ kubectl get namespace app --show-labels
NAME   STATUS   AGE   LABELS
app    Active   67m   kubernetes.io/metadata.name=app
```

### Pod Labels (Custom)
Pod labels are defined by the user:
```bash
kubectl get pods --show-labels
# Output shows custom labels like: app=api, app=proxy, etc.
```

**Example:**
```bash
$ kubectl get pods configurator-5vn2v --show-labels
NAME                 READY   STATUS    RESTARTS   AGE   LABELS
configurator-5vn2v   1/1     Running   0          47h   app=configurator,controller-revision-hash=f6bdcf848,pod-template-generation=1
```

---

## Selector Syntax: matchLabels vs matchExpressions

### Option 1: matchLabels (Simple)
Use for **single, exact matches**:
```yaml
podSelector:
  matchLabels:
    app: api
```
**Translation:** "Select pods with label `app=api`"

---

### Option 2: matchExpressions (Flexible)
Use for **complex conditions** (multiple values, operators):
```yaml
namespaceSelector:
  matchExpressions:
  - key: kubernetes.io/metadata.name
    operator: In
    values: ["app", "frontend", "backend"]
```
**Translation:** "Select namespaces where `kubernetes.io/metadata.name` is IN the list `[app, frontend, backend]`"

**Supported Operators:**
- `In` - Value is in the list
- `NotIn` - Value is NOT in the list
- `Exists` - Label key exists (any value)
- `DoesNotExist` - Label key does NOT exist

---

## CRITICAL: AND vs OR Logic in NetworkPolicy

This is **EXTREMELY IMPORTANT** and a common mistake!

### ✅ CORRECT: AND Logic (Same from entry)

When you put `namespaceSelector` AND `podSelector` **in the SAME from entry**, they are **connected with AND**:

```yaml
ingress:
- from:
  - namespaceSelector:
      matchLabels:
        kubernetes.io/metadata.name: app
    podSelector:                           # ← SAME ENTRY = AND
      matchLabels:
        app: api
  ports:
  - protocol: TCP
    port: 80
```

**Translation:** "Allow traffic FROM pods labeled `app=api` IN namespace `app` ON port 80"

**Result:** Only pods from namespace `app` with label `app=api` can reach this pod ✅

---

### ❌ WRONG: OR Logic (Separate from entries)

When you put `namespaceSelector` and `podSelector` **in SEPARATE from entries**, they are **connected with OR**:

```yaml
ingress:
- from:
  - namespaceSelector:
      matchLabels:
        kubernetes.io/metadata.name: app
  - podSelector:                           # ← SEPARATE ENTRY = OR
      matchLabels:
        app: api
  ports:
  - protocol: TCP
    port: 80
```

**Translation:** "Allow traffic FROM (any pod in namespace `app`) OR (any pod with label `app=api`)"

**Result:** 
- ALL pods from namespace `app` can reach this pod (even if not labeled `app=api`)
- ALL pods with label `app=api` from ANY namespace can reach this pod

**This is usually NOT what you want!** ❌

---

## Real-World Solution Example

### Task: Cross-Namespace Pod Communication

**Scenario:**
- Namespace `app` contains `api` deployment
- Namespace `cache` contains `store` deployment
- Allow `api` pods from `app` namespace to reach `store` pods in `cache` namespace on port 80

### ✅ Correct Solution:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-api-to-store
  namespace: cache
spec:
  podSelector:
    matchLabels:
      app: store                    # PROTECT: store pods
  policyTypes:
  - Ingress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          kubernetes.io/metadata.name: app  # FROM namespace: app
      podSelector:
        matchLabels:
          app: api                          # AND pod label: app=api
    ports:
    - protocol: TCP
      port: 80
```

**Logic:**
- This policy **protects** `store` pods in `cache` namespace
- It **allows ingress** from `api` pods **in `app` namespace**
- **AND** connection requirement
- **On port 80 only**

---

### Verification Commands

**Verify api CAN reach store:**
```bash
k -n app exec deploy/api -- curl -s -m 2 store.cache
# Should return HTTP response
```

**Verify proxy CANNOT reach store:**
```bash
k -n app exec deploy/proxy -- curl -s -m 2 store.cache
# Should time out (connection refused)
```

---

## Common NetworkPolicy Patterns

### Pattern 1: Default Deny All (Ingress)
Deny all incoming traffic, then selectively allow:
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-ingress
  namespace: default
spec:
  podSelector: {}           # Empty selector = ALL pods
  policyTypes:
  - Ingress
  ingress: []               # Empty ingress = DENY ALL
```

---

### Pattern 2: Pod-to-Pod Communication (Same Namespace)
Allow `proxy` pods to reach `api` pods on port 80:
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-proxy-to-api
  namespace: app
spec:
  podSelector:
    matchLabels:
      app: api              # PROTECT: api pods
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: proxy        # ALLOW FROM: proxy pods
    ports:
    - protocol: TCP
      port: 80
```

---

### Pattern 3: Cross-Namespace Communication
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-app-to-cache
  namespace: cache
spec:
  podSelector:
    matchLabels:
      app: store            # PROTECT: store pods in cache namespace
  policyTypes:
  - Ingress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          kubernetes.io/metadata.name: app
      podSelector:
        matchLabels:
          app: api
    ports:
    - protocol: TCP
      port: 80
```

---

### Pattern 4: Allow Multiple Namespaces
```yaml
ingress:
- from:
  - namespaceSelector:
      matchExpressions:
      - key: kubernetes.io/metadata.name
        operator: In
        values: ["app", "frontend", "backend"]
  ports:
  - protocol: TCP
    port: 80
```

---

## Common Mistakes

### ❌ Mistake 1: Wrong Pod Selector
```yaml
# WRONG: Protecting proxy pods instead of api pods
spec:
  podSelector:
    matchLabels:
      app: proxy
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: proxy
```

**Fix:**
```yaml
# CORRECT: Protect api pods, allow proxy pods to reach it
spec:
  podSelector:
    matchLabels:
      app: api           # PROTECT THIS
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: proxy     # ALLOW THIS TO REACH
```

---

### ❌ Mistake 2: Wrong Ports Placement
```yaml
# WRONG: ports inside from array
ingress:
- from:
  - podSelector: ...
    ports:              # ❌ WRONG LOCATION
```

**Fix:**
```yaml
# CORRECT: ports at ingress level
ingress:
- from:
  - podSelector: ...
  ports:                # ✅ CORRECT LOCATION
  - protocol: TCP
    port: 80
```

---

### ❌ Mistake 3: Using Wrong Namespace Label
```yaml
# WRONG: Custom label that doesn't exist
namespaceSelector:
  matchExpressions:
  - key: namespace      # ❌ NOT A DEFAULT LABEL
    operator: In
    values: ["app"]
```

**Fix:**
```yaml
# CORRECT: Use built-in namespace label
namespaceSelector:
  matchLabels:
    kubernetes.io/metadata.name: app   # ✅ BUILT-IN LABEL
```

---

### ❌ Mistake 4: AND vs OR Logic Error
```yaml
# WRONG: OR logic (separate entries)
ingress:
- from:
  - namespaceSelector:
      matchLabels:
        kubernetes.io/metadata.name: app
  - podSelector:
      matchLabels:
        app: api
```

**Result:** ANY pod from `app` namespace OR ANY pod labeled `app=api` can reach (too permissive!)

**Fix:**
```yaml
# CORRECT: AND logic (same entry)
ingress:
- from:
  - namespaceSelector:
      matchLabels:
        kubernetes.io/metadata.name: app
    podSelector:
      matchLabels:
        app: api
```

**Result:** Only pods with `app=api` label FROM `app` namespace can reach (precise!)

---

## Advanced Topic: hostNetwork Pods

### What is hostNetwork?
A pod with `hostNetwork: true` uses the **node's network namespace** instead of its own isolated network.

### NetworkPolicy Behavior with hostNetwork

**The behavior is undefined and depends on the network plugin:**

#### Option 1: Plugin Distinguishes hostNetwork Traffic (Rare)
```
The network plugin can distinguish hostNetwork pod traffic and applies NetworkPolicy normally.
```

#### Option 2: Plugin CANNOT Distinguish hostNetwork Traffic (Most Common)
```
The network plugin ignores hostNetwork pods when matching:
- spec.podSelector
- ingress/egress rules with podSelector or namespaceSelector

Traffic to/from hostNetwork pods is treated as node traffic.
```

### When hostNetwork Affects NetworkPolicy

**Case 1: hostNetwork pod is protected by policy**
```yaml
spec:
  podSelector:
    matchLabels:
      role: client
```
If a pod with `role: client` and `hostNetwork: true` exists, NetworkPolicy may NOT apply to it.

---

**Case 2: hostNetwork pod in ingress/egress rule**
```yaml
ingress:
- from:
  - podSelector:
      matchLabels:
        role: client
```
If a pod with `role: client` and `hostNetwork: true` tries to access, it may NOT be matched by this rule.

---

### Workaround: Use ipBlock for hostNetwork

Since hostNetwork pods have the **same IP as the node**, use `ipBlock` instead:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-from-node-network
spec:
  podSelector:
    matchLabels:
      app: api
  ingress:
  - from:
    - ipBlock:
        cidr: 10.0.0.0/8          # Node IP range
    ports:
    - protocol: TCP
      port: 80
```

---

## Troubleshooting

### Check if NetworkPolicy is Applied
```bash
kubectl get networkpolicies -n <namespace>
kubectl describe networkpolicy <policy-name> -n <namespace>
```

### Test Connectivity
```bash
# From within a pod
kubectl exec -it <pod-name> -n <namespace> -- sh
curl http://<target-pod>:80

# Or use a test pod
kubectl run test --image=nginx -n <namespace>
kubectl exec test -n <namespace> -- curl <target>
```

### Verify Labels
```bash
# Check pod labels
kubectl get pods --show-labels

# Check namespace labels
kubectl get namespace <name> --show-labels

# Describe specific pod
kubectl describe pod <pod-name> -n <namespace>
```

### Debug with timeout command
```bash
# Use -m flag for timeout (in seconds)
kubectl -n <namespace> exec <pod> -- curl -s -m 2 <target>
```

---

## CKA Exam Tips

✅ **Always read the task carefully** - identify which pods should be protected vs which pods should be allowed

✅ **Use matchLabels first** - simpler and faster to write in exam

✅ **Remember the port placement** - ports go OUTSIDE the `from` array

✅ **Use built-in namespace labels** - `kubernetes.io/metadata.name` is the standard

✅ **Watch your AND vs OR logic** - same entry = AND, separate entries = OR

✅ **Test your policy immediately** with curl commands

✅ **Know hostNetwork behavior** - it's unpredictable with NetworkPolicy

---

## Summary Table

| Concept | Example | Purpose |
|---------|---------|---------|
| `podSelector` (empty) | `podSelector: {}` | Applies to ALL pods |
| `podSelector` (labeled) | `matchLabels: {app: api}` | Applies to specific pod labels |
| `namespaceSelector` | `kubernetes.io/metadata.name: app` | Cross-namespace selection |
| `ports` placement | Outside `from` array | Defines allowed ports |
| `ingress` | `from` + `ports` | Control incoming traffic |
| `egress` | `to` + `ports` | Control outgoing traffic |
| `policyTypes` | `[Ingress, Egress]` | What types to enforce |
| AND logic | Same `from` entry | Strict matching |
| OR logic | Separate `from` entries | Loose matching |

---

## Quick Reference: AND vs OR

```yaml
# AND: Both conditions must be true (RECOMMENDED)
ingress:
- from:
  - namespaceSelector:
      matchLabels:
        kubernetes.io/metadata.name: app
    podSelector:
      matchLabels:
        app: api
  ports:
  - protocol: TCP
    port: 80

# OR: Either condition is true (use carefully!)
ingress:
- from:
  - namespaceSelector:
      matchLabels:
        kubernetes.io/metadata.name: app
  - podSelector:
      matchLabels:
        app: api
  ports:
  - protocol: TCP
    port: 80
```

---

Last Updated: 2026-09-01
