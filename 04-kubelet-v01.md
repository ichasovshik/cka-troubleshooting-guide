> **WORKING DRAFT — incomplete; this document MUST be reviewed and updated.**

# KUBELET CHEAT SHEET (v01)

Based on `04-kubelet.md`, with notes from the October 30, 2026 worker-node troubleshooting lab. The original document is preserved. This is an interim working document, not the final polished guide.

## WHAT IS KUBELET?

The Kubernetes Node Agent runs on worker and control-plane nodes.
- Manages Pods and containers through the container runtime and reports node status to the API server.
- If stopped, node heartbeats stop and node health reporting is affected. Existing containers can continue running; their presence does not prove kubelet is healthy.
- A running kubelet process does not by itself prove API connectivity or node readiness.

## SYSTEMD COMMANDS

Run on the affected node, using root or sudo as needed:

```bash
systemctl status kubelet --no-pager -l # Check status without truncation
systemctl start kubelet               # Start a stopped service
systemctl restart kubelet             # Restart after configuration changes
systemctl stop kubelet                # Stop it (not a repair step)
systemctl enable kubelet              # Enable on boot; does not start it now
```

### Interpret service state

| State | Interpretation | Next step |
|---|---|---|
| `inactive (dead)`, `0/SUCCESS` | Service is stopped; does not identify who or what stopped it | Start it, then inspect fresh logs |
| `activating (auto-restart)`, `1/FAILURE` | Process repeatedly exits with an error | Inspect the current startup error before changing files |
| `active (running)` | Process is running, but may still be unable to reach the API server | Check fresh logs and Kubernetes node conditions |

## CHECK KUBELET STATUS

```bash
# Is it running?
systemctl status kubelet --no-pager -l

# Full recent logs, without pager or truncated fields
journalctl -u kubelet -n 50 --no-pager -l

# Preferred error filter for the journal format seen in this lab
journalctl -u kubelet | cat | grep "]: E"

# Focus on recent errors using the same pattern
journalctl -u kubelet --since "10 minutes ago" --no-pager -l | grep "]: E"

# Follow all messages in real time
journalctl -u kubelet -f
```

The pattern `"]: E"` matches the error prefix after the process identifier in the journal output shown in the lab, including `E1019` and `E1030`. Unlike `grep "E0"`, it is not restricted to dates beginning with zero. It depends on that output format and is not a universal filter for every journal/log format.

Always inspect unfiltered recent logs too: filtering only errors hides registration, startup, and recovery messages. `cat` is retained in the preferred command; `--no-pager` is an alternative when piping directly to grep.

## KUBELET CONFIG FILES

| File or setting | Purpose |
|---|---|
| `/var/lib/kubelet/config.yaml` | Kubelet behavior settings, including `authentication.x509.clientCAFile` |
| `/etc/kubernetes/kubelet.conf` | API-server endpoint and kubelet client credentials |
| `/etc/kubernetes/bootstrap-kubelet.conf` | Bootstrap kubeconfig when needed; may not remain after bootstrap |
| `/var/lib/kubelet/kubeadm-flags.env` | Kubeadm-generated kubelet flags |
| Systemd unit/drop-ins | Service command, environment, and overrides |

Inspect the effective service definition rather than assuming a single drop-in location:

```bash
systemctl cat kubelet
systemctl status kubelet --no-pager -l
```

The lab showed the `10-kubeadm.conf` drop-in under `/usr/lib/systemd/system/kubelet.service.d/`; local overrides may be under `/etc/systemd/system/kubelet.service.d/`.

## KEY CONFIG OPTIONS — KEEP THE LAYERS SEPARATE

These are partial examples, not replacement configuration files. Preserve other existing settings and credentials.

### `/var/lib/kubelet/config.yaml`

```yaml
authentication:
  x509:
    clientCAFile: /etc/kubernetes/pki/ca.crt
```

Other kubelet settings include `clusterDNS` and `clusterDomain`; verify their values against the actual cluster, not a copied example.

### `/etc/kubernetes/kubelet.conf`

The endpoint belongs under the appropriate `clusters[].cluster.server` entry:

```yaml
clusters:
- cluster:
    server: https://controlplane:6443
```

This endpoint is the verified value for this lab, not a universal hostname or port requirement.

### Service arguments

- `--config=/var/lib/kubelet/config.yaml` selects the kubelet configuration.
- `--kubeconfig=/etc/kubernetes/kubelet.conf` selects the API connection and client credentials.

## COMMON ERRORS & FIXES

### "unable to load client CA file"

```bash
grep -n 'clientCAFile:' /var/lib/kubelet/config.yaml
ls -l /etc/kubernetes/pki/ca.crt
```

Back up the configuration, correct the existing `authentication.x509.clientCAFile` path to the verified file, then restart kubelet. Do not regenerate certificates when only the configured path is wrong.

### "connection refused" to API server

```bash
grep -n 'server:' /etc/kubernetes/kubelet.conf
```

Compare the endpoint with a working control-plane configuration. In this lab, `6553` was incorrect and `6443` was correct. Connection refusal alone does not establish that a typo is the cause: also consider the API listener and connectivity if the endpoint is correct.

### Container runtime unavailable

Check runtime status and current logs before deciding on a repair:

```bash
systemctl status containerd --no-pager -l
journalctl -u containerd -n 50 --no-pager -l
```

If containerd is simply stopped and no other blocker is identified:

```bash
systemctl start containerd
```

## DIAGNOSTIC WORKFLOW

1. From the control plane: `kubectl get nodes` and `kubectl describe node node01`.
2. SSH to the affected node.
3. Check `systemctl status kubelet --no-pager -l`.
4. Read recent, unfiltered kubelet logs; use `grep "]: E"` for focused error review.
5. Follow the evidence to the correct configuration file. Do not investigate CNI merely because old init containers show `Exited`.
6. Back up the affected file before editing. For example:
   ```bash
   cp -p /var/lib/kubelet/config.yaml /var/lib/kubelet/config.yaml.bak
   # Or, when repairing the API endpoint:
   cp -p /etc/kubernetes/kubelet.conf /etc/kubernetes/kubelet.conf.bak
   ```
7. Start a stopped kubelet, or restart it after correcting its configuration.
8. Check fresh service logs and verify node readiness from the control plane.

## QUICK RECOVERY CHECK

On the node:

```bash
systemctl status kubelet --no-pager -l
journalctl -u kubelet --since "2 minutes ago" --no-pager -l
```

From the control plane:

```bash
kubectl get nodes -w
kubectl describe node node01
kubectl get pods -A -o wide --field-selector spec.nodeName=node01
```

Success requires more than `active (running)`: confirm node readiness and affected workload behavior. Historical errors can remain in the journal after recovery.

## COMMON TRAPS AND NOTES FOR THE NEXT REVISION

- **Enabled is not running.** Q.1 showed an enabled but inactive service.
- **Running is not Ready.** Q.3 showed a running kubelet using an incorrect API endpoint.
- **Use fresh timestamps.** Early bootstrap errors do not establish the current failure.
- **Read the underlying error.** `reconstruct.go` reported a failed operation, but the actionable cause was connection refusal to the configured endpoint.
- **Do not confuse configuration layers.** CA-path repair belonged in `config.yaml`; API-port repair belonged in `kubelet.conf`.
- **Do not infer CNI failure from `Exited`.** Init containers may have completed successfully; inspect exit status if relevant.
- **`crictl logs` takes one container ID.** In this lab, `--name=install-cni` returned both `install-cni` and `install-cni-plugin`; passing both IDs to one invocation printed usage. Inspect them individually or loop:
  ```bash
  for id in $(crictl ps -a --name=install-cni -q); do
    echo "=== $id ==="
    crictl logs "$id"
  done
  ```
- **Do not infer exact recovery duration.** The node-status snapshots were taken apart; they establish eventual recovery, not how long recovery took.
- **Lab evidence limits.** Q.1 and Q.2 show successful registration after repair; Q.2 and Q.3 also include subsequent `Ready` snapshots. Not every repair command was captured in the transcript.

---

# raw -material for an update

The following supplied notes are retained as source material for a future revision.

## REAL LAB: THREE KUBELET FAILURE PATTERNS

### 1. Kubelet stopped

**Evidence**
- `Active: inactive (dead)`
- `code=exited, status=0/SUCCESS`

**Interpretation**
The service is stopped. This alone does not establish who stopped it.
`enabled` means enabled at boot—not currently running.

**On the affected node**
```bash
sudo systemctl start kubelet
sudo systemctl status kubelet --no-pager -l
sudo journalctl -u kubelet --since "2 minutes ago" --no-pager -l
```

**Observed result:** kubelet became active and registered node01.

### 2. Kubelet repeatedly exits: incorrect CA path

**Evidence**
- `activating (auto-restart)`
- `status=1/FAILURE`
- `unable to load client CA file`
- `/etc/kubernetes/pki/WRONG-CA-FILE.crt` does not exist

**Find the setting**
```bash
grep -n 'clientCAFile:' /var/lib/kubelet/config.yaml
ls -l /etc/kubernetes/pki/ca.crt
```

**Fix**
Back up `/var/lib/kubelet/config.yaml`, then correct the existing
`authentication.x509.clientCAFile` value to the verified CA path.

In this lab:
`/etc/kubernetes/pki/ca.crt`

Restart kubelet and inspect fresh logs:
```bash
sudo systemctl restart kubelet
sudo journalctl -u kubelet --since "2 minutes ago" --no-pager -l
```

**Observed result:** kubelet started and successfully registered node01.
Do not regenerate certificates when the fault is only an incorrect path.

### 3. Kubelet running but cannot reach the API server

**Evidence**
- `Active: active (running)`
- Requests go to `https://controlplane:6553`
- `connect: connection refused`

**Locate the endpoint**
```bash
grep -n 'server:' /etc/kubernetes/kubelet.conf
```

**Fix**
Confirm the actual API endpoint from a working control-plane configuration.
Back up the kubeconfig, then correct only the incorrect endpoint.

In this lab:
`https://controlplane:6553` → `https://controlplane:6443`

```bash
sudo systemctl restart kubelet
sudo journalctl -u kubelet --since "2 minutes ago" --no-pager -l
```

**Observed result:** successful registration after restart; node01 later
reported Ready.

`active (running)` alone does not prove that kubelet can communicate
with the API server.

## VERIFY FROM THE CONTROL PLANE

```bash
kubectl get nodes -w
kubectl describe node node01
kubectl get pods -A -o wide --field-selector spec.nodeName=node01
```

Check both the local service and Kubernetes node readiness.
Do not infer an exact recovery duration from snapshots taken far apart.


════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════

════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════

════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════


════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
══════════════════════════════════════════════ Q.1 ═════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
node01 ~ ➜  systemctl status kubelet
○ kubelet.service - kubelet: The Kubernetes Node Agent
     Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
    Drop-In: /usr/lib/systemd/system/kubelet.service.d
             └─10-kubeadm.conf
     Active: inactive (dead) since Fri 2026-10-30 17:04:13 UTC; 12min ago
       Docs: https://kubernetes.io/docs/
    Process: 1918 ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_CONFIG_ARGS $KUBELET_KUBEADM_ARGS $KUBELET_EXTRA_AR>
   Main PID: 1918 (code=exited, status=0/SUCCESS)
        CPU: 7.651s


1. The strongest evidence
Your service status says:

Active: inactive (dead) since 17:04:13
status=0/SUCCESS — the process exited successfully, rather than reporting a crash.
enabled — configured to start at boot, but not currently running.
This confirms a stopped service; it does not tell us who or what stopped it.



node01 ~ ➜  systemctl status kubelet
● kubelet.service - kubelet: The Kubernetes Node Agent
     Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
    Drop-In: /usr/lib/systemd/system/kubelet.service.d
             └─10-kubeadm.conf
     Active: active (running) since Fri 2026-10-30 17:17:09 UTC; 3s ago
       Docs: https://kubernetes.io/docs/
   Main PID: 17647 (kubelet)
      Tasks: 23 (limit: 75787)
     Memory: 24.4M
        CPU: 181ms
     CGroup: /system.slice/kubelet.service
             └─17647 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/k>
			 

════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════

Oct 30 17:17:10 node01 kubelet[17647]: I1030 17:17:10.070559   17647 kubelet_node_status.go:123] "Node was previously registered" >
Oct 30 17:17:10 node01 kubelet[17647]: I1030 17:17:10.070625   17647 kubelet_node_status.go:77] "Successfully registered node" nod>
Oct 30 17:17:10 node01 kubelet[17647]: I1030 17:17:10.862561   17647 apiserver.go:52] "Watching apiserver"
Oct 30 17:17:10 node01 kubelet[17647]: I1030 17:17:10.872646   17647 desired_state_of_world_populator.go:154] "Finished populating>
Oct 30 17:17:10 node01 kubelet[17647]: I1030 17:17:10.877662   17647 reconciler_common.go:251] "operationExecutor.VerifyController>
Oct 30 17:17:10 node01 kubelet[17647]: I1030 17:17:10.877708   17647 reconciler_common.go:251] "operationExecutor.VerifyController>
Oct 30 17:17:10 node01 kubelet[17647]: I1030 17:17:10.877731   17647 reconciler_common.go:251] "operationExecutor.VerifyController>
Oct 30 17:17:10 node01 kubelet[17647]: I1030 17:17:10.877743   17647 reconciler_common.go:251] "operationExecutor.VerifyController>
Oct 30 17:17:10 node01 kubelet[17647]: I1030 17:17:10.877758   17647 reconciler_common.go:251] "operationExecutor.VerifyController>
Oct 30 17:17:10 node01 kubelet[17647]: I1030 17:17:10.877805   17647 reconciler_common.go:251] "operationExecutor.VerifyController>

node01 ~ ➜  

node01 ~ ➜  


════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
══════════════════════════════════════════════ Q.2 ═════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════



node01 ~ ✖ systemctl status kubelet
● kubelet.service - kubelet: The Kubernetes Node Agent
     Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
    Drop-In: /usr/lib/systemd/system/kubelet.service.d
             └─10-kubeadm.conf
     Active: activating (auto-restart) (Result: exit-code) since Fri 2026-10-30 17:20:05 UTC; 681ms ago
       Docs: https://kubernetes.io/docs/
    Process: 19235 ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_CONFIG_ARGS $KUBELET_KUBEADM_ARGS $KUBELET_EXTRA_A>
   Main PID: 19235 (code=exited, status=1/FAILURE)
        CPU: 41ms

node01 ~ ✖ 

node01 ~ ✖ crictl ps -a
CONTAINER           IMAGE               CREATED             STATE               NAME                 ATTEMPT             POD ID              POD                     NAMESPACE
639e45e4cde05       01cdfa8dd262f       36 minutes ago      Running             kube-flannel         0                   cb762959d4e06       kube-flannel-ds-dl8w6   kube-flannel
06c4aea6af92b       01cdfa8dd262f       36 minutes ago      Exited              install-cni          0                   cb762959d4e06       kube-flannel-ds-dl8w6   kube-flannel
abbaee67e43e2       a55d1bad692b7       36 minutes ago      Exited              install-cni-plugin   0                   cb762959d4e06       kube-flannel-ds-dl8w6   kube-flannel
a4adeb6019e15       32652ff1bbe6b       36 minutes ago      Running             kube-proxy           0                   53c14c9311dd8       kube-proxy-72cfq        kube-system

node01 ~ ➜  crictl ps -a --name=install-cni 
CONTAINER           IMAGE               CREATED             STATE               NAME                 ATTEMPT             POD ID              POD                     NAMESPACE
06c4aea6af92b       01cdfa8dd262f       37 minutes ago      Exited              install-cni          0                   cb762959d4e06       kube-flannel-ds-dl8w6   kube-flannel
abbaee67e43e2       a55d1bad692b7       37 minutes ago      Exited              install-cni-plugin   0                   cb762959d4e06       kube-flannel-ds-dl8w6   kube-flannel

node01 ~ ➜  crictl ps -a --name=install-cni -q
06c4aea6af92b95ae1b2a902ec767cff026592a0cd2519ca7d25e54e697494a5
abbaee67e43e280d7f657717ee82b9ca301d5d3c43e5773a9474c575443346a4

node01 ~ ➜  crictl logs 06c4aea6af92b95ae1b2a902ec767cff026592a0cd2519ca7d25e54e697494a5

node01 ~ ➜  crictl logs abbaee67e43e280d7f657717ee82b9ca301d5d3c43e5773a9474c575443346a4

node01 ~ ➜  journalctl -u kubelet
t: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Oct 30 17:22:08 node01 kubelet[20524]: E1030 17:22:08.462775   20524 run.go:72] "command failed" err="failed to construct kubelet dependencies: unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Oct 30 17:22:18 node01 kubelet[20607]: E1030 17:22:18.712310   20607 run.go:72] "command failed" err="failed to construct kubelet dependencies: unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Oct 30 17:22:28 node01 kubelet[20690]: E1030 17:22:28.964418   20690 run.go:72] "command failed" err="failed to construct kubelet dependencies: unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"

        

node01 ~ ✖ grep WRONG -rn /var/lib/kubelet/
/var/lib/kubelet/config.yaml:9:    clientCAFile: /etc/kubernetes/pki/WRONG-CA-FILE.crt

node01 ~ ➜  ls -l /etc/kubernetes/pki
total 4
-rw-r--r-- 1 root root 1107 Oct 30 16:43 ca.crt

node01 ~ ➜  vi /var/lib/kubelet/config.yaml

node01 ~ ➜  

node01 ~ ➜  

node01 ~ ➜  

node01 ~ ➜  systemctl status kubelet
● kubelet.service - kubelet: The Kubernetes Node Agent
     Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
    Drop-In: /usr/lib/systemd/system/kubelet.service.d
             └─10-kubeadm.conf
     Active: active (running) since Fri 2026-10-30 17:25:02 UTC; 4s ago
       Docs: https://kubernetes.io/docs/
   Main PID: 22199 (kubelet)
      Tasks: 22 (limit: 75787)
     Memory: 24.0M
        CPU: 177ms
     CGroup: /system.slice/kubelet.service
             └─22199 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/k>

Oct 30 17:25:02 node01 kubelet[22199]: I1030 17:25:02.961219   22199 kubelet_node_status.go:123] "Node was previously registered" >
Oct 30 17:25:02 node01 kubelet[22199]: I1030 17:25:02.961275   22199 kubelet_node_status.go:77] "Successfully registered node" nod>
Oct 30 17:25:03 node01 kubelet[22199]: I1030 17:25:03.738310   22199 apiserver.go:52] "Watching apiserver"
Oct 30 17:25:03 node01 kubelet[22199]: I1030 17:25:03.747084   22199 desired_state_of_world_populator.go:154] "Finished populating>
Oct 30 17:25:03 node01 kubelet[22199]: I1030 17:25:03.752081   22199 reconciler_common.go:251] "operationExecutor.VerifyController>
Oct 30 17:25:03 node01 kubelet[22199]: I1030 17:25:03.752109   22199 reconciler_common.go:251] "operationExecutor.VerifyController>
Oct 30 17:25:03 node01 kubelet[22199]: I1030 17:25:03.752131   22199 reconciler_common.go:251] "operationExecutor.VerifyController>
Oct 30 17:25:03 node01 kubelet[22199]: I1030 17:25:03.752155   22199 reconciler_common.go:251] "operationExecutor.VerifyController>
Oct 30 17:25:03 node01 kubelet[22199]: I1030 17:25:03.752214   22199 reconciler_common.go:251] "operationExecutor.VerifyController>
Oct 30 17:25:03 node01 kubelet[22199]: I1030 17:25:03.752279   22199 reconciler_common.go:251] "operationExecutor.VerifyController>

node01 ~ ➜  


////////////////////////////\\\\\\\\\\\\\\\\\\\\\\\\\\\\///////////////////////////////\\\\\\\\\\\\\\\\
//
//				Output of the termianals T1
//
////////////////////////////\\\\\\\\\\\\\\\\\\\\\\\\\\\\///////////////////////////////\\\\\\\\\\\\\\\\


controlplane ~ ➜  k get nodes
NAME           STATUS     ROLES           AGE   VERSION
controlplane   Ready      control-plane   36m   v1.35.0
node01         NotReady   <none>          36m   v1.35.0

controlplane ~ ➜  

controlplane ~ ➜  k get nodes
NAME           STATUS   ROLES           AGE   VERSION
controlplane   Ready    control-plane   42m   v1.35.0
node01         Ready    <none>          41m   v1.35.0


════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════


════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
══════════════════════════════════════════════ Q.3 ═════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════

node01 ~ ➜  systemctl status kubelet
● kubelet.service - kubelet: The Kubernetes Node Agent
     Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
    Drop-In: /usr/lib/systemd/system/kubelet.service.d
             └─10-kubeadm.conf
     Active: active (running) since Fri 2026-10-30 17:27:02 UTC; 17min ago
       Docs: https://kubernetes.io/docs/
   Main PID: 23168 (kubelet)
      Tasks: 23 (limit: 75787)
     Memory: 32.6M
        CPU: 15.945s
     CGroup: /system.slice/kubelet.service
             └─23168 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --conf>

Oct 30 17:44:55 node01 kubelet[23168]: E1030 17:44:55.760198   23168 reconstruct.go:188] "Failed to get Node status to reconstruct device paths" err>
Oct 30 17:44:55 node01 kubelet[23168]: E1030 17:44:55.862533   23168 reconstruct.go:188] "Failed to get Node status to reconstruct device paths" err>


node01 ~ ➜  grep 6553 -rn /etc/kubernetes/
/etc/kubernetes/kubelet.conf:5:    server: https://controlplane:6553

node01 ~ ➜  vi /etc/kubernetes/kubelet.conf

node01 ~ ➜  systemctl restart kubelet

node01 ~ ➜  systemctl status kubelet
● kubelet.service - kubelet: The Kubernetes Node Agent
     Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
    Drop-In: /usr/lib/systemd/system/kubelet.service.d
             └─10-kubeadm.conf
     Active: active (running) since Fri 2026-10-30 17:48:19 UTC; 12s ago
       Docs: https://kubernetes.io/docs/
   Main PID: 31985 (kubelet)
      Tasks: 23 (limit: 75787)
     Memory: 24.6M
        CPU: 231ms
     CGroup: /system.slice/kubelet.service
             └─31985 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --conf>

Oct 30 17:48:19 node01 kubelet[31985]: I1030 17:48:19.468030   31985 kubelet_node_status.go:123] "Node was previously registered" node="node01"
Oct 30 17:48:19 node01 kubelet[31985]: I1030 17:48:19.468083   31985 kubelet_node_status.go:77] "Successfully registered node" node="node01"
Oct 30 17:48:20 node01 kubelet[31985]: I1030 17:48:20.256821   31985 apiserver.go:52] "Watching apiserver"
Oct 30 17:48:20 node01 kubelet[31985]: I1030 17:48:20.267920   31985 desired_state_of_world_populator.go:154] "Finished populating initial desired s>
Oct 30 17:48:20 node01 kubelet[31985]: I1030 17:48:20.273792   31985 reconciler_common.go:251] "operationExecutor.VerifyControllerAttachedVolume sta>
Oct 30 17:48:20 node01 kubelet[31985]: I1030 17:48:20.273812   31985 reconciler_common.go:251] "operationExecutor.VerifyControllerAttachedVolume sta>
Oct 30 17:48:20 node01 kubelet[31985]: I1030 17:48:20.273823   31985 reconciler_common.go:251] "operationExecutor.VerifyControllerAttachedVolume sta>
Oct 30 17:48:20 node01 kubelet[31985]: I1030 17:48:20.273832   31985 reconciler_common.go:251] "operationExecutor.VerifyControllerAttachedVolume sta>
Oct 30 17:48:20 node01 kubelet[31985]: I1030 17:48:20.273849   31985 reconciler_common.go:251] "operationExecutor.VerifyControllerAttachedVolume sta>
Oct 30 17:48:20 node01 kubelet[31985]: I1030 17:48:20.273947   31985 reconciler_common.go:251] "operationExecutor.VerifyControllerAttachedVolume sta>

node01 ~ ➜  ^C

node01 ~ ✖ grep 6443 -rn /etc/kubernetes/
/etc/kubernetes/kubelet.conf:5:    server: https://controlplane:6443

node01 ~ ➜  


////////////////////////////\\\\\\\\\\\\\\\\\\\\\\\\\\\\///////////////////////////////\\\\\\\\\\\\\\\\
//
//				Output of the termianals T1
//
////////////////////////////\\\\\\\\\\\\\\\\\\\\\\\\\\\\///////////////////////////////\\\\\\\\\\\\\\\\
controlplane ~ ➜  k get nodes
NAME           STATUS     ROLES           AGE   VERSION
controlplane   Ready      control-plane   51m   v1.35.0
node01         NotReady   <none>          50m   v1.35.0

controlplane ~ ➜  k get nodes
NAME           STATUS   ROLES           AGE   VERSION
controlplane   Ready    control-plane   70m   v1.35.0
node01         Ready    <none>          69m   v1.35.0

controlplane ~ ➜  




