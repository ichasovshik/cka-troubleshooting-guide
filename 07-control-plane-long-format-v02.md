# CKA Control Plane Failure Runbook (v02)

## Scope

This runbook covers troubleshooting failures in control-plane static Pods:

- `kube-apiserver`
- `kube-controller-manager`
- `kube-scheduler`
- `etcd`

and related symptoms in workloads (Pending pods, scaling not applied, stale reconciliation).

---

## 1) Core model you must remember

Control-plane components are managed as **static Pods** by kubelet from:

`/etc/kubernetes/manifests/*.yaml`

If a manifest is broken, kubelet keeps restarting the affected static Pod.

### Implications
- Fix the **manifest file on node**, not Deployment/ReplicaSet objects.
- `kubectl edit` on these pods won’t persist desired fix.
- A corrected manifest triggers automatic Pod recreation.

---

## 2) Symptom → suspect mapping

| Symptom | Likely component |
|---|---|
| `kubectl` unavailable / API failures | kube-apiserver or etcd |
| New app pods stuck `Pending` | kube-scheduler |
| Scaled deployment but pod count unchanged | kube-controller-manager |
| Cluster state odd/inconsistent | etcd / apiserver |
| “file not found” inside component despite file on host | bad hostPath volume mount |

---

## 3) Fast triage sequence (battle-tested)

```bash
# A. Control plane health
k get pods -n kube-system | grep -E "apiserver|controller|scheduler|etcd"

# B. If pod is CrashLoopBackOff or not ready
k describe pod -n kube-system <pod-name>
k logs -n kube-system <pod-name>

# C. If logs empty, use events (container may fail before start)
k events -n kube-system | tail -60

# D. Inspect static manifests
ls -l /etc/kubernetes/manifests/
cat /etc/kubernetes/manifests/kube-scheduler.yaml
cat /etc/kubernetes/manifests/kube-controller-manager.yaml
```

---

## 4) Deep check for reconciliation issues

When scaling doesn’t materialize:

```bash
k scale deploy app --replicas=2
k get deploy app -o yaml | grep -E "generation|observedGeneration|replicas"
k get rs
```

Interpretation:
- `spec.replicas: 2` with `status.replicas: 1` and
- `metadata.generation: 2` vs `status.observedGeneration: 1`

→ API accepted new desired state, but controller-manager didn’t reconcile it.

---

## 5) Incident case studies from the lab

## Case A: kube-scheduler command typo

### Symptom
- Workload pod stayed Pending
- `kube-scheduler` in CrashLoopBackOff

### Evidence
From events:
`exec: "kube-schedulerrrr": executable file not found in $PATH`

### Root cause
Static manifest typo in `kube-scheduler.yaml` command.

### Fix
Set command binary back to:

```yaml
command:
- kube-scheduler
```

---

## Case B: kube-controller-manager wrong kubeconfig path

### Symptom
- `kubectl scale` succeeded
- Deployment remained at 1/2
- ReplicaSet desired stayed 1

### Evidence
Controller-manager logs:
`stat /etc/kubernetes/controller-manager-XXXX.conf: no such file or directory`

### Root cause
Invalid arg in static manifest:
`--kubeconfig=/etc/kubernetes/controller-manager-XXXX.conf`

### Fix
Use existing file:

```bash
ls -l /etc/kubernetes/controller-manager.conf
```

and set:

```yaml
- --kubeconfig=/etc/kubernetes/controller-manager.conf
```

---

## Case C: `ca.crt` exists on host, missing in container

### Symptom
Controller-manager CrashLoop:
`open /etc/kubernetes/pki/ca.crt: no such file or directory`

### Why this happens
Host file can exist, but container won’t see it unless correct hostPath volume + mountPath are configured.

### Checklist
Ensure all of these are aligned:

- Arg path:
  - `--client-ca-file=/etc/kubernetes/pki/ca.crt`
- Container mount:
  - `volumeMounts[].mountPath: /etc/kubernetes/pki`
- Host source:
  - `volumes[].hostPath.path: /etc/kubernetes/pki`
- Same volume name in `volumes` and `volumeMounts`

---

## 6) Validation after each manifest fix

```bash
# Watch control plane recovery
k get pods -n kube-system -w

# Validate affected workload behavior
k get deploy,rs,pods
```

Success criteria:
- control-plane pod returns to `1/1 Running`
- desired replicas propagate to RS/pods
- Pending pods begin scheduling

---

## 7) Common traps

1. **Checking only logs** when container fails before process start  
   → use `describe` / events.

2. **Assuming HPA/Quota** without evidence  
   → confirm quickly:
   ```bash
   k get hpa
   k get resourcequota -A
   ```

3. **Editing the wrong layer**  
   → static pod issues must be fixed in `/etc/kubernetes/manifests/`.

4. **Confusing startup with steady state**  
   - `0/1 Running` for a few seconds can be normal during restart.
   - wait and re-check.

5. **Assuming scale failure is always controller-manager**  
   - Wrong/missing ConfigMap or Secret can make new replicas fail to start or never become Ready.
   - This can look like “didn’t scale” even when ReplicaSet desired count increased.
   - Verify with:
   ```bash
   k get deploy <name>
   k get rs
   k get pods
   k describe pod <new-pod>
   k logs <new-pod> --previous
   ```
   - Look for:
     - `CreateContainerConfigError`
     - `configmap "<name>" not found`
     - `secret "<name>" not found`
     - readiness/liveness probe failures caused by bad env/config

---

## 8) Minimal command cheat block

```bash
# Status
k get pods -A
k get pods -n kube-system | grep -E "apiserver|controller|scheduler|etcd"

# Component diagnostics
k describe pod -n kube-system kube-scheduler-controlplane
k logs -n kube-system kube-scheduler-controlplane
k logs -n kube-system kube-controller-manager-controlplane
k events -n kube-system | tail -60

# Static manifests
grep -rn "schedulerrrr\|controller-manager-XXXX\|client-ca-file\|kubeconfig" /etc/kubernetes/manifests/*
vi /etc/kubernetes/manifests/kube-scheduler.yaml
vi /etc/kubernetes/manifests/kube-controller-manager.yaml

# Reconciliation signal
k get deploy app -o yaml | grep -E "generation|observedGeneration|replicas"
k get rs
```

---

## 9) Final takeaway

If Deployment scale is accepted but not applied, think:
**“Controller manager reconciliation is broken.”**

If Pods are Pending with no scheduling progress, think:
**“Scheduler is unhealthy or misconfigured.”**

For control plane static pods, always:
**Inspect logs/events → inspect manifest → fix path/command/volume mapping → verify 1/1 Running.**
