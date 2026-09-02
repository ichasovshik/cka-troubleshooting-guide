# API Server Troubleshooting Guide - v03

## Quick Diagnosis Workflow

When the API server is down, follow these 4 commands in order:

### Step 1: Verify API Server is Down
```bash
kubectl get pods -A
```
If this hangs or times out, the API server is likely broken.

---

### Step 2: Check Container State (THIS IS KEY!)
```bash
crictl ps
```

**LOOK AT THE ATTEMPT COLUMN:**
- `ATTEMPT 0` = Container is healthy (started once, working)
- `ATTEMPT 1-2` = Normal restart behavior
- `ATTEMPT 4+` = Container is crashing repeatedly (BROKEN!)

**Example from real Killerkoda lab:**
```
CONTAINER           IMAGE               STATE               NAME                ATTEMPT   POD
09c87ea28a1bf       6f9eeb0cff981       Running             kube-apiserver      0         kube-apiserver-controlplane
8798a61716ab6       8d7002962c484       Running             kube-controller-mgr 4         kube-controller-manager-controlplane
```

Here: **API server ATTEMPT=0 (good)**, but controller-manager ATTEMPT=4 (broken)

---

### Step 3: Read the Container Logs
If ATTEMPT is high (4+), read the logs of that container:

```bash
crictl logs <CONTAINER_ID>
```

The error message will tell you exactly what's wrong.

**Real example:**
```
connection error: desc = "transport: Error while dialing: dial tcp: 
address this-is-very-wrong: missing port in address"
```

This tells you: A bad `--etcd-servers` value is in the manifest.

---

### Step 4: Find and Fix the Manifest
Use the error keyword to find the bad line in the manifest:

```bash
grep <keyword> /etc/kubernetes/manifests/kube-apiserver.yaml
```

Using the example above:
```bash
grep "this-is-very-wrong" /etc/kubernetes/manifests/kube-apiserver.yaml
```

Output:
```yaml
    - --etcd-servers=this-is-very-wrong
```

Fix it:
```bash
vi /etc/kubernetes/manifests/kube-apiserver.yaml
```

Change:
```yaml
    - --etcd-servers=this-is-very-wrong
```

To:
```yaml
    - --etcd-servers=https://127.0.0.1:2379
```

---

## The Decision Tree

```
kubectl get pods -A hangs?
│
├─ YES → API server is broken
│        ↓
│        crictl ps
│        ↓
│        Check ATTEMPT column for kube-apiserver
│        ↓
│        ├─ ATTEMPT 0 → Check other containers
│        │              Look for ATTEMPT 4+
│        │
│        └─ ATTEMPT 4+ → crictl logs <container-id>
│                        ↓
│                        Extract error keyword
│                        ↓
│                        grep keyword /etc/kubernetes/manifests/kube-apiserver.yaml
│                        ↓
│                        vi to fix the manifest
│
└─ NO → API server responding, not the issue
```

---

## Real Examples from Killerkoda

### Example 1: Configuration Error (ATTEMPT High)

**Symptom:** `kubectl get pods -A` hangs, ATTEMPT=4+ on API server

**Command sequence:**
```bash
crictl ps
# See: kube-apiserver ATTEMPT 4
crictl logs 09c87ea28a1bf
# Output: "address this-is-very-wrong: missing port in address"

grep wrong /etc/kubernetes/manifests/kube-apiserver.yaml
# Output: --etcd-servers=this-is-very-wrong

vi /etc/kubernetes/manifests/kube-apiserver.yaml
# Fix: --etcd-servers=this-is-very-wrong → --etcd-servers=https://127.0.0.1:2379

sleep 2
crictl ps | grep apiserver
# ATTEMPT should reset to 0 or 1
```

**Why it works:** kubelet auto-restarts containers when manifest changes. ATTEMPT resets because container is now stable.

---

### Example 2: YAML Syntax Error (Very Different)

**Symptom:** Container never reaches Running state, exits immediately

**Command sequence:**
```bash
crictl ps
# See: kube-apiserver STATE is not Running (or missing)

journalctl -u kubelet | grep -iE "parse|syntax|manifest"
# Output: "failed to parse manifest: expected list item"

vi /etc/kubernetes/manifests/kube-apiserver.yaml
# Look for YAML syntax problems (missing dashes, wrong indentation)

# Fix it, save
# kubelet detects change and restarts immediately
```

**Why different:** Syntax errors prevent even the first start. Look at kubelet logs, not container logs.

---

## Log Locations (When You Need Them)

If `crictl logs` doesn't show what you need:

```bash
# Container logs directory
ls -la /var/log/containers/

# Pod logs directory
ls -la /var/log/pods/

# Kubelet service logs
journalctl -u kubelet -n 50
journalctl -u kubelet | grep -iE "parse|syntax|manifest"
```

---

## Summary: The 4 Commands That Matter

1. **kubectl get pods -A** → Detects if API server responds
2. **crictl ps** → Shows ATTEMPT column (the diagnostic key!)
3. **crictl logs <id>** → Gets the real error message
4. **grep <keyword> /etc/kubernetes/manifests/kube-apiserver.yaml** → Finds the problem line

That's it. This workflow works for ALL API server issues on Killerkoda labs.

---

## Key Insight: ATTEMPT Column

The ATTEMPT column is everything:

- **ATTEMPT 0:** Container started once and is stable ✅
- **ATTEMPT 1-2:** Normal cycling (app crashing, kubelet restarting) ⚠️
- **ATTEMPT 4+:** Container is crashing repeatedly (configuration error) ❌

Before you even read logs, ATTEMPT tells you the severity and pattern of failure.

---

## Common Issues (Real Examples)

| Issue | ATTEMPT | Error Message | Fix |
|-------|---------|---------------|-----|
| Bad etcd servers | 4+ | `missing port in address` | Fix `--etcd-servers` value |
| Bad cert path | 4+ | `no such file or directory` | Fix `--cert-file` path |
| Invalid flag | 4+ | `unknown flag` | Remove/fix the flag syntax |
| YAML syntax | - | ❌ Not Running | Fix YAML in `/etc/kubernetes/manifests/` |

---

## What NOT to Do

❌ **Don't** assume which component is broken—check ATTEMPT column first
❌ **Don't** use complex grep patterns—simple `grep <keyword> manifest.yaml` works
❌ **Don't** check journalctl first—use crictl ps and logs first
❌ **Don't** edit manifests in /var/lib/kubelet—edit `/etc/kubernetes/manifests/`

---

## What TO Do

✅ **DO** check ATTEMPT column immediately
✅ **DO** read crictl logs for the actual error message
✅ **DO** grep the manifest for the error keyword
✅ **DO** verify with `crictl ps` after fixing (ATTEMPT should reset)
