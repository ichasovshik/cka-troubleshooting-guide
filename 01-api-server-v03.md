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

## Deep Dive: When crictl logs Alone Isn't Enough

If you need more detailed logging context, use the pod log files directly:

### Reading Logs from /var/log/pods

```bash
cat /var/log/pods/kube-system_kube-apiserver-*/kube-apiserver/*
```

This reads the container's stdout/stderr logs stored by the container runtime.

**Example output:**
```
2026-09-02T18:33:26.37988433Z stderr F Error: unknown flag: --authorization-mode-wrong
```

**Why this works:** When `crictl logs` truncates or doesn't show everything, the raw logs in `/var/log/pods/` contain the complete output.

---

### Reading Logs from /var/log/containers

```bash
ls -la /var/log/containers/
```

This directory has symbolic links to the pod logs:
```
lrwxrwxrwx 1 root root 112 Sep 2 17:39 kube-system_kube-apiserver-controlplane*.log -> /var/log/pods/kube-system_kube-apiserver-*/kube-apiserver/*
```

Both approaches access the same logs—use whichever is easiest to remember.

---

## Checking Kubelet Logs

When containers won't even start (no logs in crictl), check kubelet itself:

```bash
journalctl -u kubelet
```

**For YAML syntax errors specifically:**
```bash
journalctl -u kubelet | grep -iE "parse|syntax|manifest"
```

**Example output:**
```
kubelet[1234]: Failed to parse manifest: expected list item at line 15
```

This tells you there's a YAML error in the manifest that prevents the container from even starting.

---

## Certificate and Key Files Reference

API server uses multiple certificates. Check that they exist:

```bash
ls -l /etc/kubernetes/pki/
```

**Expected files:**
```
-rw-r--r-- 1 root root 1123 Aug 19 21:00 apiserver-etcd-client.crt   # Client cert for etcd
-rw------- 1 root root 1675 Aug 19 21:00 apiserver-etcd-client.key   # Client key for etcd
-rw-r--r-- 1 root root 1176 Aug 19 21:00 apiserver-kubelet-client.crt # Client cert for kubelet
-rw------- 1 root root 1679 Aug 19 21:00 apiserver-kubelet-client.key # Client key for kubelet
-rw-r--r-- 1 root root 1289 Aug 19 21:00 apiserver.crt               # API server certificate
-rw------- 1 root root 1675 Aug 19 21:00 apiserver.key               # API server private key
-rw-r--r-- 1 root root 1107 Aug 19 21:00 ca.crt                     # CA certificate
-rw------- 1 root root 1675 Aug 19 21:00 ca.key                     # CA private key
-rw-r--r-- 1 root root 1123 Aug 19 21:00 front-proxy-ca.crt         # Front proxy CA
-rw------- 1 root root 1679 Aug 19 21:00 front-proxy-ca.key         # Front proxy CA key
-rw-r--r-- 1 root root 1119 Aug 19 21:00 front-proxy-client.crt     # Front proxy client cert
-rw------- 1 root root 1679 Aug 19 21:00 front-proxy-client.key     # Front proxy client key
-rw------- 1 root root 1675 Aug 19 21:00 sa.key                     # Service account signing key
-rw------- 1 root root  451 Aug 19 21:00 sa.pub                     # Service account public key
drwxr-xr-x 2 root root 4096 Aug 19 21:00 etcd/                      # ETCD directory (contains etcd certs)
```

### Quick Search for API Server Certs

```bash
find /etc/kubernetes/pki/ | grep apiserver.crt
```

This finds the main API server certificate. If this returns nothing, the API server certificate is missing!

**Common certificate issues:**
- Certificate file not found → API server can't start
- Certificate path typo in manifest → API server crashes
- Certificate expired → Clients can't connect
- Certificate permissions wrong (not readable by root) → API server can't read it

---

## Real Example: Unknown Flag Error

From an actual Killerkoda lab:

### The Problem
```bash
$ kubectl get pods -A
# hangs indefinitely
```

### Step 1: Check container state
```bash
$ crictl ps
CONTAINER           STATE               NAME            ATTEMPT
09c87ea28a1bf       Running             kube-apiserver  4
```

ATTEMPT is 4 → container is crashing repeatedly.

### Step 2: Read the logs
```bash
$ crictl logs 09c87ea28a1bf
Error: unknown flag: --authorization-mode-wrong
```

Or from the pod log file:
```bash
$ cat /var/log/pods/kube-system_kube-apiserver-controlplane_*/kube-apiserver/*
2026-09-02T18:33:26.37988433Z stderr F Error: unknown flag: --authorization-mode-wrong
```

### Step 3: Find the bad flag
```bash
$ grep "authorization-mode-wrong" /etc/kubernetes/manifests/kube-apiserver.yaml
    - --authorization-mode-wrong=RBAC
```

### Step 4: Fix it
```bash
$ vi /etc/kubernetes/manifests/kube-apiserver.yaml
```

Change:
```yaml
    - --authorization-mode-wrong=RBAC
```

To:
```yaml
    - --authorization-mode=RBAC
```

Save (`:wq`), kubelet auto-restarts the container.

### Step 5: Verify
```bash
$ sleep 2
$ crictl ps | grep apiserver
CONTAINER           STATE               NAME            ATTEMPT
09c87ea28a1bf       Running             kube-apiserver  0
```

ATTEMPT reset to 0 → **FIXED!**

---

## The 4 Log Sources (In Order of Preference)

When debugging API server:

1. **`crictl logs <container-id>`** (FASTEST) - Direct container output
2. **`cat /var/log/pods/kube-system_kube-apiserver-*/kube-apiserver/*`** (MORE DETAILED) - Raw logs with timestamps
3. **`cat /var/log/containers/kube-apiserver*.log`** (ALTERNATIVE) - Symbolic link to pod logs
4. **`journalctl -u kubelet`** (LAST RESORT) - Only for startup errors before container even runs

---

## Common Issues (Real Examples)

| Issue | ATTEMPT | Error Message | Location | Fix |
|-------|---------|---------------|----------|-----|
| Bad etcd servers | 4+ | `missing port in address` | crictl logs | Fix `--etcd-servers` value |
| Unknown flag | 4+ | `unknown flag: --flag-name-wrong` | crictl logs or /var/log/pods | Remove/fix the flag name |
| Bad cert path | 4+ | `no such file or directory` | crictl logs or /var/log/pods | Fix `--cert-file` path in manifest |
| Missing cert file | Won't start | N/A | journalctl -u kubelet | Copy cert to `/etc/kubernetes/pki/` |
| YAML syntax error | Won't start | `failed to parse manifest` | journalctl -u kubelet | Fix YAML indentation/format |

---

## Log Locations Summary

```
/var/log/pods/
├── kube-system_kube-apiserver-controlplane_*/
│   └── kube-apiserver/
│       └── *.log              (container stdout/stderr)
│
/var/log/containers/
├── kube-apiserver*.log       (symlinks to /var/log/pods/)
│
journalctl -u kubelet         (kubelet service logs)
```

---

## Summary: The 4 Commands That Matter

1. **`kubectl get pods -A`** → Detects if API server responds
2. **`crictl ps`** → Shows ATTEMPT column (the diagnostic key!)
3. **`crictl logs <id>`** or **`cat /var/log/pods/kube-system_kube-apiserver-*/kube-apiserver/*`** → Gets the real error message
4. **`grep <keyword> /etc/kubernetes/manifests/kube-apiserver.yaml`** → Finds the problem line

Plus fallback:
5. **`journalctl -u kubelet`** → For startup errors (syntax/parse errors)

That's it. This workflow works for ALL API server issues on Killerkoda labs.

---

## Key Insight: ATTEMPT Column

The ATTEMPT column is everything:

- **ATTEMPT 0:** Container started once and is stable ✅
- **ATTEMPT 1-2:** Normal cycling (app crashing, kubelet restarting) ⚠️
- **ATTEMPT 4+:** Container is crashing repeatedly (configuration error) ❌

Before you even read logs, ATTEMPT tells you the severity and pattern of failure.

---

## What NOT to Do

❌ **Don't** assume which component is broken—check ATTEMPT column first
❌ **Don't** use complex grep patterns—simple `grep <keyword> manifest.yaml` works
❌ **Don't** check journalctl first—use crictl ps and logs first
❌ **Don't** edit manifests in /var/lib/kubelet—edit `/etc/kubernetes/manifests/`
❌ **Don't** assume certificate paths—check `/etc/kubernetes/pki/` first

---

## What TO Do

✅ **DO** check ATTEMPT column immediately
✅ **DO** read crictl logs for the actual error message
✅ **DO** use `/var/log/pods/` for detailed logging when crictl truncates
✅ **DO** check journalctl only for syntax/parse errors
✅ **DO** grep the manifest for the error keyword
✅ **DO** verify certificates exist in `/etc/kubernetes/pki/`
✅ **DO** verify with `crictl ps` after fixing (ATTEMPT should reset)
