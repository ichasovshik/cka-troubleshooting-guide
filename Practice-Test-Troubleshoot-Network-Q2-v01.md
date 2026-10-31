**Hermano, the fix was simple: kube-proxy was told to open `configuration.conf`, but its configuration file was named `config.conf`. We corrected the command—not the file.**

I reviewed the Quiz 2 section. Here is the same investigation in **CKA style: one question, one command, one conclusion at a time.** No repository changes made.

## Starting point — understand the error

> `open /var/lib/kube-proxy/configuration.conf: no such file or directory`

**Meaning:** kube-proxy tried to open this file **inside its container** and could not find it.

At this point, we do **not** create a file. We ask:

**“Who told kube-proxy to use this filename, and where should the file come from?”**

---

## Step 1 — find who manages kube-proxy

```bash name=01-find-kube-proxy.sh
kubectl -n kube-system get ds
```

In our lab, the output included the **`kube-proxy` DaemonSet**.

**Conclusion:** inspect and fix the DaemonSet. Searching `/etc/kubernetes/manifests/` was the wrong direction for this component.

---

## Step 2 — inspect its command and mounts

```bash name=02-inspect-kube-proxy.sh
kubectl -n kube-system get ds kube-proxy -o yaml
```

Inside **`spec.template.spec`**, we found three connected pieces:

| What we inspected | What it said | Meaning |
|---|---|---|
| Container `command` | `--config=/var/lib/kube-proxy/configuration.conf` | This tells kube-proxy which file to open. |
| Container `volumeMounts` | Mount `/var/lib/kube-proxy` using volume `kube-proxy` | This supplies the container directory. |
| Pod `volumes` | Volume `kube-proxy` comes from ConfigMap `kube-proxy` | The files come from that ConfigMap. |

**Conclusion:** look in the **ConfigMap**, not in the host directory. Kubernetes connects a volume source to a container through `volumes` and `volumeMounts`. ([kubernetes.io](https://kubernetes.io/docs/concepts/storage/volumes/?q=configmap))

**Important:** ignore the large `last-applied-configuration` annotation at the top. We need the **live container command** under `spec.template.spec`.

---

## Step 3 — check the filenames supplied by the ConfigMap

Use a simple command:

```bash name=03-inspect-configmap.sh
kubectl -n kube-system get cm kube-proxy -o yaml
```

Under **`data:`**, our ConfigMap contained:

- **`config.conf`**
- **`kubeconfig.conf`**

It did **not** contain `configuration.conf`.

When mounted this way, ConfigMap keys become filenames in the container directory. Our volume had no mapping that changed those names. ([kubernetes.io](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/))

### Now the mismatch is clear

| kube-proxy requests | ConfigMap supplies |
|---|---|
| `/var/lib/kube-proxy/configuration.conf` | `/var/lib/kube-proxy/config.conf` |

**Diagnosis: wrong filename in the startup command.**

### Why not use `kubeconfig.conf`?

Our ConfigMap showed that:

- **`config.conf`** contained `kind: KubeProxyConfiguration` — the configuration kube-proxy needed at startup.
- That configuration referenced **`kubeconfig.conf`** for its API-server connection.

**They have different jobs. Do not copy one over the other.**

---

## Step 4 — change only the incorrect argument

```bash name=04-edit-daemonset.sh
kubectl -n kube-system edit ds kube-proxy
```

Find the container **`command` under `spec.template.spec`**.

Change this one line:

```diff name=kube-proxy-command.diff
- --config=/var/lib/kube-proxy/configuration.conf
+ --config=/var/lib/kube-proxy/config.conf
```

Save and exit.

**What we changed:** the filename kube-proxy requests.  
**What we left unchanged:** the ConfigMap, application, Services, and ports.

---

## Step 5 — verify recovery

```bash name=05-verify-recovery.sh
kubectl -n kube-system rollout status ds/kube-proxy --timeout=120s
kubectl -n kube-system get pods -l k8s-app=kube-proxy

curl -sS --connect-timeout 5 http://10.244.243.226:30081
```

**Your captured results:**
- DaemonSet successfully rolled out.
- kube-proxy became ready.
- Curl returned the Flask application's **green success page**.

**That confirmed the repair for this failure.**

---

## Why the earlier attempts did not work

| Attempt | Why it did not fix this problem |
|---|---|
| Copy `kubeconfig.conf` to `configuration.conf` on the host | Wrong file type, and the container directory came from a ConfigMap—not that host directory. |
| Restart the web application | The incorrect command belonged to kube-proxy. |
| Delete the kube-proxy Pod | The DaemonSet recreated it with the same incorrect command. |

My earlier host-file copy advice was wrong. **The correct action was to inspect the mount source and correct the DaemonSet argument.**

### CKA memory rule

**Missing file → inspect command → inspect mount source → compare filenames → fix → test.**

For this lab:

**Requested `configuration.conf` → supplied `config.conf` → change `--config` → application works.**

### Want me to…
<!-- copilot:suggestions -->
1. Hand this clearer Quiz 2 explanation off to the coding agent to open a pull request in `ichasovshik/cka-troubleshooting-guide`, leaving Quiz 1 unchanged.
