# ENV & ENVFROM CHEATSHEET

## QUICK REFERENCE

**env:** Define individual environment variables inside a container  
**envFrom:** Import all keys from ConfigMap/Secret as environment variables  
**valueFrom:** Get env value from Kubernetes object fields or resources

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

### Full Pod Example
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

### Full Pod Example
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

### Full Pod Example
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

### Full Pod Example
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

---

## 7. MIXED PATTERN (env + envFrom)

### Use Case
Use explicit single-key mapping for critical vars + bulk imports for convenience

### Full Pod Example
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-mixed-env
spec:
  containers:
  - name: app
    image: busybox:1.36
    command: ["sh", "-c", "env | sort; sleep 3600"]
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

### Full Pod Example
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-downward-env
  labels:
    app: demo
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

---

## 10. WHERE ARE ENV VARS AVAILABLE?

### Container Scope
Environment variables are available **inside the container process environment**.

### Verify Inside Container
```bash
kubectl exec -it <pod> -- env | sort
kubectl exec -it <pod> -- printenv RUN_MODE
```

### Verify via /proc
```bash
kubectl exec -it <pod> -- sh -c "cat /proc/1/environ | tr '\0' '\n' | sort"
```

### Node Scope Clarification
- Env vars are **not mounted as files on the node** by default.
- They exist in the container runtime process environment.
- If you need file-based access, use ConfigMap/Secret volumes (mounts), not env.

---

## QUICK COMPARISON TABLE

| Pattern | Scope | Source | Key Selection | Prefix Support | CKA Priority |
|---------|-------|--------|---------------|----------------|--------------|
| `env + value` | One var | Literal | N/A | ❌ | ⭐⭐⭐⭐⭐ |
| `env + configMapKeyRef` | One var | ConfigMap | ✅ Single key | ❌ | ⭐⭐⭐⭐⭐ |
| `env + secretKeyRef` | One var | Secret | ✅ Single key | ❌ | ⭐⭐⭐⭐⭐ |
| `envFrom + configMapRef` | Many vars | ConfigMap | ❌ All keys | ✅ | ⭐⭐⭐⭐ |
| `envFrom + secretRef` | Many vars | Secret | ❌ All keys | ✅ | ⭐⭐⭐⭐ |
| `env + fieldRef` | One var | Pod metadata | Field path | ❌ | ⭐⭐⭐ |
| `env + resourceFieldRef` | One var | Container resources | Resource path | ❌ | ⭐⭐⭐ |

---

## COMMON PATTERNS

### Pattern 1: Explicit Critical Vars + Bulk Defaults
```yaml
env:
  - name: DB_USER
    valueFrom:
      secretKeyRef:
        name: app-secret
        key: db-user
envFrom:
  - configMapRef:
      name: app-env
```

### Pattern 2: Collision-Safe Imports with Prefixes
```yaml
envFrom:
  - prefix: CFG_
    configMapRef:
      name: app-env
  - prefix: SEC_
    secretRef:
      name: app-env-secret
```

### Pattern 3: Metadata-aware Containers
```yaml
env:
  - name: POD_NAME
    valueFrom:
      fieldRef:
        fieldPath: metadata.name
  - name: POD_NAMESPACE
    valueFrom:
      fieldRef:
        fieldPath: metadata.namespace
```

---

## CKA EXAM TIPS

✅ **Most Tested:**
1. `env` literal values
2. `configMapKeyRef` / `secretKeyRef`
3. `envFrom` with ConfigMap/Secret
4. Prefix usage in `envFrom`

✅ **Likely Scenarios:**
- Map one config key to a custom env name
- Inject DB credentials from Secret
- Import all app settings from ConfigMap
- Use prefixes to avoid naming conflicts
- Verify env from inside running Pod

❌ **Common Mistakes:**
- Putting `envFrom` inside an `env` item
- Missing `key` under `configMapKeyRef` / `secretKeyRef`
- Wrong secret/configmap names
- Expecting env vars to appear as mounted files

✅ **Remember:**
- `env` = define one variable at a time
- `valueFrom` = source for one `env` variable
- `envFrom` = import all keys as variables
- Prefixes apply only with `envFrom`
- Secrets in env are visible to processes in container; use least privilege

---

## QUICK COPY-PASTE TEMPLATES

### Literal Env Template
```yaml
env:
  - name: VAR_NAME
    value: "VALUE"
```

### ConfigMap Key Template
```yaml
env:
  - name: VAR_NAME
    valueFrom:
      configMapKeyRef:
        name: CONFIGMAP_NAME
        key: CONFIG_KEY
```

### Secret Key Template
```yaml
env:
  - name: VAR_NAME
    valueFrom:
      secretKeyRef:
        name: SECRET_NAME
        key: SECRET_KEY
```

### envFrom ConfigMap Template
```yaml
envFrom:
  - configMapRef:
      name: CONFIGMAP_NAME
```

### envFrom Secret Template
```yaml
envFrom:
  - secretRef:
      name: SECRET_NAME
```

### Prefixed envFrom Template
```yaml
envFrom:
  - prefix: CFG_
    configMapRef:
      name: CONFIGMAP_NAME
  - prefix: SEC_
    secretRef:
      name: SECRET_NAME
```

**THIS IS EXAM-READY, HERMANO!** 💪
