# CKA Exam Card — API Server Down (Ultra-Short)

## 0) Symptom
```bash
kubectl get pods -A
```
If connection refused/timeout → continue.

## 1) Find apiserver container attempt
```bash
crictl ps -a --name kube-apiserver
```

## 2) Read latest error
```bash
crictl logs $(crictl ps -a --name kube-apiserver -q | head -n1)
```

## 3) Fix manifest
```bash
vi /etc/kubernetes/manifests/kube-apiserver.yaml
```

## 4) 3 common planted errors to check fast
```bash
grep -nE 'authorization-mode|tls-cert-file|etcd-servers' /etc/kubernetes/manifests/kube-apiserver.yaml
```

Expected patterns:
- `--authorization-mode=Node,RBAC`
- `--tls-cert-file=/etc/kubernetes/pki/apiserver.crt`
- `--etcd-servers=https://127.0.0.1:2379`

## 5) Remove backup/swap in static-pod dir
```bash
ls -la /etc/kubernetes/manifests/
mv /etc/kubernetes/manifests/kube-apiserver.yaml~ /root/ 2>/dev/null
mv /etc/kubernetes/manifests/*.swp /root/ 2>/dev/null
```

## 6) Trigger recreate + recheck
```bash
systemctl restart kubelet
crictl ps -a --name kube-apiserver
crictl logs $(crictl ps -a --name kube-apiserver -q | head -n1)
```

## 7) Verify recovered
```bash
crictl ps | grep kube-apiserver
kubectl get nodes
```

---
Rule: **logs → fix 1 thing → recheck → repeat**.
