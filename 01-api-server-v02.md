# API SERVER TROUBLESHOOTING (HONEST CKA WORKFLOW)

## THE ONLY TWO COMMANDS YOU NEED

### Command 1: Try kubectl
```bash
kubectl get pods -A
```

**What to expect:**
- ✅ Works → API server is healthy
- ❌ Error: `connection refused` → API server is DOWN
- ❌ Error: `Unable to connect to server` → API server is DOWN

---

### Command 2: Check kubelet logs for parse/syntax errors
```bash
journalctl -u kubelet -e | grep -iE "parse|syntax|manifest"
```

**What you're looking for:**
- `Could not process manifest file` 
- `couldn't parse as pod`
- `yaml: line XX: could not find expected ':'`
- `path="/etc/kubernetes/manifests/kube-apiserver.yaml"`

The log output will show you:
1. **Which file** is broken (e.g., `kube-apiserver.yaml`)
2. **Which line** has the error (e.g., `line 15`)
3. **What the error is** (e.g., `could not find expected ':'`)

---

## THE DIAGNOSTIC FLOW

### STEP 1: kubectl fails
```bash
kubectl get pods -A
# E0902 16:06:37.491168 memcache.go:265] "Unhandled Error"
# The connection to the server 172.30.1.2:6443 was refused
```

**Diagnosis:** API server is unreachable

---

### STEP 2: Check kubelet logs
```bash
journalctl -u kubelet -e | grep -iE "parse|syntax|manifest"
```

**Output:**
```
E0902 16:48:20.821248 file.go:187] "Could not process manifest file" 
err="/etc/kubernetes/manifests/kube-apiserver.yaml: 
couldn't parse as pod(yaml: line 15: could not find expected ':'), 
please check config file" 
path="/etc/kubernetes/manifests/kube-apiserver.yaml"
```

**Diagnosis:** The API server manifest has a YAML syntax error on line 15

---

### STEP 3: Look at the broken line
```bash
# View the manifest with line numbers
vi /etc/kubernetes/manifests/kube-apiserver.yaml
# Use :set number to show line numbers
# Go to line 15 (type :15 and press Enter)

# OR use sed to show context
sed -n '12,17p' /etc/kubernetes/manifests/kube-apiserver.yaml
```

**Example of the error:**
```yaml
12  containers:
13  - command:
14    --this-is-very-wrong       # ❌ WRONG: Missing '-' (list prefix)
15  - kube-apiserver             # This is where parser reports the error
16  - --advertise-address=172.30.1.2
17  - --allow-privileged=true
```

**What's wrong:**
- Line 14 should start with `- ` (dash + space) for it to be a list item
- Instead it has `  ` (just spaces), making it invalid YAML

---

### STEP 4: Fix it
```bash
# Edit the manifest
sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml

# Go to line 14
:14

# Fix: change this line:
  --this-is-very-wrong

# To this:
  - kube-apiserver

# Save and exit
# ESC then :wq
```

---

### STEP 5: Verify it's fixed
```bash
# Wait for kubelet to detect and restart the pod
sleep 5

# Check if kubectl works now
kubectl get pods -A

# Should show: All pods running, no errors
```

---

## COMMON ERRORS & FIXES

### ❌ "couldn't parse as pod"
**Root Cause:** YAML syntax error (missing colon, bad indentation, missing list prefix `-`)

**Fix:**
```bash
# The error log tells you the file and line number
# Go to that line in the manifest
vi /etc/kubernetes/manifests/kube-apiserver.yaml

# Check for:
# - Missing '-' before list items in command: section
# - Bad indentation (use 2 spaces, not tabs)
# - Missing ':' after keys
# - Trailing spaces

# Save: ESC then :wq
```

---

### ❌ "unrecognized flag"
**Root Cause:** Invalid command line argument in the manifest

**Example:**
```yaml
- command:
  - kube-apiserver
  - --this-is-very-wrong    # ❌ This flag doesn't exist
  - --valid-flag=value
```

**Fix:**
```bash
# Check kubelet logs
journalctl -u kubelet -e | grep -iE "parse|syntax|manifest"

# Go to the manifest
vi /etc/kubernetes/manifests/kube-apiserver.yaml

# Remove the invalid flag
# Save: ESC then :wq
```

---

### ❌ "certificate verify failed"
**Root Cause:** CA cert path wrong or cert file missing

**Fix:**
```bash
# Check the manifest for cert paths
vi /etc/kubernetes/manifests/kube-apiserver.yaml

# Look for lines like:
# - --client-ca-file=/etc/kubernetes/pki/ca.crt
# - --tls-cert-file=/etc/kubernetes/pki/apiserver.crt
# - --tls-private-key-file=/etc/kubernetes/pki/apiserver.key

# Verify the cert files exist
ls -la /etc/kubernetes/pki/ca.crt
ls -la /etc/kubernetes/pki/apiserver.crt
ls -la /etc/kubernetes/pki/apiserver.key

# If missing, restore from backup or check file paths in manifest
```

---

### ❌ "address already in use"
**Root Cause:** Port 6443 already taken by another process

**Fix:**
```bash
# Check what's using port 6443
sudo netstat -tlnp | grep 6443

# Kill the conflicting process OR change the port in manifest
# To change port, edit the manifest:
vi /etc/kubernetes/manifests/kube-apiserver.yaml
# Find: --secure-port=6443
# Change to: --secure-port=6444
```

---

## WHAT NOT TO DO

❌ **DON'T use complex awk/grep patterns** during exam stress
❌ **DON'T try to remember which log files to check** - use only journalctl
❌ **DON'T assume** which component is broken - let kubectl + journalctl tell you
❌ **DON'T edit manifests without knowing the exact error** - check kubelet logs first

---

## KEY TAKEAWAY

**During CKA under stress, remember only TWO commands:**

```bash
# 1. See if API server responds
kubectl get pods -A

# 2. Check why it's broken (ONLY for parse/syntax errors)
journalctl -u kubelet -e | grep -iE "parse|syntax|manifest"
```

Everything else flows from these two commands!

---

## EXAMPLE: Real CKA Scenario

```bash
# Step 1: Notice kubectl is broken
$ kubectl get pods -A
The connection to the server 172.30.1.2:6443 was refused

# Step 2: Check kubelet logs
$ journalctl -u kubelet -e | grep -iE "parse|syntax|manifest"
E0902 16:48:20.821248 file.go:187] "Could not process manifest file"
err="/etc/kubernetes/manifests/kube-apiserver.yaml: 
couldn't parse as pod(yaml: line 15: could not find expected ':'), 
please check config file"

# Step 3: kubelet tells you it's line 15, check it
$ sed -n '12,17p' /etc/kubernetes/manifests/kube-apiserver.yaml
containers:
- command:
  --this-is-very-wrong      # ← THE PROBLEM (missing '-')
- kube-apiserver
- --advertise-address=172.30.1.2

# Step 4: Fix it (add the '-')
$ vi /etc/kubernetes/manifests/kube-apiserver.yaml
# Change line 14 to: - kube-apiserver
# Save

# Step 5: Verify
$ sleep 5
$ kubectl get pods -A
# ✅ All pods running!
```

---

## REMEMBER

During the exam, you're **NOT** expected to know Kubernetes internals.

You **ARE** expected to:
1. ✅ Try basic commands (kubectl)
2. ✅ Read error messages carefully
3. ✅ Check service logs (journalctl)
4. ✅ Fix obvious YAML syntax errors

That's it! You've got this, HERMANO! 💪
