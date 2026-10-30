# CKA Network Troubleshooting: Missing CNI and Broken kube-proxy

> Lab-based debugging runbook — October 30, 2026.
> Commands and addresses reflect this lab. Verify them before reuse.

## 1. Objective and architecture

Restore the two-tier application without changing its architecture,
resource names, or port numbers.

| Component | Lab value |
|---|---|
| Application namespace | triton |
| Web Deployment | webapp-mysql |
| Database Pod and Service | mysql |
| Web Service | web-service |
| Application port | 8080 |
| NodePort | 30081 |
| Database port | 3306 |
| Node | controlplane |
| Node IP at time of test | 10.244.243.226 |
| Cluster Pod CIDR | 172.17.0.0/16 |
| Node Pod CIDR | 172.17.0.0/24 |
| Service CIDR | 172.20.0.0/16 |

**Success:** the NodePort returns the application's green success page.

## 2. First-response workflow

Start with evidence, not edits.

```bash
kubectl -n triton get pods,deployments,replicasets,services -o wide
kubectl -n triton get events --sort-by=.metadata.creationTimestamp
kubectl get nodes -o wide
kubectl get daemonsets -A
kubectl get pods -A -o wide
```

### Choose the failing layer

| Evidence | Investigate next |
|---|---|
| Pending + FailedScheduling | Node conditions, taints, scheduling constraints |
| NotReady + NetworkPluginNotReady | CNI configuration, binaries, plugin workload |
| kube-proxy Error/CrashLoopBackOff | Proxy logs, command, mounted configuration |
| Application Running but NodePort fails | kube-proxy, Service endpoints, network path |
| HTTP response but application reports DB failure | Database connectivity and application configuration |

Do not treat every application failure as an application configuration problem.

---

# Quiz 1 — Missing network plugin

## 3. Symptoms and evidence

The application Pods were Pending.

The earlier scheduler events reported an untolerated taint.
Node inspection identified:

- `node.kubernetes.io/not-ready:NoSchedule`
- Ready condition: False
- `NetworkPluginNotReady`
- `cni plugin not initialized`

Additional evidence:

- Only kube-proxy appeared in the DaemonSet list.
- No network-plugin Pods appeared across namespaces.
- `/etc/cni/net.d/` contained only `.kubernetes-cni-keep`.
- No Flannel ConfigMap or local Flannel manifest was found.

### Diagnosis

The node lacked usable CNI configuration and its expected Flannel
installation was absent.

Failure chain:

Missing CNI setup
→ runtime reports network not initialized
→ node NotReady
→ not-ready scheduling taint
→ application Pods cannot schedule.

**Do not bypass the health problem by adding application tolerations
or manually removing the not-ready taint.**

## 4. Confirm the network configuration

```bash
kubectl describe node controlplane

ls -la /etc/cni/net.d/
ls -la /opt/cni/bin/

grep -- '--cluster-cidr' \
  /etc/kubernetes/manifests/kube-controller-manager.yaml

kubectl -n kube-system get configmap kubeadm-config -o yaml

kubectl get nodes \
  -o custom-columns=NAME:.metadata.name,PODCIDR:.spec.podCIDR
```

Lab results:

- Controller-manager cluster CIDR: `172.17.0.0/16`
- kubeadm podSubnet: `172.17.0.0/16`
- Node allocation: `172.17.0.0/24`

**Do not configure the entire Flannel network as the node's /24.**
The cluster-wide network is the /16.

## 5. Restore the intended CNI safely

### Preferred procedure

1. Confirm Flannel is the intended plugin.
2. Obtain the lab-provided or an appropriate pinned Flannel manifest.
3. Download it locally for inspection.
4. Set the Flannel ConfigMap's `net-conf.json` Network to:
   `172.17.0.0/16`.
5. Preserve the intended backend and other required settings.
6. Apply the inspected manifest.

```bash
# Assumes an inspected local manifest, already customized for this lab
kubectl apply -f ./kube-flannel.yml

kubectl -n kube-flannel rollout status \
  daemonset/kube-flannel-ds --timeout=180s
```

Do not install an additional CNI alongside an existing intended one
without understanding the cluster design.

### What the transcript actually captured

The lab applied the upstream master manifest directly:

```bash
kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml
```

Afterward:

- `/etc/cni/net.d/10-flannel.conflist` appeared.
- The node reported Ready.

**This is historical evidence, not the preferred installation procedure.**
The transcript does not show the Flannel network being customized
before application.

Later output showed Flannel repeatedly exiting. Its logs were not
captured, so the exact reason remains unconfirmed.

## 6. Verify beyond node readiness

```bash
kubectl get nodes
kubectl get daemonsets -A
kubectl -n kube-flannel get pods -o wide

kubectl -n kube-flannel logs daemonset/kube-flannel-ds \
  -c kube-flannel --tail=80

kubectl -n kube-flannel get configmap kube-flannel-cfg \
  -o jsonpath='{.data.net-conf\.json}'; echo

ls -la /etc/cni/net.d/
kubectl -n triton get pods -o wide
```

### Completion criteria

- Node Ready.
- Flannel desired and ready counts match.
- No continuing Flannel crash loop.
- CNI configuration exists and references available binaries.
- Application Pods become Ready.
- End-to-end application test succeeds.

**A CNI file appearing is progress—not proof that the entire network
is healthy. Its filename is specific to the installed plugin.**

---

# Quiz 2 — kube-proxy configuration filename mismatch

## 7. Symptoms and evidence

The application Pods were Running, but NodePort access failed.

```bash
curl -sS --connect-timeout 5 http://10.244.243.226:30081
```

Captured result: `No route to host`.

Meanwhile kube-proxy showed Error and repeated restarts.

```bash
kubectl -n kube-system get pods -l k8s-app=kube-proxy
kubectl -n kube-system logs -l k8s-app=kube-proxy --tail=80
```

Decisive startup error:

`open /var/lib/kube-proxy/configuration.conf: no such file or directory`

The connection error alone did not identify the cause.
The proxy startup error provided the actionable evidence.

## 8. Trace the configuration inside the container

Follow this chain:

**command → volumeMount → volume source → ConfigMap key**

```bash
# Live command, excluding historical annotation content
kubectl -n kube-system get daemonset kube-proxy \
  -o jsonpath='{.spec.template.spec.containers[?(@.name=="kube-proxy")].command}'; echo

# Container mounts
kubectl -n kube-system get daemonset kube-proxy \
  -o jsonpath='{.spec.template.spec.containers[?(@.name=="kube-proxy")].volumeMounts}'; echo

# Volume sources
kubectl -n kube-system get daemonset kube-proxy \
  -o jsonpath='{.spec.template.spec.volumes}'; echo

# ConfigMap keys
kubectl -n kube-system get configmap kube-proxy \
  -o go-template='{{range $key, $value := .data}}{{printf "%s\n" $key}}{{end}}'

# Proxy behavior configuration
kubectl -n kube-system get configmap kube-proxy \
  -o jsonpath='{.data.config\.conf}'; echo
```

### Findings

| Item | Captured value |
|---|---|
| Requested file | /var/lib/kube-proxy/configuration.conf |
| Mount directory | /var/lib/kube-proxy |
| Volume source | ConfigMap kube-proxy |
| Available keys | config.conf, kubeconfig.conf |
| Explicit key-to-path remapping | None |

**Confirmed cause:** the command requested a filename not supplied
by the mounted ConfigMap.

### Keep the two files distinct

- `config.conf`: `KubeProxyConfiguration`, including behavior settings.
- `kubeconfig.conf`: API-server connection and credentials.

The behavior configuration correctly referenced:

```yaml
clientConnection:
  kubeconfig: /var/lib/kube-proxy/kubeconfig.conf
```

## 9. Correct the owning DaemonSet

Optionally export the current object before editing:

```bash
kubectl -n kube-system get daemonset kube-proxy -o yaml \
  > kube-proxy-before.yaml

kubectl -n kube-system edit daemonset kube-proxy
```

Change only the incorrect argument in the live container command:

```diff
- --config=/var/lib/kube-proxy/configuration.conf
+ --config=/var/lib/kube-proxy/config.conf
```

Preserve the resource names, ports, mounts, and unrelated settings.

Wait for the replacement Pod:

```bash
kubectl -n kube-system rollout status \
  daemonset/kube-proxy --timeout=120s

kubectl -n kube-system get pods -l k8s-app=kube-proxy
kubectl -n kube-system logs -l k8s-app=kube-proxy --tail=80
```

## 10. Verify the actual application path

```bash
kubectl -n triton get pods,services -o wide

kubectl -n triton get endpointslices \
  -l kubernetes.io/service-name=web-service -o wide

curl -sS --connect-timeout 5 http://10.244.243.226:30081
```

### Captured recovery evidence

- DaemonSet rollout completed.
- Desired, updated, ready, and available counts were all 1.
- `observedGeneration: 2` matched `metadata.generation: 2`.
- Curl returned the Flask application's green success HTML.

The final functional request succeeded after correcting the
kube-proxy argument.

This does not independently establish that the earlier Flannel
restart problem was resolved.

---

## 11. Wrong turns to avoid

### Copying the host kubeconfig to the missing filename

The transcript includes this unsuccessful action:

```bash
# DO NOT use this as the repair
cp /var/lib/kube-proxy/kubeconfig.conf \
   /var/lib/kube-proxy/configuration.conf
```

It was incorrect for two reasons:

1. Kubeconfig is not KubeProxyConfiguration.
2. The container directory was backed by a ConfigMap, not that host
   directory.

### Searching only static Pod manifests

kube-proxy was owned by a DaemonSet.
Its repair belonged in the DaemonSet, not
`/etc/kubernetes/manifests/`.

### Restarting application Pods

Restarting webapp-mysql did not correct kube-proxy's missing file.

### Deleting kube-proxy without correcting its template

The DaemonSet recreated a Pod with the same incorrect command.

### Reading last-applied annotations as the current command

The annotation retained the old filename after the repair.
Inspect `spec.template.spec`, not historical annotation JSON.

### Treating every Exited init container as failed

The CNI installers showed Exited and empty logs.
That alone did not prove failure; inspect their exit status if needed.

### Requiring an empty error history

Old errors remain after recovery. Review recent messages:

```bash
journalctl -u kubelet --since "10 minutes ago" --no-pager -l

# Preferred focused filter for the journal format in this lab
journalctl -u kubelet --since "10 minutes ago" --no-pager -l \
  | grep "]: E"
```

Read unfiltered logs too; they include recovery messages.

## 12. CKA memory patterns

**Pending + NotReady → inspect node conditions before application config.**

**Missing file → trace the container's mount source before creating files.**

**Running Pods ≠ working Service ≠ successful application transaction.**

**Fix the cause → verify rollout → test the user-facing endpoint.**
