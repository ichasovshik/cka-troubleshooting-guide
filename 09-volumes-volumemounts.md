# VOLUMES & VOLUMEMOUNTS CHEATSHEET

## QUICK REFERENCE

**volumeMounts:** Where to mount inside the container
**volumes:** What storage to use (source)

---

## 1. CONFIGMAP VOLUME

### Use Case
Mount configuration files into a pod

### volumeMounts Section
```yaml
volumeMounts:
  - name: config-volume
    mountPath: /etc/config
```

### volumes Section
```yaml
volumes:
  - name: config-volume
    configMap:
      name: my-config
```

### Full Pod Example
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-config
spec:
  containers:
  - name: app
    image: nginx
    volumeMounts:
      - name: config-volume
        mountPath: /etc/config
  volumes:
    - name: config-volume
      configMap:
        name: my-config
```

### Mount Specific Keys (Optional)
```yaml
volumes:
  - name: config-volume
    configMap:
      name: my-config
      items:
        - key: app.conf
          path: app.conf
        - key: db.conf
          path: db.conf
```

---

## 2. SECRET VOLUME

### Use Case
Mount secrets (passwords, tokens, certs) into a pod

### volumeMounts Section
```yaml
volumeMounts:
  - name: secret-volume
    mountPath: /etc/secrets
    readOnly: true
```

### volumes Section
```yaml
volumes:
  - name: secret-volume
    secret:
      secretName: my-secret
```

### Full Pod Example
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-secret
spec:
  containers:
  - name: app
    image: nginx
    volumeMounts:
      - name: secret-volume
        mountPath: /etc/secrets
        readOnly: true
  volumes:
    - name: secret-volume
      secret:
        secretName: my-secret
```

### Mount Specific Keys (Optional)
```yaml
volumes:
  - name: secret-volume
    secret:
      secretName: my-secret
      items:
        - key: username
          path: username.txt
        - key: password
          path: password.txt
```

---

## 3. HOSTPATH VOLUME

### Use Case
Mount files/directories from the host node into pod

### volumeMounts Section
```yaml
volumeMounts:
  - name: host-volume
    mountPath: /host-data
```

### volumes Section
```yaml
volumes:
  - name: host-volume
    hostPath:
      path: /var/log
      type: Directory
```

### Full Pod Example
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-hostpath
spec:
  containers:
  - name: app
    image: nginx
    volumeMounts:
      - name: host-volume
        mountPath: /host-data
  volumes:
    - name: host-volume
      hostPath:
        path: /var/log
        type: Directory
```

### HostPath Types
```yaml
type: Directory          # Mount existing directory
type: DirectoryOrCreate  # Create if doesn't exist
type: File               # Mount existing file
type: FileOrCreate       # Create if doesn't exist
type: Socket             # Unix socket
type: CharDevice         # Character device
type: BlockDevice        # Block device
```

---

## 4. PERSISTENTVOLUMECLAIM (PVC)

### Use Case
Mount persistent storage (databases, stateful apps)

### volumeMounts Section
```yaml
volumeMounts:
  - name: pvc-volume
    mountPath: /data
```

### volumes Section
```yaml
volumes:
  - name: pvc-volume
    persistentVolumeClaim:
      claimName: my-pvc
```

### Full Pod Example
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-pvc
spec:
  containers:
  - name: app
    image: postgres
    volumeMounts:
      - name: pvc-volume
        mountPath: /var/lib/postgresql/data
  volumes:
    - name: pvc-volume
      persistentVolumeClaim:
        claimName: my-pvc
```

---

## 5. EMPTYDIR VOLUME

### Use Case
Temporary storage shared between containers in same pod

### volumeMounts Section
```yaml
volumeMounts:
  - name: temp-volume
    mountPath: /tmp/data
```

### volumes Section
```yaml
volumes:
  - name: temp-volume
    emptyDir: {}
```

### Full Pod Example (Multi-Container)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-emptydir
spec:
  containers:
  - name: writer
    image: nginx
    volumeMounts:
      - name: temp-volume
        mountPath: /tmp/data
  - name: reader
    image: alpine
    volumeMounts:
      - name: temp-volume
        mountPath: /tmp/data
  volumes:
    - name: temp-volume
      emptyDir: {}
```

### With Size Limit
```yaml
volumes:
  - name: temp-volume
    emptyDir:
      sizeLimit: 1Gi
```

---

## 6. DOWNWARDAPI VOLUME

### Use Case
Expose pod metadata (name, namespace, labels, resources)

### volumeMounts Section
```yaml
volumeMounts:
  - name: downward-volume
    mountPath: /etc/podinfo
```

### volumes Section
```yaml
volumes:
  - name: downward-volume
    downwardAPI:
      items:
        - path: "pod-name"
          fieldRef:
            fieldPath: metadata.name
        - path: "pod-namespace"
          fieldRef:
            fieldPath: metadata.namespace
        - path: "pod-labels"
          fieldRef:
            fieldPath: metadata.labels
        - path: "cpu-limit"
          resourceFieldRef:
            containerName: app
            resource: limits.cpu
```

### Full Pod Example
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-downwardapi
  labels:
    app: myapp
    env: prod
spec:
  containers:
  - name: app
    image: nginx
    resources:
      limits:
        cpu: 500m
        memory: 128Mi
    volumeMounts:
      - name: downward-volume
        mountPath: /etc/podinfo
  volumes:
    - name: downward-volume
      downwardAPI:
        items:
          - path: "pod-name"
            fieldRef:
              fieldPath: metadata.name
          - path: "pod-namespace"
            fieldRef:
              fieldPath: metadata.namespace
          - path: "pod-labels"
            fieldRef:
              fieldPath: metadata.labels
          - path: "pod-annotations"
            fieldRef:
              fieldPath: metadata.annotations
```

---

## 7. PROJECTED VOLUME

### Use Case
Combine multiple volume sources (configmap + secret + downwardAPI)

### volumeMounts Section
```yaml
volumeMounts:
  - name: all-in-one
    mountPath: /etc/all
```

### volumes Section
```yaml
volumes:
  - name: all-in-one
    projected:
      sources:
        - configMap:
            name: my-config
        - secret:
            name: my-secret
        - downwardAPI:
            items:
              - path: "pod-name"
                fieldRef:
                  fieldPath: metadata.name
```

### Full Pod Example
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-projected
spec:
  containers:
  - name: app
    image: nginx
    volumeMounts:
      - name: all-in-one
        mountPath: /etc/all
  volumes:
    - name: all-in-one
      projected:
        sources:
          - configMap:
              name: my-config
          - secret:
              name: my-secret
          - downwardAPI:
              items:
                - path: "pod-name"
                  fieldRef:
                    fieldPath: metadata.name
```

---

## 8. NFSVOLUME

### Use Case
Mount NFS (Network File System) storage

### volumeMounts Section
```yaml
volumeMounts:
  - name: nfs-volume
    mountPath: /nfs-data
```

### volumes Section
```yaml
volumes:
  - name: nfs-volume
    nfs:
      server: nfs-server.example.com
      path: /exports/data
      readOnly: false
```

### Full Pod Example
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-nfs
spec:
  containers:
  - name: app
    image: nginx
    volumeMounts:
      - name: nfs-volume
        mountPath: /nfs-data
  volumes:
    - name: nfs-volume
      nfs:
        server: 192.168.1.100
        path: /exports/data
        readOnly: false
```

---

## QUICK COMPARISON TABLE

| Volume Type | Use Case | Persistent | Shared | CKA Priority |
|------------|----------|-----------|--------|--------------|
| **ConfigMap** | Config files | ❌ No | ✅ Yes | ⭐⭐⭐⭐⭐ |
| **Secret** | Passwords/tokens | ❌ No | ✅ Yes | ⭐⭐⭐⭐⭐ |
| **HostPath** | Node files/logs | ✅ Yes | ❌ No | ⭐⭐⭐⭐ |
| **PVC** | Persistent data | ✅ Yes | ✅ Yes | ⭐⭐⭐⭐⭐ |
| **EmptyDir** | Temp shared data | ❌ No | ✅ Yes (same pod) | ⭐⭐⭐⭐ |
| **DownwardAPI** | Pod metadata | ❌ No | ❌ No | ⭐⭐⭐ |
| **Projected** | Multiple sources | ❌ No | ✅ Yes | ⭐⭐⭐ |
| **NFS** | Network storage | ✅ Yes | ✅ Yes | ⭐⭐⭐ |

---

## COMMON PATTERNS

### Pattern 1: Config + Secret (Database Connection)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-with-db
spec:
  containers:
  - name: app
    image: myapp
    volumeMounts:
      - name: config
        mountPath: /etc/config
      - name: secrets
        mountPath: /etc/secrets
  volumes:
    - name: config
      configMap:
        name: db-config
    - name: secrets
      secret:
        name: db-credentials
```

### Pattern 2: HostPath + PVC (Backup)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: backup-pod
spec:
  containers:
  - name: backup
    image: backup-tool
    volumeMounts:
      - name: data
        mountPath: /data
      - name: backup-dest
        mountPath: /backups
  volumes:
    - name: data
      persistentVolumeClaim:
        claimName: app-data
    - name: backup-dest
      hostPath:
        path: /backups
        type: DirectoryOrCreate
```

### Pattern 3: Multiple Containers Sharing EmptyDir
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: shared-data-pod
spec:
  containers:
  - name: producer
    image: producer-image
    volumeMounts:
      - name: shared
        mountPath: /data
  - name: consumer
    image: consumer-image
    volumeMounts:
      - name: shared
        mountPath: /data
  volumes:
    - name: shared
      emptyDir: {}
```

---

## CKA EXAM TIPS

✅ **Most Tested:**
1. ConfigMap volumes (mounting config files)
2. Secret volumes (mounting credentials)
3. PVC volumes (persistent storage)
4. HostPath volumes (node data access)

✅ **Likely Scenarios:**
- Mount config file from ConfigMap
- Mount database credentials from Secret
- Mount data directory to PersistentVolume
- Access node logs via HostPath
- Share data between containers with EmptyDir

❌ **Less Common in CKA:**
- DownwardAPI (more CKAD)
- Projected volumes (advanced)
- NFS (usually pre-configured)

✅ **Remember:**
- volumeMounts = where inside container
- volumes = what storage source
- Always match names between volumeMounts and volumes
- readOnly is optional (default: false)

---

## QUICK COPY-PASTE TEMPLATES

### ConfigMap Template
```yaml
volumeMounts:
  - name: config
    mountPath: /etc/config
volumes:
  - name: config
    configMap:
      name: CONFIG_NAME
```

### Secret Template
```yaml
volumeMounts:
  - name: secret
    mountPath: /etc/secrets
    readOnly: true
volumes:
  - name: secret
    secret:
      secretName: SECRET_NAME
```

### HostPath Template
```yaml
volumeMounts:
  - name: host
    mountPath: /host-data
volumes:
  - name: host
    hostPath:
      path: /var/log
      type: Directory
```

### PVC Template
```yaml
volumeMounts:
  - name: pvc
    mountPath: /data
volumes:
  - name: pvc
    persistentVolumeClaim:
      claimName: PVC_NAME
```

### EmptyDir Template
```yaml
volumeMounts:
  - name: temp
    mountPath: /tmp
volumes:
  - name: temp
    emptyDir: {}
```

**THIS IS EXAM-READY, HERMANO!** 💪
