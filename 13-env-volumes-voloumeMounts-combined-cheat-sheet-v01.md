# ENV + VOLUMES/VOLUMEMOUNTS COMBINED CHEATSHEET

## QUICK REFERENCE

**env:** Define individual environment variables inside a container  
**envFrom:** Import all keys from ConfigMap/Secret as environment variables  
**valueFrom:** Get env value from ConfigMap/Secret/Downward API/resources  
**volumeMounts:** Where files appear inside container  
**volumes:** Which source provides those files

---

## 1. LITERAL ENV (env + value)

### Use Case
Set fixed values directly in Pod spec

### env Section
```yaml
env:
  - name: APP_MODE
    value: "practice"
  - name: LOG_LEVEL
    value: "info"
```

### Full Pod Example
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-literal-env
spec:
  containers:
  - name: app
    image: busybox:1.36
    command: ["sh", "-c", "env | sort; sleep 3600"]
    env:
      - name: APP_MODE
        value: "practice"
      - name: LOG_LEVEL
        value: "info"
```

### Volume Equivalent
Not applicable directly (literal env has no ConfigMap/Secret source to mount).

---

## 2. CONFIGMAP KEY TO ENV (env + valueFrom + configMapKeyRef)

### Use Case
Inject one specific key from a ConfigMap as one env variable

### env Section
```yaml
env:
  - name: RUN_MODE
    valueFrom:
      configMapKeyRef:
        name: app-env
        key: APP_MODE
```

### Full Pod Example (env)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-cm-key-env
spec:
  containers:
  - name: app
    image: busybox:1.36
    command: ["sh", "-c", "echo RUN_MODE=$RUN_MODE; sleep 3600"]
    env:
      - name: RUN_MODE
        valueFrom:
          configMapKeyRef:
            name: app-env
            key: APP_MODE
```

### Volume Equivalent (same source as file)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-cm-key-volume
spec:
  containers:
  - name: app
    image: busybox:1.36
    command: ["sh", "-c", "cat /etc/app.conf; sleep 3600"]
    volumeMounts:
      - name: app-files
        mountPath: /etc/app.conf
        subPath: app.conf
        readOnly: true
  volumes:
    - name: app-files
      configMap:
        name: app-files
```

### Optional Key Behavior
```yaml
env:
  - name: RUN_MODE
    valueFrom:
      configMapKeyRef:
        name: app-env
        key: APP_MODE
        optional: true
```

---

## 3. SECRET KEY TO ENV (env + valueFrom + secretKeyRef)

### Use Case
Inject one specific secret key as one env variable

### env Section
```yaml
env:
  - name: DB_USER
    valueFrom:
      secretKeyRef:
        name: app-secret
        key: db-user
```

### Full Pod Example (env)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-secret-key-env
spec:
  containers:
  - name: app
    image: busybox:1.36
    command: ["sh", "-c", "echo DB_USER=$DB_USER; sleep 3600"]
    env:
      - name: DB_USER
        valueFrom:
          secretKeyRef:
            name: app-secret
            key: db-user
```

### Volume Equivalent (same source as file)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-secret-key-volume
spec:
  containers:
  - name: app
    image: busybox:1.36
    command: ["sh", "-c", "cat /etc/db-password; sleep 3600"]
    volumeMounts:
      - name: secret-files
        mountPath: /etc/db-password
        subPath: db-password
        readOnly: true
  volumes:
    - name: secret-files
      secret:
        secretName: app-secret
```

### Optional Key Behavior
```yaml
env:
  - name: DB_USER
    valueFrom:
      secretKeyRef:
        name: app-secret
        key: db-user
        optional: true
```

---

## 4. IMPORT ALL CONFIGMAP KEYS (envFrom + configMapRef)

### Use Case
Bring all keys from a ConfigMap into container env

### envFrom Section
```yaml
envFrom:
  - configMapRef:
      name: app-env
```

### Full Pod Example (envFrom)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-envfrom-configmap
spec:
  containers:
  - name: app
    image: busybox:1.36
    command: ["sh", "-c", "env | grep -E 'APP_MODE|LOG_LEVEL' | sort; sleep 3600"]
    envFrom:
      - configMapRef:
          name: app-env
```

### Volume Equivalent (all keys as files)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-configmap-volume
spec:
  containers:
  - name: app
    image: busybox:1.36
    command: ["sh", "-c", "ls -l /etc/config; sleep 3600"]
    volumeMounts:
      - name: app-files
        mountPath: /etc/config
        readOnly: true
  volumes:
    - name: app-files
      configMap:
        name: app-files
```

---

## 5. IMPORT ALL SECRET KEYS (envFrom + secretRef)

### Use Case
Bring all keys from a Secret into container env

### envFrom Section
```yaml
envFrom:
  - secretRef:
      name: app-env-secret
```

### Full Pod Example (envFrom)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-envfrom-secret
spec:
  containers:
  - name: app
    image: busybox:1.36
    command: ["sh", "-c", "env | grep API_TOKEN; sleep 3600"]
    envFrom:
      - secretRef:
          name: app-env-secret
```

### Volume Equivalent (all keys as files)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-secret-volume
spec:
  containers:
  - name: app
    image: busybox:1.36
    command: ["sh", "-c", "ls -l /etc/secret; sleep 3600"]
    volumeMounts:
      - name: secret-files
        mountPath: /etc/secret
        readOnly: true
  volumes:
    - name: secret-files
      secret:
        secretName: app-secret
```

---

## 6. PREFIXED IMPORTS (envFrom + prefix)

### Use Case
Avoid variable name collisions when importing all keys

### envFrom Section
```yaml
envFrom:
  - prefix: CFG_
    configMapRef:
      name: app-env
  - prefix: SEC_
    secretRef:
      name: app-env-secret
```

### Full Pod Example
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-prefixed-envfrom
spec:
  containers:
  - name: app
    image: busybox:1.36
    command: ["sh", "-c", "env | grep -E '^CFG_|^SEC_' | sort; sleep 3600"]
    envFrom:
      - prefix: CFG_
        configMapRef:
          name: app-env
      - prefix: SEC_
        secretRef:
          name: app-env-secret
```

### Expected Variables
- `CFG_APP_MODE`
- `CFG_LOG_LEVEL`
- `SEC_API_TOKEN`

### Volume Equivalent
No direct equivalent for prefixes in file mounts. Prefix behavior is env-only.

---

## 7. MIXED PATTERN (env + envFrom + volume mounts)

### Use Case
Use explicit single-key mapping for critical vars + bulk imports + file mounts

### Full Pod Example
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-mixed-env-and-mounts
spec:
  containers:
  - name: app
    image: busybox:1.36
    command: ["sh", "-c", "env | sort; ls -l /etc/config /etc/secret; sleep 3600"]
    env:
      - name: RUN_MODE
        valueFrom:
          configMapKeyRef:
            name: app-env
            key: APP_MODE
      - name: DB_USER
        valueFrom:
          secretKeyRef:
            name: app-secret
            key: db-user
    envFrom:
      - prefix: CFG_
        configMapRef:
          name: app-env
      - prefix: SEC_
        secretRef:
          name: app-env-secret
    volumeMounts:
      - name: app-files
        mountPath: /etc/config
        readOnly: true
      - name: secret-files
        mountPath: /etc/secret
        readOnly: true
  volumes:
    - name: app-files
      configMap:
        name: app-files
    - name: secret-files
      secret:
        secretName: app-secret
```

---

## 8. DOWNWARD API AS ENV (fieldRef)

### Use Case
Expose Pod metadata as environment variables

### env Section
```yaml
env:
  - name: MY_POD_NAME
    valueFrom:
      fieldRef:
        fieldPath: metadata.name
  - name: MY_POD_NAMESPACE
    valueFrom:
      fieldRef:
        fieldPath: metadata.namespace
```

### Full Pod Example (env)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-downward-env
spec:
  containers:
  - name: app
    image: busybox:1.36
    command: ["sh", "-c", "echo $MY_POD_NAME $MY_POD_NAMESPACE; sleep 3600"]
    env:
      - name: MY_POD_NAME
        valueFrom:
          fieldRef:
            fieldPath: metadata.name
      - name: MY_POD_NAMESPACE
        valueFrom:
          fieldRef:
            fieldPath: metadata.namespace
```

### Volume Equivalent (downwardAPI files)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-downward-volume
spec:
  containers:
  - name: app
    image: busybox:1.36
    command: ["sh", "-c", "cat /etc/podinfo/pod-name /etc/podinfo/pod-namespace; sleep 3600"]
    volumeMounts:
      - name: podinfo
        mountPath: /etc/podinfo
        readOnly: true
  volumes:
    - name: podinfo
      downwardAPI:
        items:
          - path: pod-name
            fieldRef:
              fieldPath: metadata.name
          - path: pod-namespace
            fieldRef:
              fieldPath: metadata.namespace
```

---

## 9. RESOURCE VALUES AS ENV (resourceFieldRef)

### Use Case
Expose container resource requests/limits in env vars

### env Section
```yaml
env:
  - name: CPU_LIMIT
    valueFrom:
      resourceFieldRef:
        containerName: app
        resource: limits.cpu
  - name: MEM_REQUEST
    valueFrom:
      resourceFieldRef:
        containerName: app
        resource: requests.memory
```

### Full Pod Example
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-resource-env
spec:
  containers:
  - name: app
    image: busybox:1.36
    resources:
      requests:
        cpu: "100m"
        memory: "64Mi"
      limits:
        cpu: "500m"
        memory: "128Mi"
    command: ["sh", "-c", "echo CPU_LIMIT=$CPU_LIMIT MEM_REQUEST=$MEM_REQUEST; sleep 3600"]
    env:
      - name: CPU_LIMIT
        valueFrom:
          resourceFieldRef:
            containerName: app
            resource: limits.cpu
      - name: MEM_REQUEST
        valueFrom:
          resourceFieldRef:
            containerName: app
            resource: requests.memory
```

### Volume Equivalent
No direct standard volume equivalent for resourceFieldRef output.

---

## 10. WHERE ARE ENV VARS AND MOUNTED FILES AVAILABLE?

### Container Scope
- Environment variables are available inside container process environment.
- Mounted files are available on container filesystem paths (`mountPath`).

### Verify Inside Container
```bash
kubectl exec -it <pod> -- env | sort
kubectl exec -it <pod> -- printenv RUN_MODE
kubectl exec -it <pod> -- ls -l /etc/config /etc/secret
kubectl exec -it <pod> -- cat /etc/app.conf
```

### Verify via /proc
```bash
kubectl exec -it <pod> -- sh -c "cat /proc/1/environ | tr '\0' '\n' | sort"
```

### Node Scope Clarification
- Env vars are not mounted as files on node by default.
- ConfigMap/Secret volumes are mounted into container filesystem.
- For debugging node-level bind mounts, inspect container runtime mounts on the node (advanced).

---

## QUICK COMPARISON TABLE

| Pattern | Result in Container | Source | Single Key | All Keys | Prefix | File Mount Equivalent |
|---------|----------------------|--------|------------|----------|--------|-----------------------|
| `env + value` | Env var | Literal | N/A | N/A | ❌ | ❌ |
| `env + configMapKeyRef` | Env var | ConfigMap | ✅ | ❌ | ❌ | ✅ (`subPath`) |
| `env + secretKeyRef` | Env var | Secret | ✅ | ❌ | ❌ | ✅ (`subPath`) |
| `envFrom + configMapRef` | Env vars | ConfigMap | ❌ | ✅ | ✅ | ✅ (dir mount) |
| `envFrom + secretRef` | Env vars | Secret | ❌ | ✅ | ✅ | ✅ (dir mount) |
| `env + fieldRef` | Env var | Pod metadata | Field path | N/A | ❌ | ✅ (downwardAPI volume) |
| `env + resourceFieldRef` | Env var | Container resources | Resource path | N/A | ❌ | ❌ |

---

## COMMON PATTERNS

### Pattern 1: Explicit Critical Vars + Mounted Files
```yaml
env:
  - name: DB_USER
    valueFrom:
      secretKeyRef:
        name: app-secret
        key: db-user
volumeMounts:
  - name: secret-files
    mountPath: /etc/secret
    readOnly: true
volumes:
  - name: secret-files
    secret:
      secretName: app-secret
```

### Pattern 2: Full Imports + Prefixes + Full File Mounts
```yaml
envFrom:
  - prefix: CFG_
    configMapRef:
      name: app-env
  - prefix: SEC_
    secretRef:
      name: app-env-secret
volumeMounts:
  - name: app-files
    mountPath: /etc/config
    readOnly: true
volumes:
  - name: app-files
    configMap:
      name: app-files
```

### Pattern 3: Single File via subPath (your style)
```yaml
volumeMounts:
  - name: app-files
    mountPath: /etc/app.conf
    subPath: app.conf
    readOnly: true
volumes:
  - name: app-files
    configMap:
      name: app-files
```

---

## CKA EXAM TIPS

✅ **Most Tested:**
1. `env` literal values
2. `configMapKeyRef` / `secretKeyRef`
3. `envFrom` with prefixes
4. ConfigMap/Secret volume mounts
5. `subPath` single-file mounts

✅ **Likely Scenarios:**
- Same source used as both env and mounted files
- Single key as env + full source mounted for app config files
- Prefix imports to avoid collisions

❌ **Common Mistakes:**
- Putting `envFrom` inside an `env` item
- Missing `key` in keyRef
- `configMaps` (wrong) instead of `configMap`
- Wrong indentation in `volumeMounts`
- `subPath` file mounted to directory path by mistake

✅ **Remember:**
- `env`/`envFrom` = process environment
- `volumes`/`volumeMounts` = filesystem files
- `subPath` = mount one selected file/path from a volume

---

## QUICK COPY-PASTE TEMPLATES

### ConfigMap key as env + same key as file
```yaml
env:
  - name: RUN_MODE
    valueFrom:
      configMapKeyRef:
        name: app-env
        key: APP_MODE
volumeMounts:
  - name: app-files
    mountPath: /etc/app.conf
    subPath: app.conf
    readOnly: true
volumes:
  - name: app-files
    configMap:
      name: app-files
```

### Secret key as env + same key as file
```yaml
env:
  - name: DB_USER
    valueFrom:
      secretKeyRef:
        name: app-secret
        key: db-user
volumeMounts:
  - name: secret-files
    mountPath: /etc/db-password
    subPath: db-password
    readOnly: true
volumes:
  - name: secret-files
    secret:
      secretName: app-secret
```

### envFrom + directory mount
```yaml
envFrom:
  - prefix: CFG_
    configMapRef:
      name: app-env
volumeMounts:
  - name: app-files
    mountPath: /etc/config
    readOnly: true
volumes:
  - name: app-files
    configMap:
      name: app-files
```

**THIS IS EXAM-READY, HERMANO!** 💪
