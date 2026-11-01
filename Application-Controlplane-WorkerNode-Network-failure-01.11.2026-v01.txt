

════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
═══════════ Practice Test - Worker Node Failure ════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════


════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
══════════════════════════════════════════════ Q.1 ═════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════

   __ ______  ____  ________ __ __    ____  __  ______ 
   / //_/ __ \/ __ \/ ____/ //_// /   / __ \/ / / / __ \
  / ,< / / / / / / / __/ / ,<  / /   / / / / / / / / / /
 / /| / /_/ / /_/ / /___/ /| |/ /___/ /_/ / /_/ / /_/ / 
/_/ |_\____/_____/_____/_/ |_/_____/\____/\____/_____/  
                                                        
               All rights reserved                                                                                                                  

controlplane ~ ➜  ssh node01
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 6.8.0-90-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.

node01 ~ ➜  k get nodes
E1031 20:55:56.259752   20918 memcache.go:265] "Unhandled Error" err="couldn't get current server API group list: the server could not find the requested resource"
E1031 20:55:56.268889   20918 memcache.go:265] "Unhandled Error" err="couldn't get current server API group list: the server could not find the requested resource"
E1031 20:55:56.286056   20918 memcache.go:265] "Unhandled Error" err="couldn't get current server API group list: the server could not find the requested resource"
E1031 20:55:56.295652   20918 memcache.go:265] "Unhandled Error" err="couldn't get current server API group list: the server could not find the requested resource"
E1031 20:55:56.296567   20918 memcache.go:265] "Unhandled Error" err="couldn't get current server API group list: the server could not find the requested resource"
Error from server (NotFound): the server could not find the requested resource

node01 ~ ✖ 

node01 ~ ✖ systemctl status kubelet
○ kubelet.service - kubelet: The Kubernetes Node Agent
     Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
    Drop-In: /usr/lib/systemd/system/kubelet.service.d
             └─10-kubeadm.conf
     Active: inactive (dead) since Sat 2026-10-31 20:54:07 UTC; 2min 7s ago
       Docs: https://kubernetes.io/docs/
    Process: 2109 ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_CONFIG_ARGS $KUBELET_KUBEADM_ARGS $KUBELET_EXTRA_AR>
   Main PID: 2109 (code=exited, status=0/SUCCESS)  <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< HERE 
        CPU: 14.662s

Oct 31 20:14:22 node01 kubelet[2109]: I1031 20:14:22.100829    2109 swap_util.go:58] "Running under a user namespace - tmpfs noswa>
Oct 31 20:14:24 node01 kubelet[2109]: I1031 20:14:24.027714    2109 pod_startup_latency_tracker.go:108] "Observed pod startup dura>
Oct 31 20:14:24 node01 kubelet[2109]: I1031 20:14:24.302828    2109 kubelet_node_status.go:427] "Fast updating node status as it j>
Oct 31 20:19:20 node01 kubelet[2109]: E1031 20:19:20.998584    2109 info.go:104] Failed to get disk map: could not parse device nu>
Oct 31 20:24:20 node01 kubelet[2109]: E1031 20:24:20.998117    2109 info.go:104] Failed to get disk map: could not parse device nu>
Oct 31 20:29:20 node01 kubelet[2109]: E1031 20:29:20.999725    2109 info.go:104] Failed to get disk map: could not parse device nu>
Oct 31 20:34:20 node01 kubelet[2109]: E1031 20:34:20.998939    2109 info.go:104] Failed to get disk map: could not parse device nu>
Oct 31 20:39:20 node01 kubelet[2109]: E1031 20:39:20.998878    2109 info.go:104] Failed to get disk map: could not parse device nu>
Oct 31 20:44:20 node01 kubelet[2109]: E1031 20:44:20.999423    2109 info.go:104] Failed to get disk map: could not parse device nu>
Oct 31 20:49:20 node01 kubelet[2109]: E1031 20:49:20.999080    2109 info.go:104] Failed to get disk map: could not parse device nu>
lines 1-20/20 (END)



////////////////////////////\\\\\\\\\\\\\\\\\\\\\\\\\\\\///////////////////////////////\\\\\\\\\\\\\\\\
//
//				Output of the termianals T1
//
////////////////////////////\\\\\\\\\\\\\\\\\\\\\\\\\\\\///////////////////////////////\\\\\\\\\\\\\\\\

controlplane ~ ➜  k get nodes
NAME           STATUS   ROLES           AGE   VERSION
controlplane   Ready    control-plane   43m   v1.35.0
node01         Ready    <none>          42m   v1.35.0

controlplane ~ ➜  




════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
══════════════════════════════════════════════ Q.2 ═════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════

node01 ~ ➜  k get nodes
E1031 20:59:10.625228   22669 memcache.go:265] "Unhandled Error" err="couldn't get current server API group list: the server could not find the requested resource"
E1031 20:59:10.626553   22669 memcache.go:265] "Unhandled Error" err="couldn't get current server API group list: the server could not find the requested resource"
E1031 20:59:10.627326   22669 memcache.go:265] "Unhandled Error" err="couldn't get current server API group list: the server could not find the requested resource"
E1031 20:59:10.637194   22669 memcache.go:265] "Unhandled Error" err="couldn't get current server API group list: the server could not find the requested resource"
E1031 20:59:10.638054   22669 memcache.go:265] "Unhandled Error" err="couldn't get current server API group list: the server could not find the requested resource"
Error from server (NotFound): the server could not find the requested resource

node01 ~ ✖ 

node01 ~ ✖ systemctl status kubelet
● kubelet.service - kubelet: The Kubernetes Node Agent
     Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
    Drop-In: /usr/lib/systemd/system/kubelet.service.d
             └─10-kubeadm.conf
     Active: activating (auto-restart) (Result: exit-code) since Sat 2026-10-31 20:59:19 UTC; 6s ago
       Docs: https://kubernetes.io/docs/
    Process: 22795 ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_CONFIG_ARGS $KUBELET_KUBEADM_ARGS $KUBELET_EXTRA_A>
   Main PID: 22795 (code=exited, status=1/FAILURE)
        CPU: 39ms

node01 ~ ✖ crictl ps -a
CONTAINER           IMAGE               CREATED             STATE               NAME                 ATTEMPT             POD ID              POD                     NAMESPACE
e96cd02902a9e       01cdfa8dd262f       45 minutes ago      Running             kube-flannel         0                   10857df5a01a5       kube-flannel-ds-nnrzh   kube-flannel
60d38082bc8f2       01cdfa8dd262f       45 minutes ago      Exited              install-cni          0                   10857df5a01a5       kube-flannel-ds-nnrzh   kube-flannel
6303ea0cfc022       a55d1bad692b7       45 minutes ago      Exited              install-cni-plugin   0                   10857df5a01a5       kube-flannel-ds-nnrzh   kube-flannel
7cd26d7f0de7c       32652ff1bbe6b       45 minutes ago      Running             kube-proxy           0                   db888f709a530       kube-proxy-9dchv        kube-system

node01 ~ ➜  crictl ps -a --name=install-cni
CONTAINER           IMAGE               CREATED             STATE               NAME                 ATTEMPT             POD ID              POD                     NAMESPACE
60d38082bc8f2       01cdfa8dd262f       45 minutes ago      Exited              install-cni          0                   10857df5a01a5       kube-flannel-ds-nnrzh   kube-flannel
6303ea0cfc022       a55d1bad692b7       45 minutes ago      Exited              install-cni-plugin   0                   10857df5a01a5       kube-flannel-ds-nnrzh   kube-flannel

node01 ~ ➜  crictl ps -a --name=install-cni-plugin
CONTAINER           IMAGE               CREATED             STATE               NAME                 ATTEMPT             POD ID              POD                     NAMESPACE
6303ea0cfc022       a55d1bad692b7       46 minutes ago      Exited              install-cni-plugin   0                   10857df5a01a5       kube-flannel-ds-nnrzh   kube-flannel

node01 ~ ➜  crictl ps -a --name=install-cni-plugin -q
6303ea0cfc022ff9fef253e54d2dafdd25d58f7ac61b71c5b5e252947c0b850f

node01 ~ ➜  crictl logs $(crictl ps -a --name=install-cni-plugin -q)

node01 ~ ➜  crictl logs $(crictl ps -a --name=install-cni-plugin -q)

node01 ~ ➜  crictl logs $(crictl ps -a --name=install-cni -q)
NAME:
   crictl logs - Fetch the logs of a container

USAGE:
   crictl logs [command options] CONTAINER-ID

COMMANDS:
   help, h  Shows a list of commands or help for one command

OPTIONS:
   --follow, -f              Follow log output (default: false)
   --limit-bytes value       Maximum bytes of logs to return. Defaults to no limit (default: -1)
   --previous, -p            Print the logs for the previous instance of the container in a pod if it exists (default: false)
   --reopen, -r              Reopen the container logs for the provided container (default: false)
   --since value             Show logs since timestamp (e.g. 2013-01-02T13:23:37) or relative (e.g. 42m for 42 minutes)
   --stream value, -s value  Show specified stream (stdout or stderr). Defaults to both.
   --tail value              Number of lines to show from the end of the logs. Defaults to all (default: -1)
   --timestamps, -t          Show timestamps (default: false)
   --help, -h                show help

node01 ~ ➜  crictl ps -a --name=install-cni -q
60d38082bc8f2caf43e49f00153a5f2fd27b7c180c91232147d0c7460c851945
6303ea0cfc022ff9fef253e54d2dafdd25d58f7ac61b71c5b5e252947c0b850f

node01 ~ ➜  

node01 ~ ➜  crictl logs 60d38082bc8f2caf43e49f00153a5f2fd27b7c180c91232147d0c7460c851945

node01 ~ ➜  crictl logs 6303ea0cfc022ff9fef253e54d2dafdd25d58f7ac61b71c5b5e252947c0b850f

node01 ~ ➜  grep -irn error /var/log/container
grep: /var/log/container: No such file or directory

node01 ~ ✖ grep -irn error /var/log/container
grep: /var/log/container: No such file or directory

node01 ~ ✖ ls -l /var/log/container
ls: cannot access '/var/log/container': No such file or directory

node01 ~ ✖ ls -l /var/log/container
containerd.log  containers/     

node01 ~ ✖ ls -l /var/log/container
containerd.log  containers/     

node01 ~ ✖ ls -l /var/log/containers
total 16
lrwxrwxrwx 1 root root 103 Oct 31 20:14 kube-flannel-ds-nnrzh_kube-flannel_install-cni-60d38082bc8f2caf43e49f00153a5f2fd27b7c180c91232147d0c7460c851945.log -> /var/log/pods/kube-flannel_kube-flannel-ds-nnrzh_1ad73bfd-2bfc-473f-baab-8931d9589bd9/install-cni/0.log
lrwxrwxrwx 1 root root 110 Oct 31 20:14 kube-flannel-ds-nnrzh_kube-flannel_install-cni-plugin-6303ea0cfc022ff9fef253e54d2dafdd25d58f7ac61b71c5b5e252947c0b850f.log -> /var/log/pods/kube-flannel_kube-flannel-ds-nnrzh_1ad73bfd-2bfc-473f-baab-8931d9589bd9/install-cni-plugin/0.log
lrwxrwxrwx 1 root root 104 Oct 31 20:14 kube-flannel-ds-nnrzh_kube-flannel_kube-flannel-e96cd02902a9e1f2a38c09e703cdbbf2c174fb238052397b9b7687acd50321c2.log -> /var/log/pods/kube-flannel_kube-flannel-ds-nnrzh_1ad73bfd-2bfc-473f-baab-8931d9589bd9/kube-flannel/0.log
lrwxrwxrwx 1 root root  96 Oct 31 20:14 kube-proxy-9dchv_kube-system_kube-proxy-7cd26d7f0de7c9eab1c14a9324d8c3a9c95855b08580a21224f4bcb648d86d83.log -> /var/log/pods/kube-system_kube-proxy-9dchv_9a82fc9b-5d4a-4a0e-9def-76147f935dea/kube-proxy/0.log

node01 ~ ➜  grep -irn error /var/log/containers

node01 ~ ✖ grep -irn error /var/log/pods

node01 ~ ✖ grep -irn failed /var/log/pods

node01 ~ ✖ grep -irn failed /var/log/containers

node01 ~ ✖ grep -irn error /etc/kubernetes/

node01 ~ ✖ grep -irn manifest /etc/kubernetes/

node01 ~ ✖ ls -l /etc/cni/net.d/
total 4
-rw-r--r-- 1 root root 292 Oct 31 20:14 10-flannel.conflist



node01 ~ ➜  journalctl -u kubelet -n 60 --no-pager -l | cat | grep "]: E"

ct 31 21:08:32 node01 kubelet[28125]: E1031 21:08:32.779267   28125 run.go:72] "command failed" err="failed to construct kubelet dependencies: unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Oct 31 21:08:43 node01 kubelet[28235]: E1031 21:08:43.031021   28235 run.go:72] "command failed" err="failed to construct kubelet dependencies: unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Oct 31 21:08:53 node01 kubelet[28321]: E1031 21:08:53.281692   28321 run.go:72] "command failed" err="failed to construct kubelet dependencies: unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"  <<<<<<<<<<<<<< ====



════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
══════════════════════════════════════════════ Q.4 ═════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════

node01 ~ ➜  journalctl -u kubelet | cat | grep "]: E"

Oct 31 21:15:42 node01 kubelet[30618]: E1031 21:15:42.339321   30618 event.go:368] "Unable to write event (may retry after sleeping)" err="Post \"https://controlplane:6553/api/v1/namespaces/default/events\": dial tcp 10.244.243.243:6553: connect: connection refused" event="&Event{ObjectMeta:{node01.18e3b9a0d1a35cd0  default    0 0001-01-01 00:00:00 +0000 UTC <nil> <nil> map[] map[] [] [] []},InvolvedObject:ObjectReference{Kind:Node,Namespace:,Name:node01,UID:,APIVersion:v1,ResourceVersion:,FieldPath:,},Reason:InvalidDiskCapacity,Message:invalid capacity 0 on image filesystem,Source:EventSource{Component:kubelet,Host:node01,},FirstTimestamp:2026-10-31 21:12:56.986639568 +0000 UTC m=+0.113199175,LastTimestamp:2026-10-31 21:12:56.986639568 +0000 UTC m=+0.113199175,Count:1,Type:Warning,EventTime:0001-01-01 00:00:00 +0000 UTC,Series:nil,Action:,Related:nil,ReportingController:kubelet,ReportingInstance:node01,}"

node01 ~ ➜  journalctl -u kubelet | cat | grep "]: E"^C

node01 ~ ✖ grep 6553 -rn /etc/kubernetes/
/etc/kubernetes/kubelet.conf:5:    server: https://controlplane:6553

node01 ~ ➜  vi /etc/kubernetes/kubelet.conf

node01 ~ ➜  

node01 ~ ➜  

node01 ~ ➜  grep 6553 -rn /etc/kubernetes/

node01 ~ ✖ grep 6443 -rn /etc/kubernetes/
/etc/kubernetes/kubelet.conf:5:    server: https://controlplane:6443

node01 ~ ➜  systemctl restart kubelet

node01 ~ ➜  systemctl status kubelet
● kubelet.service - kubelet: The Kubernetes Node Agent
     Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
    Drop-In: /usr/lib/systemd/system/kubelet.service.d
             └─10-kubeadm.conf
     Active: active (running) since Sat 2026-10-31 21:18:32 UTC; 10s ago
       Docs: https://kubernetes.io/docs/
   Main PID: 33222 (kubelet)
      Tasks: 23 (limit: 75883)
     Memory: 24.9M
        CPU: 216ms
     CGroup: /system.slice/kubelet.service
             └─33222 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --config=/var/lib/kubelet/config>

Oct 31 21:18:32 node01 kubelet[33222]: I1031 21:18:32.849271   33222 kubelet_node_status.go:123] "Node was previously registered" node="node01"
Oct 31 21:18:32 node01 kubelet[33222]: I1031 21:18:32.849380   33222 kubelet_node_status.go:77] "Successfully registered node" node="node01"
Oct 31 21:18:33 node01 kubelet[33222]: I1031 21:18:33.613047   33222 apiserver.go:52] "Watching apiserver"
Oct 31 21:18:33 node01 kubelet[33222]: I1031 21:18:33.621610   33222 desired_state_of_world_populator.go:154] "Finished populating initial desired state of world"
Oct 31 21:18:33 node01 kubelet[33222]: I1031 21:18:33.627851   33222 reconciler_common.go:251] "operationExecutor.VerifyControllerAttachedVolume started for volume \"xtables->
Oct 31 21:18:33 node01 kubelet[33222]: I1031 21:18:33.627879   33222 reconciler_common.go:251] "operationExecutor.VerifyControllerAttachedVolume started for volume \"lib-modu>
Oct 31 21:18:33 node01 kubelet[33222]: I1031 21:18:33.627914   33222 reconciler_common.go:251] "operationExecutor.VerifyControllerAttachedVolume started for volume \"run\" (U>
Oct 31 21:18:33 node01 kubelet[33222]: I1031 21:18:33.627927   33222 reconciler_common.go:251] "operationExecutor.VerifyControllerAttachedVolume started for volume \"cni-plug>
Oct 31 21:18:33 node01 kubelet[33222]: I1031 21:18:33.627941   33222 reconciler_common.go:251] "operationExecutor.VerifyControllerAttachedVolume started for volume \"cni\" (U>
Oct 31 21:18:33 node01 kubelet[33222]: I1031 21:18:33.627959   33222 reconciler_common.go:251] "operationExecutor.VerifyControllerAttachedVolume started for volume \"xtables->

node01 ~ ➜  




////////////////////////////\\\\\\\\\\\\\\\\\\\\\\\\\\\\///////////////////////////////\\\\\\\\\\\\\\\\
//
//				Output of the termianals T1
//
////////////////////////////\\\\\\\\\\\\\\\\\\\\\\\\\\\\///////////////////////////////\\\\\\\\\\\\\\\\

controlplane ~ ➜  k get nodes
NAME           STATUS     ROLES           AGE   VERSION
controlplane   Ready      control-plane   64m   v1.35.0
node01         NotReady   <none>          63m   v1.35.0

controlplane ~ ➜  k get nodes
NAME           STATUS   ROLES           AGE   VERSION
controlplane   Ready    control-plane   65m   v1.35.0
node01         Ready    <none>          64m   v1.35.0

controlplane ~ ➜  



════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
═══════════ Practice Test - Worker Node Failure ═════════════════ 01.11.2026 ═══════════════════════════════════════════
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
     Active: inactive (dead) since Sun 2026-11-01 15:25:46 UTC; 2min 35s ago
       Docs: https://kubernetes.io/docs/
    Process: 2122 ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_CONFIG_ARGS $KUBELET_KUBEADM_ARGS $KUBELET_EXTRA_AR>
   Main PID: 2122 (code=exited, status=0/SUCCESS)
        CPU: 9.159s

Nov 01 15:00:29 node01 kubelet[2122]: I1101 15:00:29.377938    2122 reconciler_common.go:251] "operationExecutor.VerifyControllerA>
Nov 01 15:00:29 node01 kubelet[2122]: I1101 15:00:29.377988    2122 reconciler_common.go:251] "operationExecutor.VerifyControllerA>
Nov 01 15:00:29 node01 kubelet[2122]: I1101 15:00:29.381708    2122 swap_util.go:58] "Running under a user namespace - tmpfs noswa>
Nov 01 15:00:31 node01 kubelet[2122]: I1101 15:00:31.296376    2122 pod_startup_latency_tracker.go:108] "Observed pod startup dura>
Nov 01 15:00:31 node01 kubelet[2122]: I1101 15:00:31.556997    2122 kubelet_node_status.go:427] "Fast updating node status as it j>
Nov 01 15:05:28 node01 kubelet[2122]: E1101 15:05:28.278910    2122 info.go:104] Failed to get disk map: could not parse device nu>
Nov 01 15:10:28 node01 kubelet[2122]: E1101 15:10:28.278152    2122 info.go:104] Failed to get disk map: could not parse device nu>
Nov 01 15:15:28 node01 kubelet[2122]: E1101 15:15:28.278144    2122 info.go:104] Failed to get disk map: could not parse device nu>
Nov 01 15:20:28 node01 kubelet[2122]: E1101 15:20:28.279152    2122 info.go:104] Failed to get disk map: could not parse device nu>
Nov 01 15:25:28 node01 kubelet[2122]: E1101 15:25:28.278589    2122 info.go:104] Failed to get disk map: could not parse device nu>

node01 ~ ✖ systemctl start kubelet

node01 ~ ➜  

node01 ~ ➜  systemctl status kubelet
● kubelet.service - kubelet: The Kubernetes Node Agent
     Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
    Drop-In: /usr/lib/systemd/system/kubelet.service.d
             └─10-kubeadm.conf
     Active: active (running) since Sun 2026-11-01 15:28:37 UTC; 3s ago
       Docs: https://kubernetes.io/docs/
   Main PID: 15637 (kubelet)
      Tasks: 23 (limit: 75883)
     Memory: 23.4M
        CPU: 172ms
     CGroup: /system.slice/kubelet.service
             └─15637 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/k>

Nov 01 15:28:38 node01 kubelet[15637]: I1101 15:28:38.152047   15637 kubelet_node_status.go:123] "Node was previously registered" >
Nov 01 15:28:38 node01 kubelet[15637]: I1101 15:28:38.152099   15637 kubelet_node_status.go:77] "Successfully registered node" nod>
Nov 01 15:28:38 node01 kubelet[15637]: I1101 15:28:38.948259   15637 apiserver.go:52] "Watching apiserver"
Nov 01 15:28:38 node01 kubelet[15637]: I1101 15:28:38.956306   15637 desired_state_of_world_populator.go:154] "Finished populating>
Nov 01 15:28:38 node01 kubelet[15637]: I1101 15:28:38.961027   15637 reconciler_common.go:251] "operationExecutor.VerifyController>
Nov 01 15:28:38 node01 kubelet[15637]: I1101 15:28:38.961075   15637 reconciler_common.go:251] "operationExecutor.VerifyController>
Nov 01 15:28:38 node01 kubelet[15637]: I1101 15:28:38.961088   15637 reconciler_common.go:251] "operationExecutor.VerifyController>
Nov 01 15:28:38 node01 kubelet[15637]: I1101 15:28:38.961152   15637 reconciler_common.go:251] "operationExecutor.VerifyController>
Nov 01 15:28:38 node01 kubelet[15637]: I1101 15:28:38.961220   15637 reconciler_common.go:251] "operationExecutor.VerifyController>
Nov 01 15:28:38 node01 kubelet[15637]: I1101 15:28:38.961263   15637 reconciler_common.go:251] "operationExecutor.VerifyController>

node01 ~ ➜  



////////////////////////////\\\\\\\\\\\\\\\\\\\\\\\\\\\\///////////////////////////////\\\\\\\\\\\\\\\\
//
//				Output of the termianals T1
//
////////////////////////////\\\\\\\\\\\\\\\\\\\\\\\\\\\\///////////////////////////////\\\\\\\\\\\\\\\\
controlplane ~ ➜  k get nodes
NAME           STATUS     ROLES           AGE   VERSION
controlplane   Ready      control-plane   26m   v1.35.0
node01         NotReady   <none>          26m   v1.35.0

controlplane ~ ➜  

controlplane ~ ➜  k get nodes
NAME           STATUS   ROLES           AGE   VERSION
controlplane   Ready    control-plane   28m   v1.35.0
node01         Ready    <none>          28m   v1.35.0

controlplane ~ ➜  


════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
══════════════════════════════════════════════ Q.2 ═════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
node01 ~ ➜  #==================================== Q.2=================================

node01 ~ ➜  systemctl status kubelet
● kubelet.service - kubelet: The Kubernetes Node Agent
     Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
    Drop-In: /usr/lib/systemd/system/kubelet.service.d
             └─10-kubeadm.conf
     Active: activating (auto-restart) (Result: exit-code) since Sun 2026-11-01 15:31:32 UTC; 4s ago
       Docs: https://kubernetes.io/docs/
    Process: 17208 ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_CONFIG_ARGS $KUBELET_KUBEADM_ARGS $KUBELET_EXTRA_A>
   Main PID: 17208 (code=exited, status=1/FAILURE)
        CPU: 37ms

node01 ~ ✖ 


node01 ~ ✖ grep WRONG -rn /var/lib/kubelet/
/var/lib/kubelet/config.yaml:9:    clientCAFile: /etc/kubernetes/pki/WRONG-CA-FILE.crt

node01 ~ ➜  vi /var/lib/kubelet/config.yaml

node01 ~ ➜  

node01 ~ ➜  systemctl restart kubelet

node01 ~ ➜  

Nov 01 15:31:53 node01 kubelet[17434]: E1101 15:31:53.226879   17434 run.go:72] "command failed" err="failed to construct kubelet dependencies: unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
Nov 01 15:32:03 node01 kubelet[17564]: E1101 15:32:03.475243   17564 run.go:72] "command failed" err="failed to construct kubelet dependencies: unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"

node01 ~ ➜  ls -l /etc/kubernetes/pki/
total 4
-rw-r--r-- 1 root root 1107 Nov  1 15:00 ca.crt

node01 ~ ➜  grep WRONG -rn /etc/kubernetes/

node01 ~ ✖ grep WRONG -rn /var/lib



////////////////////////////\\\\\\\\\\\\\\\\\\\\\\\\\\\\///////////////////////////////\\\\\\\\\\\\\\\\
//
//				Output of the termianals T1
//
////////////////////////////\\\\\\\\\\\\\\\\\\\\\\\\\\\\///////////////////////////////\\\\\\\\\\\\\\\\
controlplane ~ ➜  #================== q.2========================

controlplane ~ ➜  k get nodes
NAME           STATUS     ROLES           AGE   VERSION
controlplane   Ready      control-plane   31m   v1.35.0
node01         NotReady   <none>          30m   v1.35.0

controlplane ~ ➜  k get nodes
NAME           STATUS   ROLES           AGE   VERSION
controlplane   Ready    control-plane   35m   v1.35.0
node01         Ready    <none>          35m   v1.35.0



════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
══════════════════════════════════════════════ Q.3 ═════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════



journalctl -u kubelet | grep "]: E"

Nov 01 15:38:22 node01 kubelet[19842]: E1101 15:38:22.259223   19842 event.go:368] "Unable to write event (may retry after sleeping)" err="Post \"https://controlplane:6553/api/v1/namespaces/default/events\": dial tcp 10.244.197.198:6553: connect: connection refused" event="&Event{ObjectMeta:{node01.18e3f5d3e7c258b2  default    0 0001-01-01 00:00:00 +0000 UTC <nil> <nil> map[] map[] [] [] []},InvolvedObject:ObjectReference{Kind:Node,Namespace:,Name:node01,UID:,APIVersion:v1,ResourceVersion:,FieldPath:,},Reason:InvalidDiskCapacity,Message:invalid capacity 0 on image filesystem,Source:EventSource{Component:kubelet,Host:node01,},FirstTimestamp:2026-11-01 15:36:07.098767538 +0000 UTC m=+0.064092586,LastTimestamp:2026-11-01 15:36:07.098767538 +0000 UTC m=+0.064092586,Count:1,Type:Warning,EventTime:0001-01-01 00:00:00 +0000 UTC,Series:nil,Action:,Related:nil,ReportingController:kubelet,ReportingInstance:node01


node01 ~ ➜  grep 6553 -rn /etc/kubernetes
/etc/kubernetes/kubelet.conf:5:    server: https://controlplane:6553

node01 ~ ➜  vi /etc/kubernetes/kubelet.conf

node01 ~ ➜  

node01 ~ ➜  grep 6553 -rn /etc/kubernetes

node01 ~ ✖ grep 6443 -rn /etc/kubernetes
/etc/kubernetes/kubelet.conf:5:    server: https://controlplane:6443

node01 ~ ➜  systemctl restart kubelet

node01 ~ ➜  systemctl status kubelet
● kubelet.service - kubelet: The Kubernetes Node Agent
     Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
    Drop-In: /usr/lib/systemd/system/kubelet.service.d
             └─10-kubeadm.conf
     Active: active (running) since Sun 2026-11-01 15:40:53 UTC; 10s ago
       Docs: https://kubernetes.io/docs/
   Main PID: 21872 (kubelet)
      Tasks: 23 (limit: 75883)
     Memory: 24.2M
        CPU: 220ms
     CGroup: /system.slice/kubelet.service
             └─21872 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/k>

Nov 01 15:40:53 node01 kubelet[21872]: I1101 15:40:53.495555   21872 kubelet_node_status.go:123] "Node was previously registered" >
Nov 01 15:40:53 node01 kubelet[21872]: I1101 15:40:53.495608   21872 kubelet_node_status.go:77] "Successfully registered node" nod>
Nov 01 15:40:54 node01 kubelet[21872]: I1101 15:40:54.264733   21872 apiserver.go:52] "Watching apiserver"
Nov 01 15:40:54 node01 kubelet[21872]: I1101 15:40:54.287970   21872 desired_state_of_world_populator.go:154] "Finished populating>
Nov 01 15:40:54 node01 kubelet[21872]: I1101 15:40:54.294037   21872 reconciler_common.go:251] "operationExecutor.VerifyController>
Nov 01 15:40:54 node01 kubelet[21872]: I1101 15:40:54.294062   21872 reconciler_common.go:251] "operationExecutor.VerifyController>
Nov 01 15:40:54 node01 kubelet[21872]: I1101 15:40:54.294074   21872 reconciler_common.go:251] "operationExecutor.VerifyController>
Nov 01 15:40:54 node01 kubelet[21872]: I1101 15:40:54.294098   21872 reconciler_common.go:251] "operationExecutor.VerifyController>
Nov 01 15:40:54 node01 kubelet[21872]: I1101 15:40:54.294171   21872 reconciler_common.go:251] "operationExecutor.VerifyController>
Nov 01 15:40:54 node01 kubelet[21872]: I1101 15:40:54.294185   21872 reconciler_common.go:251] "operationExecutor.VerifyController>

node01 ~ ➜  


////////////////////////////\\\\\\\\\\\\\\\\\\\\\\\\\\\\///////////////////////////////\\\\\\\\\\\\\\\\
//
//				Output of the termianals T1
//
////////////////////////////\\\\\\\\\\\\\\\\\\\\\\\\\\\\///////////////////////////////\\\\\\\\\\\\\\\\
controlplane ~ ➜  #===================== Q.3 ======================

controlplane ~ ➜  k get nodes
NAME           STATUS     ROLES           AGE   VERSION
controlplane   Ready      control-plane   37m   v1.35.0
node01         NotReady   <none>          36m   v1.35.0

controlplane ~ ➜  k get nodes
NAME           STATUS   ROLES           AGE   VERSION
controlplane   Ready    control-plane   41m   v1.35.0
node01         Ready    <none>          40m   v1.35.0

controlplane ~ ➜  

════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
═══════ Practice Test - Troubleshoot Network ══════════════ 01.11.2026 ═════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════


════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
══════════════════════════════════════════════ Q.1 ═════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
**Troubleshooting Test 1:** A simple 2 tier application is deployed in the triton namespace. It must display a green web page on 
success. Click on the app tab at the top of your terminal to view your application. It is currently failed. 
Troubleshoot and fix the issue.


Stick to the given architecture. Use the same names and port numbers as given in the below architecture diagram. 
Feel free to edit, delete or recreate objects as necessary.

         Welcome to the KodeKloud Hands-On lab                                                                                                      
    __ ______  ____  ________ __ __    ____  __  ______ 
   / //_/ __ \/ __ \/ ____/ //_// /   / __ \/ / / / __ \
  / ,< / / / / / / / __/ / ,<  / /   / / / / / / / / / /
 / /| / /_/ / /_/ / /___/ /| |/ /___/ /_/ / /_/ / /_/ / 
/_/ |_\____/_____/_____/_/ |_/_____/\____/\____/_____/  
                                                        
               All rights reserved                                                                                                                  

controlplane ~ ➜  k config set-context --current --namespace=triton 
Context "kubernetes-admin@kubernetes" modified.

controlplane ~ ➜  k get all
NAME                               READY   STATUS    RESTARTS   AGE
pod/mysql                          0/1     Pending   0          69s
pod/webapp-mysql-58c8d859b-lpzwz   0/1     Pending   0          69s

NAME                  TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/mysql         ClusterIP   172.20.134.37   <none>        3306/TCP         69s
service/web-service   NodePort    172.20.62.74    <none>        8080:30081/TCP   69s

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/webapp-mysql   0/1     1            0           69s

NAME                                     DESIRED   CURRENT   READY   AGE
replicaset.apps/webapp-mysql-58c8d859b   1         1         0       69s

controlplane ~ ➜  k get nodes
NAME           STATUS     ROLES           AGE   VERSION
controlplane   NotReady   control-plane   13m   v1.35.0

controlplane ~ ➜  systemctl status kubelet
● kubelet.service - kubelet: The Kubernetes Node Agent
     Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
    Drop-In: /usr/lib/systemd/system/kubelet.service.d
             └─10-kubeadm.conf
     Active: active (running) since Sun 2026-11-01 15:54:50 UTC; 1min 46s ago
       Docs: https://kubernetes.io/docs/
   Main PID: 13169 (kubelet)
      Tasks: 23 (limit: 75883)
     Memory: 36.5M
        CPU: 1.557s
     CGroup: /system.slice/kubelet.service
             └─13169 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/k>

Nov 01 15:56:30 controlplane kubelet[13169]: E1101 15:56:30.147827   13169 pod_workers.go:1324] "Error syncing pod, skipping" err=>
Nov 01 15:56:30 controlplane kubelet[13169]: E1101 15:56:30.147857   13169 pod_workers.go:1324] "Error syncing pod, skipping" err=>
Nov 01 15:56:30 controlplane kubelet[13169]: E1101 15:56:30.270987   13169 kubelet.go:3130] "Container runtime network not ready" >
Nov 01 15:56:32 controlplane kubelet[13169]: E1101 15:56:32.147339   13169 pod_workers.go:1324] "Error syncing pod, skipping" err=>
Nov 01 15:56:32 controlplane kubelet[13169]: E1101 15:56:32.147445   13169 pod_workers.go:1324] "Error syncing pod, skipping" err=>
Nov 01 15:56:34 controlplane kubelet[13169]: E1101 15:56:34.147657   13169 pod_workers.go:1324] "Error syncing pod, skipping" err=>
Nov 01 15:56:34 controlplane kubelet[13169]: E1101 15:56:34.147808   13169 pod_workers.go:1324] "Error syncing pod, skipping" err=>
Nov 01 15:56:35 controlplane kubelet[13169]: E1101 15:56:35.272352   13169 kubelet.go:3130] "Container runtime network not ready" >
Nov 01 15:56:36 controlplane kubelet[13169]: E1101 15:56:36.147943   13169 pod_workers.go:1324] "Error syncing pod, skipping" err=>
Nov 01 15:56:36 controlplane kubelet[13169]: E1101 15:56:36.148064   13169 pod_workers.go:1324] "Error syncing pod, skipping" err=>

controlplane ~ ➜  k get pods -n kube-system 
NAME                                   READY   STATUS    RESTARTS   AGE
coredns-6f6c7df987-67g2l               1/1     Running   0          14m
coredns-6f6c7df987-hlzdx               1/1     Running   0          14m
etcd-controlplane                      1/1     Running   0          14m
kube-apiserver-controlplane            1/1     Running   0          14m
kube-controller-manager-controlplane   1/1     Running   0          14m
kube-proxy-9lpwd                       1/1     Running   0          14m
kube-scheduler-controlplane            1/1     Running   0          14m

controlplane ~ ➜  k get ds -A
NAMESPACE     NAME         DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
kube-system   kube-proxy   1         1         1       1            1           kubernetes.io/os=linux   14m

controlplane ~ ➜  ls -l /etc/cni/net.d/
total 0

controlplane ~ ➜  mkdir Scripts

controlplane ~ ➜  cd Scripts/

controlplane ~/Scripts ➜  #kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml

controlplane ~/Scripts ➜  curl -fL -o kube-flannel.yaml https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
100  4478  100  4478    0     0  12571      0 --:--:-- --:--:-- --:--:-- 12571

controlplane ~/Scripts ➜  ls -l
total 8
-rw-r--r-- 1 root root 4478 Nov  1 15:59 kube-flannel.yaml

controlplane ~/Scripts ➜  grep -irn cidr /etc/kubernetes/manifests/
/etc/kubernetes/manifests/kube-controller-manager.yaml:13:    - --allocate-node-cidrs=true
/etc/kubernetes/manifests/kube-controller-manager.yaml:18:    - --cluster-cidr=172.17.0.0/16

controlplane ~/Scripts ➜  vi kube-flannel.yaml 

controlplane ~/Scripts ➜  k apply -f kube-flannel.yaml 
namespace/kube-flannel created
serviceaccount/flannel created
clusterrole.rbac.authorization.k8s.io/flannel configured
clusterrolebinding.rbac.authorization.k8s.io/flannel unchanged
configmap/kube-flannel-cfg created
daemonset.apps/kube-flannel-ds created

controlplane ~/Scripts ➜  ls -l /etc/cni/net.d/
total 4
-rw-r--r-- 1 root root 292 Nov  1 16:01 10-flannel.conflist

controlplane ~/Scripts ➜  kubectl restart kubelet
error: unknown command "restart" for "kubectl"

controlplane ~/Scripts ✖ systemctl restart kubelet

controlplane ~/Scripts ➜  systemctl status kubelet
● kubelet.service - kubelet: The Kubernetes Node Agent
     Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
    Drop-In: /usr/lib/systemd/system/kubelet.service.d
             └─10-kubeadm.conf
     Active: active (running) since Sun 2026-11-01 16:02:22 UTC; 10s ago
       Docs: https://kubernetes.io/docs/
   Main PID: 18928 (kubelet)
      Tasks: 22 (limit: 75883)
     Memory: 29.3M
        CPU: 397ms
     CGroup: /system.slice/kubelet.service
             └─18928 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/k>

Nov 01 16:02:23 controlplane kubelet[18928]: E1101 16:02:23.344990   18928 prober_manager.go:197] "Startup probe already exists fo>
Nov 01 16:02:23 controlplane kubelet[18928]: E1101 16:02:23.344948   18928 kubelet.go:3342] "Failed creating a mirror pod" err="po>
Nov 01 16:02:23 controlplane kubelet[18928]: E1101 16:02:23.345027   18928 prober_manager.go:197] "Startup probe already exists fo>
Nov 01 16:02:23 controlplane kubelet[18928]: E1101 16:02:23.345058   18928 prober_manager.go:197] "Startup probe already exists fo>
Nov 01 16:02:24 controlplane kubelet[18928]: E1101 16:02:24.343612   18928 prober_manager.go:197] "Startup probe already exists fo>
Nov 01 16:02:24 controlplane kubelet[18928]: E1101 16:02:24.343633   18928 prober_manager.go:197] "Startup probe already exists fo>
Nov 01 16:02:24 controlplane kubelet[18928]: E1101 16:02:24.343645   18928 prober_manager.go:197] "Startup probe already exists fo>
Nov 01 16:02:24 controlplane kubelet[18928]: E1101 16:02:24.343674   18928 prober_manager.go:197] "Startup probe already exists fo>
Nov 01 16:02:30 controlplane kubelet[18928]: E1101 16:02:30.473508   18928 prober_manager.go:209] "Readiness probe already exists >
Nov 01 16:02:32 controlplane kubelet[18928]: E1101 16:02:32.548961   18928 prober_manager.go:209] "Readiness probe already exists >

controlplane ~/Scripts ➜  k get node -o wide
NAME           STATUS   ROLES           AGE   VERSION   INTERNAL-IP      EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION     CONTAINER-RUNTIME
controlplane   Ready    control-plane   20m   v1.35.0   10.244.105.169   <none>        Ubuntu 22.04.5 LTS   6.8.0-90-generic   containerd://1.7.22

controlplane ~/Scripts ➜  k get all
NAME                               READY   STATUS    RESTARTS   AGE
pod/mysql                          1/1     Running   0          7m59s
pod/webapp-mysql-58c8d859b-lpzwz   1/1     Running   0          7m59s

NAME                  TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/mysql         ClusterIP   172.20.134.37   <none>        3306/TCP         7m59s
service/web-service   NodePort    172.20.62.74    <none>        8080:30081/TCP   7m59s

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/webapp-mysql   1/1     1            1           7m59s

NAME                                     DESIRED   CURRENT   READY   AGE
replicaset.apps/webapp-mysql-58c8d859b   1         1         1       7m59s

controlplane ~/Scripts ➜  curl -sS http://10.244.105.169:30081
<!doctype html>
<title>Hello from Flask</title>
<body style="background: #39b54b;"></body>
<div style="color: #e4e4e4;
    text-align:  center;
    height: 90px;
    vertical-align:  middle;">

  
    <!-- <h1> DATABASE CONNECTION SUCCESSFUL !</h1> -->
    <img src="/static/img/success.jpg">
  

  
    <h2> Environment Variables: DB_Host=mysql; DB_Database=Not Set; DB_User=root; DB_Password=paswrd;  </h2>
  

  
    <p> From webapp-mysql-58c8d859b-lpzwz!</p>
  

  

</div>
controlplane ~/Scripts ➜  



════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
══════════════════════════════════════════════ Q.2 ═════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════

**Troubleshooting Test 2:** The same 2 tier application is having issues again. It must display a green web page on success. 
Click on the app tab at the top of your terminal to view your application. It is currently failed. Troubleshoot and fix the issue.


Stick to the given architecture. Use the same names and port numbers as given in the below architecture diagram. 
Feel free to edit, delete or recreate objects as necessary.

controlplane ~/Scripts ➜  # ================================ Q.2 ===================================

controlplane ~/Scripts ➜  

controlplane ~/Scripts ➜  k get all
NAME                               READY   STATUS    RESTARTS   AGE
pod/mysql                          1/1     Running   0          57s
pod/webapp-mysql-58c8d859b-nc9wv   1/1     Running   0          57s

NAME                  TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/mysql         ClusterIP   172.20.156.60   <none>        3306/TCP         57s
service/web-service   NodePort    172.20.91.225   <none>        8080:30081/TCP   57s

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/webapp-mysql   1/1     1            1           57s

NAME                                     DESIRED   CURRENT   READY   AGE
replicaset.apps/webapp-mysql-58c8d859b   1         1         1       57s

controlplane ~/Scripts ➜  curl -sS http://10.244.105.169:30081
curl: (7) Failed to connect to 10.244.105.169 port 30081 after 3062 ms: No route to host

controlplane ~/Scripts ✖ 

controlplane ~/Scripts ✖ k get pods -n kube-system 
NAME                                   READY   STATUS    RESTARTS      AGE
coredns-6f6c7df987-67g2l               1/1     Running   0             22m
coredns-6f6c7df987-hlzdx               1/1     Running   0             22m
etcd-controlplane                      1/1     Running   0             22m
kube-apiserver-controlplane            1/1     Running   0             22m
kube-controller-manager-controlplane   1/1     Running   0             22m
kube-proxy-n82ds                       0/1     Error     4 (62s ago)   98s
kube-scheduler-controlplane            1/1     Running   0             22m

controlplane ~/Scripts ➜  k logs kube-proxy-n82ds -n kube-system 
E1101 16:05:31.548442       1 run.go:74] "command failed" err="failed complete: open /var/lib/kube-proxy/configuration.conf: no such file or directory"

controlplane ~/Scripts ➜  k get ds -A
NAMESPACE      NAME              DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
kube-flannel   kube-flannel-ds   1         1         1       1            1           <none>                   4m53s
kube-system    kube-proxy        1         1         0       1            0           kubernetes.io/os=linux   2m16s

controlplane ~/Scripts ➜  ls -l /var/lib/kube-proxy/configuration.conf
ls: cannot access '/var/lib/kube-proxy/configuration.conf': No such file or directory

controlplane ~/Scripts ✖ k edit ds kube-proxy -n kube-system 
Edit cancelled, no changes made.

controlplane ~/Scripts ➜  ls -l /etc/kubernetes/manifests/
total 16
-rw------- 1 root root 2616 Nov  1 15:42 etcd.yaml
-rw------- 1 root root 3965 Nov  1 15:42 kube-apiserver.yaml
-rw------- 1 root root 3459 Nov  1 15:42 kube-controller-manager.yaml
-rw------- 1 root root 1726 Nov  1 15:42 kube-scheduler.yaml

controlplane ~/Scripts ➜  k edit ds kube-proxy -n kube-system 
Edit cancelled, no changes made.

controlplane ~/Scripts ➜  k get cm -A | grep proxy
kube-system       kube-proxy                                             2      28m

controlplane ~/Scripts ➜  k get cm kube-proxy -n kube-system -o yaml | grep .conf
  config.conf: |-
    apiVersion: kubeproxy.config.k8s.io/v1alpha1
      kubeconfig: /var/lib/kube-proxy/kubeconfig.conf
    configSyncPeriod: 0s
  kubeconfig.conf: |-
    kubeadm.kubernetes.io/component-config.hash: sha256:18238492c66d85cd87676223875eea97e931abd5bf402617aac40cede13ab4d3

controlplane ~/Scripts ➜  k edit ds kube-proxy -n kube-system 
daemonset.apps/kube-proxy edited

controlplane ~/Scripts ➜  

controlplane ~/Scripts ➜  k rollout restart ds kube-proxy -n kube-system 
daemonset.apps/kube-proxy restarted

controlplane ~/Scripts ➜  k rollout status ds kube-proxy -n kube-system 
daemon set "kube-proxy" successfully rolled out

controlplane ~/Scripts ➜  curl -sS http://10.244.105.169:30081
<!doctype html>
<title>Hello from Flask</title>
<body style="background: #39b54b;"></body>
<div style="color: #e4e4e4;
    text-align:  center;
    height: 90px;
    vertical-align:  middle;">

  
    <!-- <h1> DATABASE CONNECTION SUCCESSFUL !</h1> -->
    <img src="/static/img/success.jpg">
  

  
    <h2> Environment Variables: DB_Host=mysql; DB_Database=Not Set; DB_User=root; DB_Password=paswrd;  </h2>
  

  
    <p> From webapp-mysql-58c8d859b-nc9wv!</p>
  

  

</div>
controlplane ~/Scripts ➜  vi buff.yaml

controlplane ~/Scripts ➜  k edit ds kube-proxy -n kube-system 
Edit cancelled, no changes made.

controlplane ~/Scripts ➜  k get ds kube-proxy -n kube-system -o yaml >> buff.yaml 

controlplane ~/Scripts ➜  cat buff.yaml 
================

apiVersion: apps/v1
kind: DaemonSet
metadata:
  annotations:
    deprecated.daemonset.template.generation: "3"
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"apps/v1","kind":"DaemonSet","metadata":{"annotations":{"deprecated.daemonset.template.generation":"1"},"creationTimestamp":"2023-01-03T14:59:22Z","generation":1,"labels":{"k8s-app":"kube-proxy"},"name":"kube-proxy","namespace":"kube-system","resourceVersion":"384","uid":"d659f145-038d-4a56-8ec3-15888682569a"},"spec":{"revisionHistoryLimit":10,"selector":{"matchLabels":{"k8s-app":"kube-proxy"}},"template":{"metadata":{"creationTimestamp":null,"labels":{"k8s-app":"kube-proxy"}},"spec":{"containers":[{"command":["/usr/local/bin/kube-proxy","--config=/var/lib/kube-proxy/configuration.conf","--hostname-override=$(NODE_NAME)"],"env":[{"name":"NODE_NAME","valueFrom":{"fieldRef":{"apiVersion":"v1","fieldPath":"spec.nodeName"}}}],"image":"registry.k8s.io/kube-proxy:v1.26.0","imagePullPolicy":"IfNotPresent","name":"kube-proxy","resources":{},"securityContext":{"privileged":true},"terminationMessagePath":"/dev/termination-log","terminationMessagePolicy":"File","volumeMounts":[{"mountPath":"/var/lib/kube-proxy","name":"kube-proxy"},{"mountPath":"/run/xtables.lock","name":"xtables-lock"},{"mountPath":"/lib/modules","name":"lib-modules","readOnly":true}]}],"dnsPolicy":"ClusterFirst","hostNetwork":true,"nodeSelector":{"kubernetes.io/os":"linux"},"priorityClassName":"system-node-critical","restartPolicy":"Always","schedulerName":"default-scheduler","securityContext":{},"serviceAccount":"kube-proxy","serviceAccountName":"kube-proxy","terminationGracePeriodSeconds":30,"tolerations":[{"operator":"Exists"}],"volumes":[{"configMap":{"defaultMode":420,"name":"kube-proxy"},"name":"kube-proxy"},{"hostPath":{"path":"/run/xtables.lock","type":"FileOrCreate"},"name":"xtables-lock"},{"hostPath":{"path":"/lib/modules","type":""},"name":"lib-modules"}]}},"updateStrategy":{"rollingUpdate":{"maxSurge":0,"maxUnavailable":1},"type":"RollingUpdate"}}}
  creationTimestamp: "2026-11-01T16:04:05Z"
  generation: 3
  labels:
    k8s-app: kube-proxy
  name: kube-proxy
  namespace: kube-system
  resourceVersion: "3119"
  uid: 5969f05c-294d-4e93-af64-e52553b79ba0
spec:
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      k8s-app: kube-proxy
  template:
    metadata:
      annotations:
        kubectl.kubernetes.io/restartedAt: "2026-11-01T16:12:30Z"
      labels:
        k8s-app: kube-proxy
    spec:
      containers:
      - command:
        - /usr/local/bin/kube-proxy
        - --config=/var/lib/kube-proxy/config.conf
        - --hostname-override=$(NODE_NAME)
        env:
        - name: NODE_NAME
          valueFrom:
            fieldRef:
              apiVersion: v1
              fieldPath: spec.nodeName
        image: registry.k8s.io/kube-proxy:v1.26.0
        imagePullPolicy: IfNotPresent
        name: kube-proxy
        resources: {}
        securityContext:
          privileged: true
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
        volumeMounts:
        - mountPath: /var/lib/kube-proxy
          name: kube-proxy
        - mountPath: /run/xtables.lock
          name: xtables-lock
        - mountPath: /lib/modules
          name: lib-modules
          readOnly: true
      dnsPolicy: ClusterFirst
      hostNetwork: true
      nodeSelector:
        kubernetes.io/os: linux
      priorityClassName: system-node-critical
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      serviceAccount: kube-proxy
      serviceAccountName: kube-proxy
      terminationGracePeriodSeconds: 30
      tolerations:
      - operator: Exists
      volumes:
      - configMap:
          defaultMode: 420
          name: kube-proxy
        name: kube-proxy
      - hostPath:
          path: /run/xtables.lock
          type: FileOrCreate
        name: xtables-lock
      - hostPath:
          path: /lib/modules
          type: ""
        name: lib-modules
  updateStrategy:
    rollingUpdate:
      maxSurge: 0
      maxUnavailable: 1
    type: RollingUpdate
status:
  currentNumberScheduled: 1
  desiredNumberScheduled: 1
  numberAvailable: 1
  numberMisscheduled: 0
  numberReady: 1
  observedGeneration: 3
  updatedNumberScheduled: 1

controlplane ~/Scripts ➜  



════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
══════════════ Practice Test - Control Plane Failure ═══════════ 01.11.2026 ════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════

════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
══════════════════════════════════════════════ Q.1 ═════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════

The cluster is broken. We tried deploying an application but it's not working. Troubleshoot and fix the issue.


Start looking at the deployments.


         Welcome to the KodeKloud Hands-On lab                                                                                                      
    __ ______  ____  ________ __ __    ____  __  ______ 
   / //_/ __ \/ __ \/ ____/ //_// /   / __ \/ / / / __ \
  / ,< / / / / / / / __/ / ,<  / /   / / / / / / / / / /
 / /| / /_/ / /_/ / /___/ /| |/ /___/ /_/ / /_/ / /_/ / 
/_/ |_\____/_____/_____/_/ |_/_____/\____/\____/_____/  
                                                        
               All rights reserved                                                                                                                  

controlplane ~ ➜  k get deploy -A
NAMESPACE     NAME      READY   UP-TO-DATE   AVAILABLE   AGE
default       app       0/1     1            0           7m44s
kube-system   coredns   2/2     2            2           15m

controlplane ~ ➜  k get nodes 
NAME           STATUS   ROLES           AGE   VERSION
controlplane   Ready    control-plane   15m   v1.35.0

controlplane ~ ➜  k get pods -n kube-system 
NAME                                   READY   STATUS             RESTARTS        AGE
coredns-6f6c7df987-8zjhc               1/1     Running            0               15m
coredns-6f6c7df987-wkx8w               1/1     Running            0               15m
etcd-controlplane                      1/1     Running            0               16m
kube-apiserver-controlplane            1/1     Running            0               16m
kube-controller-manager-controlplane   1/1     Running            0               16m
kube-proxy-q7kk7                       1/1     Running            0               15m
kube-scheduler-controlplane            0/1     CrashLoopBackOff   6 (2m37s ago)   8m20s

controlplane ~ ➜  k logs kube-scheduler-controlplane -n kube-system 

controlplane ~ ➜  journalctl -u kubelet
Nov 01 16:08:04 controlplane kubelet[1933]: E1101 16:08:04.031659    1933 run.go:72] "command failed" err="failed to load kubelet >
Nov 01 16:08:05 controlplane kubelet[2459]: I1101 16:08:05.593623    2459 server.go:525] "Kubelet version" kubeletVersion="v1.35.0"
Nov 01 16:08:05 controlplane kubelet[2459]: I1101 16:08:05.593651    2459 server.go:527] "Golang settings" GOGC="" GOMAXPROCS="" G>
Nov 01 16:08:05 controlplane kubelet[2459]: I1101 16:08:05.593663    2459 watchdog_linux.go:95] "Systemd watchdog is not enabled"
Nov 01 16:08:05 controlplane kubelet[2459]: I1101 16:08:05.593667    2459 watchdog_linux.go:138] "Systemd watchdog is not enabled >
Nov 01 16:08:05 controlplane kubelet[2459]: I1101 16:08:05.593797    2459 server.go:951] "Client rotation is on, will bootstrap in>
Nov 01 16:08:05 controlplane kubelet[2459]: E1101 16:08:05.706034    2459 certificate_manager.go:596] "Failed while requesting a s>
Nov 01 16:08:05 controlplane kubelet[2459]: I1101 16:08:05.706419    2459 dynamic_cafile_content.go:161] "Starting controller" nam>
Nov 01 16:08:05 controlplane kubelet[2459]: E1101 16:08:05.708277    2459 log.go:32] "RuntimeConfig from runtime service failed" e>
Nov 01 16:08:05 controlplane kubelet[2459]: I1101 16:08:05.708315    2459 server.go:1395] "CRI implementation should be updated to>
Nov 01 16:08:05 controlplane kubelet[2459]: E1101 16:08:05.712046    2459 info.go:104] Failed to get disk map: could not parse dev>
Nov 01 16:08:05 controlplane kubelet[2459]: I1101 16:08:05.786795    2459 server.go:775] "--cgroups-per-qos enabled, but --cgroup->
Nov 01 16:08:05 controlplane kubelet[2459]: I1101 16:08:05.792602    2459 container_manager_linux.go:272] "Container manager verif>
Nov 01 16:08:05 controlplane kubelet[2459]: I1101 16:08:05.792635    2459 container_manager_linux.go:277] "Creating Container Mana>
Nov 01 16:08:05 controlplane kubelet[2459]: I1101 16:08:05.792805    2459 topology_manager.go:143] "Creating topology manager with>
Nov 01 16:08:05 controlplane kubelet[2459]: I1101 16:08:05.792813    2459 container_manager_linux.go:308] "Creating device plugin >
Nov 01 16:08:05 controlplane kubelet[2459]: I1101 16:08:05.792892    2459 container_manager_linux.go:317] "Creating Dynamic Resour>
Nov 01 16:08:05 controlplane kubelet[2459]: I1101 16:08:05.912119    2459 state_mem.go:41] "Initialized" logger="CPUManager state >
Nov 01 16:08:05 controlplane kubelet[2459]: I1101 16:08:05.912359    2459 kubelet.go:482] "Attempting to sync node with API server"
Nov 01 16:08:05 controlplane kubelet[2459]: I1101 16:08:05.912371    2459 kubelet.go:383] "Adding static pod path" path="/etc/kube>
Nov 01 16:08:05 controlplane kubelet[2459]: I1101 16:08:05.912385    2459 kubelet.go:394] "Adding apiserver pod source"
Nov 01 16:08:05 controlplane kubelet[2459]: I1101 16:08:05.912394    2459 apiserver.go:42] "Waiting for node sync before watching >
Nov 01 16:08:05 controlplane kubelet[2459]: I1101 16:08:05.913026    2459 kuberuntime_manager.go:294] "Container runtime initializ>
Nov 01 16:08:05 controlplane kubelet[2459]: I1101 16:08:05.913834    2459 kubelet.go:943] "Not starting ClusterTrustBundle informe>
Nov 01 16:08:05 controlplane kubelet[2459]: I1101 16:08:05.913848    2459 kubelet.go:970] "Not starting PodCertificateRequest mana>
Nov 01 16:08:05 controlplane kubelet[2459]: W1101 16:08:05.913885    2459 probe.go:272] Flexvolume plugin directory at /usr/libexe>
Nov 01 16:08:05 controlplane kubelet[2459]: I1101 16:08:05.920163    2459 server.go:1257] "Started kubelet"
Nov 01 16:08:05 controlplane kubelet[2459]: I1101 16:08:05.920200    2459 server.go:182] "Starting to listen" address="0.0.0.0" po>
Nov 01 16:08:05 controlplane kubelet[2459]: I1101 16:08:05.920222    2459 ratelimit.go:56] "Setting rate limiting for endpoint" se>
Nov 01 16:08:05 controlplane kubelet[2459]: I1101 16:08:05.920288    2459 server_v1.go:49] "podresources" method="list" useActiveP>
Nov 01 16:08:05 controlplane kubelet[2459]: E1101 16:08:05.920482    2459 event.go:368] "Unable to write event (may retry after sl>
Nov 01 16:08:05 controlplane kubelet[2459]: I1101 16:08:05.920602    2459 server.go:254] "Starting to serve the podresources API" >
Nov 01 16:08:05 controlplane kubelet[2459]: I1101 16:08:05.921832    2459 server.go:317] "Adding debug handlers to kubelet server"
Nov 01 16:08:05 controlplane kubelet[2459]: E1101 16:08:05.922531    2459 kubelet.go:1656] "Image garbage collection failed once. >
Nov 01 16:08:05 controlplane kubelet[2459]: I1101 16:08:05.922592    2459 fs_resource_analyzer.go:69] "Starting FS ResourceAnalyze>
Nov 01 16:08:05 controlplane kubelet[2459]: I1101 16:08:05.922603    2459 dynamic_serving_content.go:135] "Starting controller" na>
Nov 01 16:08:05 controlplane kubelet[2459]: E1101 16:08:05.922627    2459 oomparser.go:149] exiting analyzeLines. OOM events will >

controlplane ~ ➜  journalctl -u kubelet | grep "]: E"
Nov 01 16:08:04 controlplane kubelet[1933]: E1101 16:08:04.031659    1933 run.go:72] "command failed" err="failed to load kubelet config file, path: /var/lib/kubelet/config.yaml, error: failed to load Kubelet config file /var/lib/kubelet/config.yaml, error failed to read kubelet config file \"/var/lib/kubelet/config.yaml\", error: open /var/lib/kubelet/config.yaml: no such file or directory"
Nov 01 16:08:05 controlplane kubelet[2459]: E1101 16:08:05.706034    2459 certificate_manager.go:596] "Failed while requesting a signed certificate from the control plane" err="cannot create certificate signing request: Post \"https://10.244.162.146:6443/apis/certificates.k8s.io/v1/certificatesigningrequests\": dial tcp 10.244.162.146:6443: connect: connection refused" logger="kubernetes.io/kube-apiserver-client-kubelet.UnhandledError"
Nov 01 16:08:05 controlplane kubelet[2459]: E1101 16:08:05.708277    2459 log.go:32] "RuntimeConfig from runtime service failed" err="rpc error: code = Unimplemented desc = unknown method RuntimeConfig for service runtime.v1.RuntimeService"
Nov 01 16:08:05 controlplane kubelet[2459]: E1101 16:08:05.712046    2459 info.go:104] Failed to get disk map: could not parse device numbers from  for device md0
Nov 01 16:08:05 controlplane kubelet[2459]: E1101 16:08:05.920482    2459 event.go:368] "Unable to write event (may retry after sleeping)" err="Post \"https://10.244.162.146:6443/api/v1/namespaces/default/events\": dial tcp 10.244.162.146:6443: connect: connection refused" event="&Event{ObjectMeta:{controlplane.18e3f792aa6cd65b  default    0 0001-01-01 00:00:00 +0000 UTC <nil> <nil> map[] map[] [] [] []},InvolvedObject:ObjectReference{Kind:Node,Namespace:,Name:controlplane,UID:,APIVersion:v1,ResourceVersion:,FieldPath:,},Reason:Starting,Message:Starting kubelet.,Source:EventSource{Component:kubelet,Host:controlplane,},FirstTimestamp:2026-11-01 16:08:05.920134747 +0000 UTC m=+0.527989446,LastTimestamp:2026-11-01 16:08:05.920134747 +0000 UTC m=+0.527989446,Count:1,Type:Normal,EventTime:0001-01-01 00:00:00 +0000 UTC,Series:nil,Action:,Related:nil,ReportingController:kubelet,ReportingInstance:controlplane,}"
Nov 01 16:08:05 controlplane kubelet[2459]: E1101 16:08:05.922531    2459 kubelet.go:1656] "Image garbage collection failed once. Stats initialization may not have completed yet" err="invalid capacity 0 on image filesystem"
Nov 01 16:08:05 controlplane kubelet[2459]: E1101 16:08:05.922627    2459 oomparser.go:149] exiting analyzeLines. OOM events will not be reported.
Nov 01 16:08:05 controlplane kubelet[2459]: E1101 16:08:05.922663    2459 kubelet_node_status.go:392] "Error getting the current node from lister" err="node \"controlplane\" not found"
Nov 01 16:08:05 controlplane kubelet[2459]: E1101 16:08:05.922897    2459 controller.go:201] "Failed to ensure lease exists, will retry" err="Get \"https://10.244.162.146:6443/apis/coordination.k8s.io/v1/namespaces/kube-node-lease/leases/controlplane?timeout=10s\": dial tcp 10.244.162.146:6443: connect: connection refused" interval="200ms"
Nov 01 16:08:05 controlplane kubelet[2459]: E1101 16:08:05.923877    2459 oomparser.go:149] exiting analyzeLines. OOM events will not be reported.
Nov 01 16:08:05 controlplane kubelet[2459]: E1101 16:08:05.936105    2459 kubelet.go:2525] "Skipping pod synchronization" err="[container runtime status check may not have completed yet, PLEG is not healthy: pleg has yet to be successful]"
Nov 01 16:08:06 controlplane kubelet[2459]: E1101 16:08:06.022993    2459 kubelet_node_status.go:392] "Error getting the current node from lister" err="node \"controlplane\" not found"
Nov 01 16:08:06 controlplane kubelet[2459]: E1101 16:08:06.036819    2459 kubelet.go:2525] "Skipping pod synchronization" err="container runtime status check may not have completed yet"
Nov 01 16:08:06 controlplane kubelet[2459]: E1101 16:08:06.073779    2459 manager.go:525] "Failed to read data from checkpoint" err="checkpoint is not found" checkpoint="kubelet_internal_checkpoint"
Nov 01 16:08:06 controlplane kubelet[2459]: E1101 16:08:06.074524    2459 eviction_manager.go:272] "eviction manager: failed to check if we have separate container filesystem. Ignoring." err="no imagefs label for configured runtime"
Nov 01 16:08:06 controlplane kubelet[2459]: E1101 16:08:06.074543    2459 eviction_manager.go:297] "Eviction manager: failed to get summary stats" err="failed to get node info: node \"controlplane\" not found"
Nov 01 16:08:06 controlplane kubelet[2459]: E1101 16:08:06.123973    2459 controller.go:201] "Failed to ensure lease exists, will retry" err="Get \"https://10.244.162.146:6443/apis/coordination.k8s.io/v1/namespaces/kube-node-lease/leases/controlplane?timeout=10s\": dial tcp 10.244.162.146:6443: connect: connection refused" interval="400ms"
Nov 01 16:08:06 controlplane kubelet[2459]: E1101 16:08:06.176006    2459 kubelet_node_status.go:106] "Unable to register node with API server" err="Post \"https://10.244.162.146:6443/api/v1/nodes\": dial tcp 10.244.162.146:6443: connect: connection refused" node="controlplane"
Nov 01 16:08:06 controlplane kubelet[2459]: E1101 16:08:06.272716    2459 kubelet.go:3336] "No need to create a mirror pod, since failed to get node info from the cluster" err="node \"controlplane\" not found" node="controlplane"
Nov 01 16:08:06 controlplane kubelet[2459]: E1101 16:08:06.284027    2459 kubelet.go:3336] "No need to create a mirror pod, since failed to get node info from the cluster" err="node \"controlplane\" not found" node="controlplane"
Nov 01 16:08:06 controlplane kubelet[2459]: E1101 16:08:06.293832    2459 kubelet.go:3336] "No need to create a mirror pod, since failed to get node info from the cluster" err="node \"controlplane\" not found" node="controlplane"
Nov 01 16:08:06 controlplane kubelet[2459]: E1101 16:08:06.296591    2459 kubelet.go:3336] "No need to create a mirror pod, since failed to get node info from the cluster" err="node \"controlplane\" not found" node="controlplane"
Nov 01 16:08:06 controlplane kubelet[2459]: E1101 16:08:06.377745    2459 kubelet_node_status.go:106] "Unable to register node with API server" err="Post \"https://10.244.162.146:6443/api/v1/nodes\": dial tcp 10.244.162.146:6443: connect: connection refused" node="controlplane"
Nov 01 16:08:06 controlplane kubelet[2459]: E1101 16:08:06.525248    2459 controller.go:201] "Failed to ensure lease exists, will retry" err="Get \"https://10.244.162.146:6443/apis/coordination.k8s.io/v1/namespaces/kube-node-lease/leases/controlplane?timeout=10s\": dial tcp 10.244.162.146:6443: connect: connection refused" interval="800ms"
Nov 01 16:08:06 controlplane kubelet[2459]: E1101 16:08:06.779367    2459 kubelet_node_status.go:106] "Unable to register node with API server" err="Post \"https://10.244.162.146:6443/api/v1/nodes\": dial tcp 10.244.162.146:6443: connect: connection refused" node="controlplane"
Nov 01 16:08:07 controlplane kubelet[2459]: E1101 16:08:07.326330    2459 controller.go:201] "Failed to ensure lease exists, will retry" err="Get \"https://10.244.162.146:6443/apis/coordination.k8s.io/v1/namespaces/kube-node-lease/leases/controlplane?timeout=10s\": dial tcp 10.244.162.146:6443: connect: connection refused" interval="1.6s"
Nov 01 16:08:07 controlplane kubelet[2459]: E1101 16:08:07.580583    2459 kubelet_node_status.go:106] "Unable to register node with API server" err="Post \"https://10.244.162.146:6443/api/v1/nodes\": dial tcp 10.244.162.146:6443: connect: connection refused" node="controlplane"
Nov 01 16:08:07 controlplane kubelet[2459]: E1101 16:08:07.876616    2459 certificate_manager.go:596] "Failed while requesting a signed certificate from the control plane" err="cannot create certificate signing request: Post \"https://10.244.162.146:6443/apis/certificates.k8s.io/v1/certificatesigningrequests\": dial tcp 10.244.162.146:6443: connect: connection refused" logger="kubernetes.io/kube-apiserver-client-kubelet.UnhandledError"
Nov 01 16:08:08 controlplane kubelet[2459]: E1101 16:08:08.927469    2459 controller.go:201] "Failed to ensure lease exists, will retry" err="Get \"https://10.244.162.146:6443/apis/coordination.k8s.io/v1/namespaces/kube-node-lease/leases/controlplane?timeout=10s\": dial tcp 10.244.162.146:6443: connect: connection refused" interval="3.2s"
Nov 01 16:08:08 controlplane kubelet[2459]: E1101 16:08:08.942494    2459 kubelet.go:3336] "No need to create a mirror pod, since failed to get node info from the cluster" err="node \"controlplane\" not found" node="controlplane"
Nov 01 16:08:08 controlplane kubelet[2459]: E1101 16:08:08.942550    2459 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:08:08 controlplane kubelet[2459]: E1101 16:08:08.943554    2459 kubelet.go:3336] "No need to create a mirror pod, since failed to get node info from the cluster" err="node \"controlplane\" not found" node="controlplane"
Nov 01 16:08:08 controlplane kubelet[2459]: E1101 16:08:08.943585    2459 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/etcd-controlplane" containerName="etcd"
Nov 01 16:08:10 controlplane kubelet[2459]: E1101 16:08:10.039919    2459 kubelet.go:3336] "No need to create a mirror pod, since failed to get node info from the cluster" err="node \"controlplane\" not found" node="controlplane"
Nov 01 16:08:10 controlplane kubelet[2459]: E1101 16:08:10.039973    2459 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-controller-manager-controlplane" containerName="kube-controller-manager"
Nov 01 16:08:10 controlplane kubelet[2459]: E1101 16:08:10.041234    2459 kubelet.go:3336] "No need to create a mirror pod, since failed to get node info from the cluster" err="node \"controlplane\" not found" node="controlplane"
Nov 01 16:08:10 controlplane kubelet[2459]: E1101 16:08:10.041276    2459 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:08:10 controlplane kubelet[2459]: E1101 16:08:10.041505    2459 kubelet.go:3336] "No need to create a mirror pod, since failed to get node info from the cluster" err="node \"controlplane\" not found" node="controlplane"
Nov 01 16:08:10 controlplane kubelet[2459]: E1101 16:08:10.041535    2459 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-apiserver-controlplane" containerName="kube-apiserver"
Nov 01 16:08:10 controlplane kubelet[2459]: E1101 16:08:10.041724    2459 kubelet.go:3336] "No need to create a mirror pod, since failed to get node info from the cluster" err="node \"controlplane\" not found" node="controlplane"
Nov 01 16:08:10 controlplane kubelet[2459]: E1101 16:08:10.041754    2459 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/etcd-controlplane" containerName="etcd"
Nov 01 16:08:10 controlplane kubelet[2459]: E1101 16:08:10.339648    2459 kubelet.go:3342] "Failed creating a mirror pod" err="pods \"etcd-controlplane\" is forbidden: no PriorityClass with name system-node-critical was found" pod="kube-system/etcd-controlplane"
Nov 01 16:08:10 controlplane kubelet[2459]: E1101 16:08:10.340695    2459 kubelet.go:3342] "Failed creating a mirror pod" err="pods \"kube-apiserver-controlplane\" is forbidden: no PriorityClass with name system-node-critical was found" pod="kube-system/kube-apiserver-controlplane"
Nov 01 16:08:10 controlplane kubelet[2459]: E1101 16:08:10.341846    2459 kubelet.go:3342] "Failed creating a mirror pod" err="pods \"kube-controller-manager-controlplane\" is forbidden: no PriorityClass with name system-node-critical was found" pod="kube-system/kube-controller-manager-controlplane"
Nov 01 16:08:10 controlplane kubelet[2459]: E1101 16:08:10.342549    2459 kubelet.go:3342] "Failed creating a mirror pod" err="pods \"kube-scheduler-controlplane\" is forbidden: no PriorityClass with name system-node-critical was found" pod="kube-system/kube-scheduler-controlplane"
Nov 01 16:08:11 controlplane kubelet[2459]: E1101 16:08:11.043600    2459 kubelet.go:3342] "Failed creating a mirror pod" err="pods \"kube-controller-manager-controlplane\" is forbidden: no PriorityClass with name system-node-critical was found" pod="kube-system/kube-controller-manager-controlplane"
Nov 01 16:08:11 controlplane kubelet[2459]: E1101 16:08:11.043641    2459 kubelet.go:3342] "Failed creating a mirror pod" err="pods \"kube-apiserver-controlplane\" is forbidden: no PriorityClass with name system-node-critical was found" pod="kube-system/kube-apiserver-controlplane"
Nov 01 16:08:11 controlplane kubelet[2459]: E1101 16:08:11.043649    2459 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-controller-manager-controlplane" containerName="kube-controller-manager"
Nov 01 16:08:11 controlplane kubelet[2459]: E1101 16:08:11.043676    2459 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-apiserver-controlplane" containerName="kube-apiserver"
Nov 01 16:08:11 controlplane kubelet[2459]: E1101 16:08:11.124094    2459 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/etcd-controlplane" containerName="etcd"
Nov 01 16:08:12 controlplane kubelet[2459]: E1101 16:08:12.043164    2459 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/etcd-controlplane" containerName="etcd"
Nov 01 16:08:12 controlplane kubelet[2459]: E1101 16:08:12.045583    2459 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-apiserver-controlplane" containerName="kube-apiserver"
Nov 01 16:08:13 controlplane kubelet[2459]: E1101 16:08:13.044962    2459 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-apiserver-controlplane" containerName="kube-apiserver"
Nov 01 16:08:13 controlplane kubelet[2459]: E1101 16:08:13.651081    2459 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:08:14 controlplane kubelet[2459]: E1101 16:08:14.046028    2459 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:08:15 controlplane kubelet[2459]: E1101 16:08:15.412935    2459 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-controller-manager-controlplane" containerName="kube-controller-manager"
Nov 01 16:08:16 controlplane kubelet[2459]: E1101 16:08:16.049108    2459 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-controller-manager-controlplane" containerName="kube-controller-manager"
Nov 01 16:08:18 controlplane kubelet[2459]: E1101 16:08:18.065027    2459 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-apiserver-controlplane" containerName="kube-apiserver"
Nov 01 16:08:21 controlplane kubelet[2459]: E1101 16:08:21.123491    2459 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/etcd-controlplane" containerName="etcd"
Nov 01 16:08:22 controlplane kubelet[2459]: E1101 16:08:22.056709    2459 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/etcd-controlplane" containerName="etcd"
Nov 01 16:08:23 controlplane kubelet[2459]: E1101 16:08:23.653017    2459 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:08:24 controlplane kubelet[3817]: E1101 16:08:24.139790    3817 log.go:32] "RuntimeConfig from runtime service failed" err="rpc error: code = Unimplemented desc = unknown method RuntimeConfig for service runtime.v1.RuntimeService"
Nov 01 16:08:24 controlplane kubelet[3817]: E1101 16:08:24.142663    3817 info.go:104] Failed to get disk map: could not parse device numbers from  for device md0
Nov 01 16:08:24 controlplane kubelet[3817]: E1101 16:08:24.224241    3817 oomparser.go:149] exiting analyzeLines. OOM events will not be reported.
Nov 01 16:08:24 controlplane kubelet[3817]: E1101 16:08:24.239983    3817 oomparser.go:149] exiting analyzeLines. OOM events will not be reported.
Nov 01 16:08:24 controlplane kubelet[3817]: E1101 16:08:24.242899    3817 kubelet.go:1656] "Image garbage collection failed once. Stats initialization may not have completed yet" err="invalid capacity 0 on image filesystem"
Nov 01 16:08:24 controlplane kubelet[3817]: E1101 16:08:24.309249    3817 kubelet.go:2525] "Skipping pod synchronization" err="[container runtime status check may not have completed yet, PLEG is not healthy: pleg has yet to be successful]"
Nov 01 16:08:24 controlplane kubelet[3817]: E1101 16:08:24.369909    3817 manager.go:525] "Failed to read data from checkpoint" err="checkpoint is not found" checkpoint="kubelet_internal_checkpoint"
Nov 01 16:08:24 controlplane kubelet[3817]: E1101 16:08:24.407697    3817 eviction_manager.go:272] "eviction manager: failed to check if we have separate container filesystem. Ignoring." err="no imagefs label for configured runtime"
Nov 01 16:08:24 controlplane kubelet[3817]: E1101 16:08:24.414108    3817 kubelet.go:3342] "Failed creating a mirror pod" err="pods \"kube-scheduler-controlplane\" already exists" pod="kube-system/kube-scheduler-controlplane"
Nov 01 16:08:24 controlplane kubelet[3817]: E1101 16:08:24.414188    3817 kubelet.go:3342] "Failed creating a mirror pod" err="pods \"kube-apiserver-controlplane\" already exists" pod="kube-system/kube-apiserver-controlplane"
Nov 01 16:08:24 controlplane kubelet[3817]: E1101 16:08:24.414191    3817 kubelet.go:3342] "Failed creating a mirror pod" err="pods \"kube-controller-manager-controlplane\" already exists" pod="kube-system/kube-controller-manager-controlplane"
Nov 01 16:08:24 controlplane kubelet[3817]: E1101 16:08:24.414235    3817 kubelet.go:3342] "Failed creating a mirror pod" err="pods \"etcd-controlplane\" already exists" pod="kube-system/etcd-controlplane"
Nov 01 16:08:25 controlplane kubelet[3817]: E1101 16:08:25.320234    3817 kubelet.go:3342] "Failed creating a mirror pod" err="pods \"kube-apiserver-controlplane\" already exists" pod="kube-system/kube-apiserver-controlplane"
Nov 01 16:08:25 controlplane kubelet[3817]: E1101 16:08:25.320253    3817 kubelet.go:3342] "Failed creating a mirror pod" err="pods \"kube-controller-manager-controlplane\" already exists" pod="kube-system/kube-controller-manager-controlplane"
Nov 01 16:08:25 controlplane kubelet[3817]: E1101 16:08:25.320261    3817 kubelet.go:3342] "Failed creating a mirror pod" err="pods \"etcd-controlplane\" already exists" pod="kube-system/etcd-controlplane"
Nov 01 16:08:25 controlplane kubelet[3817]: E1101 16:08:25.320272    3817 kubelet.go:3342] "Failed creating a mirror pod" err="pods \"kube-scheduler-controlplane\" already exists" pod="kube-system/kube-scheduler-controlplane"
Nov 01 16:08:25 controlplane kubelet[3817]: E1101 16:08:25.320298    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-apiserver-controlplane" containerName="kube-apiserver"
Nov 01 16:08:25 controlplane kubelet[3817]: E1101 16:08:25.320359    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-controller-manager-controlplane" containerName="kube-controller-manager"
Nov 01 16:08:25 controlplane kubelet[3817]: E1101 16:08:25.320419    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/etcd-controlplane" containerName="etcd"
Nov 01 16:08:25 controlplane kubelet[3817]: E1101 16:08:25.320495    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:08:26 controlplane kubelet[3817]: E1101 16:08:26.320396    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-apiserver-controlplane" containerName="kube-apiserver"
Nov 01 16:08:26 controlplane kubelet[3817]: E1101 16:08:26.320428    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/etcd-controlplane" containerName="etcd"
Nov 01 16:08:26 controlplane kubelet[3817]: E1101 16:08:26.320520    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:08:26 controlplane kubelet[3817]: E1101 16:08:26.320602    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-controller-manager-controlplane" containerName="kube-controller-manager"
Nov 01 16:08:27 controlplane kubelet[3817]: E1101 16:08:27.321929    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/etcd-controlplane" containerName="etcd"
Nov 01 16:09:09 controlplane kubelet[3817]: E1101 16:09:09.186324    3817 log.go:32] "RunPodSandbox from runtime service failed" err="rpc error: code = Unknown desc = failed to setup network for sandbox \"3546128f6be7e7dd483287b7785bf564d3b43e2c1d2be1e31363e4fb66be6a1b\": plugin type=\"flannel\" failed (add): loadFlannelSubnetEnv failed: open /run/flannel/subnet.env: no such file or directory"
Nov 01 16:09:09 controlplane kubelet[3817]: E1101 16:09:09.186368    3817 kuberuntime_sandbox.go:71] "Failed to create sandbox for pod" err="rpc error: code = Unknown desc = failed to setup network for sandbox \"3546128f6be7e7dd483287b7785bf564d3b43e2c1d2be1e31363e4fb66be6a1b\": plugin type=\"flannel\" failed (add): loadFlannelSubnetEnv failed: open /run/flannel/subnet.env: no such file or directory" pod="kube-system/coredns-6f6c7df987-wkx8w"
Nov 01 16:09:09 controlplane kubelet[3817]: E1101 16:09:09.186383    3817 kuberuntime_manager.go:1558] "CreatePodSandbox for pod failed" err="rpc error: code = Unknown desc = failed to setup network for sandbox \"3546128f6be7e7dd483287b7785bf564d3b43e2c1d2be1e31363e4fb66be6a1b\": plugin type=\"flannel\" failed (add): loadFlannelSubnetEnv failed: open /run/flannel/subnet.env: no such file or directory" pod="kube-system/coredns-6f6c7df987-wkx8w"
Nov 01 16:09:09 controlplane kubelet[3817]: E1101 16:09:09.186427    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"CreatePodSandbox\" for \"coredns-6f6c7df987-wkx8w_kube-system(fab85a1a-046a-4fb0-9bee-07d37d1350d8)\" with CreatePodSandboxError: \"Failed to create sandbox for pod \\\"coredns-6f6c7df987-wkx8w_kube-system(fab85a1a-046a-4fb0-9bee-07d37d1350d8)\\\": rpc error: code = Unknown desc = failed to setup network for sandbox \\\"3546128f6be7e7dd483287b7785bf564d3b43e2c1d2be1e31363e4fb66be6a1b\\\": plugin type=\\\"flannel\\\" failed (add): loadFlannelSubnetEnv failed: open /run/flannel/subnet.env: no such file or directory\"" pod="kube-system/coredns-6f6c7df987-wkx8w" podUID="fab85a1a-046a-4fb0-9bee-07d37d1350d8"
Nov 01 16:09:09 controlplane kubelet[3817]: E1101 16:09:09.307045    3817 log.go:32] "RunPodSandbox from runtime service failed" err="rpc error: code = Unknown desc = failed to setup network for sandbox \"fa47dd054730cea9507dd53400b4000d88b73602ae3a4581ab826aee89c03dde\": plugin type=\"flannel\" failed (add): loadFlannelSubnetEnv failed: open /run/flannel/subnet.env: no such file or directory"
Nov 01 16:09:09 controlplane kubelet[3817]: E1101 16:09:09.307086    3817 kuberuntime_sandbox.go:71] "Failed to create sandbox for pod" err="rpc error: code = Unknown desc = failed to setup network for sandbox \"fa47dd054730cea9507dd53400b4000d88b73602ae3a4581ab826aee89c03dde\": plugin type=\"flannel\" failed (add): loadFlannelSubnetEnv failed: open /run/flannel/subnet.env: no such file or directory" pod="kube-system/coredns-7d764666f9-k6bmr"
Nov 01 16:09:09 controlplane kubelet[3817]: E1101 16:09:09.307098    3817 kuberuntime_manager.go:1558] "CreatePodSandbox for pod failed" err="rpc error: code = Unknown desc = failed to setup network for sandbox \"fa47dd054730cea9507dd53400b4000d88b73602ae3a4581ab826aee89c03dde\": plugin type=\"flannel\" failed (add): loadFlannelSubnetEnv failed: open /run/flannel/subnet.env: no such file or directory" pod="kube-system/coredns-7d764666f9-k6bmr"
Nov 01 16:09:09 controlplane kubelet[3817]: E1101 16:09:09.307130    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"CreatePodSandbox\" for \"coredns-7d764666f9-k6bmr_kube-system(567d277a-863d-4080-8c6d-cc193f7ce9c6)\" with CreatePodSandboxError: \"Failed to create sandbox for pod \\\"coredns-7d764666f9-k6bmr_kube-system(567d277a-863d-4080-8c6d-cc193f7ce9c6)\\\": rpc error: code = Unknown desc = failed to setup network for sandbox \\\"fa47dd054730cea9507dd53400b4000d88b73602ae3a4581ab826aee89c03dde\\\": plugin type=\\\"flannel\\\" failed (add): loadFlannelSubnetEnv failed: open /run/flannel/subnet.env: no such file or directory\"" pod="kube-system/coredns-7d764666f9-k6bmr" podUID="567d277a-863d-4080-8c6d-cc193f7ce9c6"
Nov 01 16:09:09 controlplane kubelet[3817]: E1101 16:09:09.368670    3817 log.go:32] "RunPodSandbox from runtime service failed" err="rpc error: code = Unknown desc = failed to setup network for sandbox \"bd7557b4974b8d8e02f03dfa979930090c0d97022ccdbb0961a5074ce31411af\": plugin type=\"flannel\" failed (add): loadFlannelSubnetEnv failed: open /run/flannel/subnet.env: no such file or directory"
Nov 01 16:09:09 controlplane kubelet[3817]: E1101 16:09:09.368721    3817 kuberuntime_sandbox.go:71] "Failed to create sandbox for pod" err="rpc error: code = Unknown desc = failed to setup network for sandbox \"bd7557b4974b8d8e02f03dfa979930090c0d97022ccdbb0961a5074ce31411af\": plugin type=\"flannel\" failed (add): loadFlannelSubnetEnv failed: open /run/flannel/subnet.env: no such file or directory" pod="kube-system/coredns-6f6c7df987-8zjhc"
Nov 01 16:09:09 controlplane kubelet[3817]: E1101 16:09:09.368735    3817 kuberuntime_manager.go:1558] "CreatePodSandbox for pod failed" err="rpc error: code = Unknown desc = failed to setup network for sandbox \"bd7557b4974b8d8e02f03dfa979930090c0d97022ccdbb0961a5074ce31411af\": plugin type=\"flannel\" failed (add): loadFlannelSubnetEnv failed: open /run/flannel/subnet.env: no such file or directory" pod="kube-system/coredns-6f6c7df987-8zjhc"
Nov 01 16:09:09 controlplane kubelet[3817]: E1101 16:09:09.368794    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"CreatePodSandbox\" for \"coredns-6f6c7df987-8zjhc_kube-system(7f7880eb-8331-4845-8c04-a70d57820961)\" with CreatePodSandboxError: \"Failed to create sandbox for pod \\\"coredns-6f6c7df987-8zjhc_kube-system(7f7880eb-8331-4845-8c04-a70d57820961)\\\": rpc error: code = Unknown desc = failed to setup network for sandbox \\\"bd7557b4974b8d8e02f03dfa979930090c0d97022ccdbb0961a5074ce31411af\\\": plugin type=\\\"flannel\\\" failed (add): loadFlannelSubnetEnv failed: open /run/flannel/subnet.env: no such file or directory\"" pod="kube-system/coredns-6f6c7df987-8zjhc" podUID="7f7880eb-8331-4845-8c04-a70d57820961"
Nov 01 16:09:20 controlplane kubelet[3817]: E1101 16:09:20.310259    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-7d764666f9-k6bmr" containerName="coredns"
Nov 01 16:09:22 controlplane kubelet[3817]: E1101 16:09:22.402904    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-7d764666f9-k6bmr" containerName="coredns"
Nov 01 16:09:23 controlplane kubelet[3817]: E1101 16:09:23.310090    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-wkx8w" containerName="coredns"
Nov 01 16:09:23 controlplane kubelet[3817]: E1101 16:09:23.404425    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-7d764666f9-k6bmr" containerName="coredns"
Nov 01 16:09:24 controlplane kubelet[3817]: E1101 16:09:24.310346    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-8zjhc" containerName="coredns"
Nov 01 16:09:24 controlplane kubelet[3817]: E1101 16:09:24.407096    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-7d764666f9-k6bmr" containerName="coredns"
Nov 01 16:09:24 controlplane kubelet[3817]: E1101 16:09:24.407245    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"coredns\" with CrashLoopBackOff: \"back-off 10s restarting failed container=coredns pod=coredns-7d764666f9-k6bmr_kube-system(567d277a-863d-4080-8c6d-cc193f7ce9c6)\"" pod="kube-system/coredns-7d764666f9-k6bmr" podUID="567d277a-863d-4080-8c6d-cc193f7ce9c6"
Nov 01 16:09:24 controlplane kubelet[3817]: E1101 16:09:24.407800    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-wkx8w" containerName="coredns"
Nov 01 16:09:25 controlplane kubelet[3817]: E1101 16:09:25.410164    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-7d764666f9-k6bmr" containerName="coredns"
Nov 01 16:09:25 controlplane kubelet[3817]: E1101 16:09:25.410192    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-wkx8w" containerName="coredns"
Nov 01 16:09:25 controlplane kubelet[3817]: E1101 16:09:25.410282    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"coredns\" with CrashLoopBackOff: \"back-off 10s restarting failed container=coredns pod=coredns-7d764666f9-k6bmr_kube-system(567d277a-863d-4080-8c6d-cc193f7ce9c6)\"" pod="kube-system/coredns-7d764666f9-k6bmr" podUID="567d277a-863d-4080-8c6d-cc193f7ce9c6"
Nov 01 16:09:25 controlplane kubelet[3817]: E1101 16:09:25.410323    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-8zjhc" containerName="coredns"
Nov 01 16:09:26 controlplane kubelet[3817]: E1101 16:09:26.411951    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-wkx8w" containerName="coredns"
Nov 01 16:09:32 controlplane kubelet[3817]: E1101 16:09:32.309713    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-apiserver-controlplane" containerName="kube-apiserver"
Nov 01 16:09:35 controlplane kubelet[3817]: E1101 16:09:35.410727    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-8zjhc" containerName="coredns"
Nov 01 16:09:35 controlplane kubelet[3817]: E1101 16:09:35.423959    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-8zjhc" containerName="coredns"
Nov 01 16:09:36 controlplane kubelet[3817]: E1101 16:09:36.309978    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:09:48 controlplane kubelet[3817]: E1101 16:09:48.309903    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-controller-manager-controlplane" containerName="kube-controller-manager"
Nov 01 16:09:51 controlplane kubelet[3817]: E1101 16:09:51.310510    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/etcd-controlplane" containerName="etcd"
Nov 01 16:10:42 controlplane kubelet[3817]: E1101 16:10:42.309978    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-wkx8w" containerName="coredns"
Nov 01 16:10:54 controlplane kubelet[3817]: E1101 16:10:54.310083    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-apiserver-controlplane" containerName="kube-apiserver"
Nov 01 16:10:57 controlplane kubelet[3817]: E1101 16:10:57.309566    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:10:59 controlplane kubelet[3817]: E1101 16:10:59.310134    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-8zjhc" containerName="coredns"
Nov 01 16:11:01 controlplane kubelet[3817]: E1101 16:11:01.309813    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-controller-manager-controlplane" containerName="kube-controller-manager"
Nov 01 16:11:07 controlplane kubelet[3817]: E1101 16:11:07.310242    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/etcd-controlplane" containerName="etcd"
Nov 01 16:12:06 controlplane kubelet[3817]: E1101 16:12:06.310181    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-wkx8w" containerName="coredns"
Nov 01 16:12:12 controlplane kubelet[3817]: E1101 16:12:12.309978    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-controller-manager-controlplane" containerName="kube-controller-manager"
Nov 01 16:12:20 controlplane kubelet[3817]: E1101 16:12:20.309839    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-apiserver-controlplane" containerName="kube-apiserver"
Nov 01 16:12:24 controlplane kubelet[3817]: E1101 16:12:24.310417    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:12:29 controlplane kubelet[3817]: E1101 16:12:29.310295    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-8zjhc" containerName="coredns"
Nov 01 16:12:30 controlplane kubelet[3817]: E1101 16:12:30.310137    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/etcd-controlplane" containerName="etcd"
Nov 01 16:13:24 controlplane kubelet[3817]: E1101 16:13:24.344145    3817 info.go:104] Failed to get disk map: could not parse device numbers from  for device md0
Nov 01 16:13:25 controlplane kubelet[3817]: E1101 16:13:25.310136    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-wkx8w" containerName="coredns"
Nov 01 16:13:28 controlplane kubelet[3817]: E1101 16:13:28.310467    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:13:35 controlplane kubelet[3817]: E1101 16:13:35.310516    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-controller-manager-controlplane" containerName="kube-controller-manager"
Nov 01 16:13:38 controlplane kubelet[3817]: E1101 16:13:38.310536    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/etcd-controlplane" containerName="etcd"
Nov 01 16:13:41 controlplane kubelet[3817]: E1101 16:13:41.310113    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-8zjhc" containerName="coredns"
Nov 01 16:13:44 controlplane kubelet[3817]: E1101 16:13:44.310009    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-apiserver-controlplane" containerName="kube-apiserver"
Nov 01 16:14:27 controlplane kubelet[3817]: E1101 16:14:27.310500    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-wkx8w" containerName="coredns"
Nov 01 16:14:34 controlplane kubelet[3817]: E1101 16:14:34.310680    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:14:37 controlplane kubelet[3817]: E1101 16:14:37.310599    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-controller-manager-controlplane" containerName="kube-controller-manager"
Nov 01 16:14:47 controlplane kubelet[3817]: E1101 16:14:47.310312    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-8zjhc" containerName="coredns"
Nov 01 16:15:03 controlplane kubelet[3817]: E1101 16:15:03.310340    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/etcd-controlplane" containerName="etcd"
Nov 01 16:15:12 controlplane kubelet[3817]: E1101 16:15:12.310546    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-apiserver-controlplane" containerName="kube-apiserver"
Nov 01 16:15:28 controlplane kubelet[3817]: E1101 16:15:28.310304    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-wkx8w" containerName="coredns"
Nov 01 16:15:45 controlplane kubelet[3817]: E1101 16:15:45.854168    3817 file.go:108] "Unable to process watch event" err="can't process config file \"/etc/kubernetes/manifests/sed8CXqJP\": /etc/kubernetes/manifests/sed8CXqJP: couldn't parse as pod(Object 'Kind' is missing in 'null'), please check config file"
Nov 01 16:15:46 controlplane kubelet[3817]: E1101 16:15:46.899857    3817 log.go:32] "ContainerStatus from runtime service failed" err="rpc error: code = NotFound desc = an error occurred when try to find container \"ece63bf1edf2c64251559a969d374630173c978f47071b466130ddc81d4962f1\": not found" containerID="ece63bf1edf2c64251559a969d374630173c978f47071b466130ddc81d4962f1"
Nov 01 16:15:58 controlplane kubelet[3817]: E1101 16:15:58.310359    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-controller-manager-controlplane" containerName="kube-controller-manager"
Nov 01 16:16:00 controlplane kubelet[3817]: E1101 16:16:00.309855    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-8zjhc" containerName="coredns"
Nov 01 16:16:01 controlplane kubelet[3817]: E1101 16:16:01.817163    3817 log.go:32] "StartContainer from runtime service failed" err="rpc error: code = Unknown desc = failed to create containerd task: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: \"kube-schedulerrrr\": executable file not found in $PATH: unknown" containerID="33efd3a134c4aae38be4116e33b58f8a630cde296bf203151ec248a67a9841af"
Nov 01 16:16:01 controlplane kubelet[3817]: E1101 16:16:01.817239    3817 kuberuntime_manager.go:1664] "Unhandled Error" err="container kube-scheduler start failed in pod kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d): RunContainerError: failed to create containerd task: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: \"kube-schedulerrrr\": executable file not found in $PATH: unknown" logger="UnhandledError"
Nov 01 16:16:01 controlplane kubelet[3817]: E1101 16:16:01.817267    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with RunContainerError: \"failed to create containerd task: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: \\\"kube-schedulerrrr\\\": executable file not found in $PATH: unknown\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:16:01 controlplane kubelet[3817]: E1101 16:16:01.917327    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:16:02 controlplane kubelet[3817]: E1101 16:16:02.176452    3817 log.go:32] "StartContainer from runtime service failed" err="rpc error: code = Unknown desc = failed to create containerd task: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: \"kube-schedulerrrr\": executable file not found in $PATH: unknown" containerID="04de7341f608619cd28006a52076f180cee6f0f82f5397aeb0527c12cc45380e"
Nov 01 16:16:02 controlplane kubelet[3817]: E1101 16:16:02.176538    3817 kuberuntime_manager.go:1664] "Unhandled Error" err="container kube-scheduler start failed in pod kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d): RunContainerError: failed to create containerd task: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: \"kube-schedulerrrr\": executable file not found in $PATH: unknown" logger="UnhandledError"
Nov 01 16:16:02 controlplane kubelet[3817]: E1101 16:16:02.176558    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with RunContainerError: \"failed to create containerd task: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: \\\"kube-schedulerrrr\\\": executable file not found in $PATH: unknown\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:16:02 controlplane kubelet[3817]: E1101 16:16:02.919489    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:16:02 controlplane kubelet[3817]: E1101 16:16:02.919601    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with CrashLoopBackOff: \"back-off 10s restarting failed container=kube-scheduler pod=kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d)\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:16:03 controlplane kubelet[3817]: E1101 16:16:03.921216    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:16:03 controlplane kubelet[3817]: E1101 16:16:03.921344    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with CrashLoopBackOff: \"back-off 10s restarting failed container=kube-scheduler pod=kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d)\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:16:04 controlplane kubelet[3817]: E1101 16:16:04.922553    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:16:04 controlplane kubelet[3817]: E1101 16:16:04.922652    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with CrashLoopBackOff: \"back-off 10s restarting failed container=kube-scheduler pod=kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d)\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:16:11 controlplane kubelet[3817]: E1101 16:16:11.320601    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:16:11 controlplane kubelet[3817]: E1101 16:16:11.320736    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with CrashLoopBackOff: \"back-off 10s restarting failed container=kube-scheduler pod=kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d)\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:16:13 controlplane kubelet[3817]: E1101 16:16:13.310161    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:16:13 controlplane kubelet[3817]: E1101 16:16:13.619172    3817 log.go:32] "StartContainer from runtime service failed" err="rpc error: code = Unknown desc = failed to create containerd task: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: \"kube-schedulerrrr\": executable file not found in $PATH: unknown" containerID="f28e4f8906ec8bbee329d19eceb4c7d5dc66274fb9c510644f05ee804a413fc7"
Nov 01 16:16:13 controlplane kubelet[3817]: E1101 16:16:13.619246    3817 kuberuntime_manager.go:1664] "Unhandled Error" err="container kube-scheduler start failed in pod kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d): RunContainerError: failed to create containerd task: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: \"kube-schedulerrrr\": executable file not found in $PATH: unknown" logger="UnhandledError"
Nov 01 16:16:13 controlplane kubelet[3817]: E1101 16:16:13.619277    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with RunContainerError: \"failed to create containerd task: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: \\\"kube-schedulerrrr\\\": executable file not found in $PATH: unknown\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:16:13 controlplane kubelet[3817]: E1101 16:16:13.935042    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:16:13 controlplane kubelet[3817]: E1101 16:16:13.935146    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with CrashLoopBackOff: \"back-off 20s restarting failed container=kube-scheduler pod=kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d)\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:16:14 controlplane kubelet[3817]: E1101 16:16:14.936417    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:16:14 controlplane kubelet[3817]: E1101 16:16:14.936544    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with CrashLoopBackOff: \"back-off 20s restarting failed container=kube-scheduler pod=kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d)\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:16:20 controlplane kubelet[3817]: E1101 16:16:20.309833    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-apiserver-controlplane" containerName="kube-apiserver"
Nov 01 16:16:21 controlplane kubelet[3817]: E1101 16:16:21.320415    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:16:21 controlplane kubelet[3817]: E1101 16:16:21.320521    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with CrashLoopBackOff: \"back-off 20s restarting failed container=kube-scheduler pod=kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d)\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:16:29 controlplane kubelet[3817]: E1101 16:16:29.309934    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/etcd-controlplane" containerName="etcd"
Nov 01 16:16:39 controlplane kubelet[3817]: E1101 16:16:39.310282    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:16:39 controlplane kubelet[3817]: E1101 16:16:39.629619    3817 log.go:32] "StartContainer from runtime service failed" err="rpc error: code = Unknown desc = failed to create containerd task: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: \"kube-schedulerrrr\": executable file not found in $PATH: unknown" containerID="b4277297d7ec018d10c8c125c5b9f11a9e0757d10486274f991d9152974dd09a"
Nov 01 16:16:39 controlplane kubelet[3817]: E1101 16:16:39.629691    3817 kuberuntime_manager.go:1664] "Unhandled Error" err="container kube-scheduler start failed in pod kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d): RunContainerError: failed to create containerd task: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: \"kube-schedulerrrr\": executable file not found in $PATH: unknown" logger="UnhandledError"
Nov 01 16:16:39 controlplane kubelet[3817]: E1101 16:16:39.629714    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with RunContainerError: \"failed to create containerd task: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: \\\"kube-schedulerrrr\\\": executable file not found in $PATH: unknown\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:16:39 controlplane kubelet[3817]: E1101 16:16:39.967971    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:16:39 controlplane kubelet[3817]: E1101 16:16:39.968065    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with CrashLoopBackOff: \"back-off 40s restarting failed container=kube-scheduler pod=kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d)\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:16:40 controlplane kubelet[3817]: E1101 16:16:40.969997    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:16:40 controlplane kubelet[3817]: E1101 16:16:40.970092    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with CrashLoopBackOff: \"back-off 40s restarting failed container=kube-scheduler pod=kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d)\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:16:41 controlplane kubelet[3817]: E1101 16:16:41.971714    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:16:41 controlplane kubelet[3817]: E1101 16:16:41.971885    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with CrashLoopBackOff: \"back-off 40s restarting failed container=kube-scheduler pod=kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d)\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:16:54 controlplane kubelet[3817]: E1101 16:16:54.310071    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-wkx8w" containerName="coredns"
Nov 01 16:17:07 controlplane kubelet[3817]: E1101 16:17:07.309773    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-controller-manager-controlplane" containerName="kube-controller-manager"
Nov 01 16:17:26 controlplane kubelet[3817]: E1101 16:17:26.310226    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-8zjhc" containerName="coredns"
Nov 01 16:17:26 controlplane kubelet[3817]: E1101 16:17:26.310294    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:17:26 controlplane kubelet[3817]: E1101 16:17:26.656075    3817 log.go:32] "StartContainer from runtime service failed" err="rpc error: code = Unknown desc = failed to create containerd task: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: \"kube-schedulerrrr\": executable file not found in $PATH: unknown" containerID="0d1fe5b8db02ca6f39fa0a35af2bd8cd43a58d2779eebc54332ba4a9e438a62a"
Nov 01 16:17:26 controlplane kubelet[3817]: E1101 16:17:26.656145    3817 kuberuntime_manager.go:1664] "Unhandled Error" err="container kube-scheduler start failed in pod kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d): RunContainerError: failed to create containerd task: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: \"kube-schedulerrrr\": executable file not found in $PATH: unknown" logger="UnhandledError"
Nov 01 16:17:26 controlplane kubelet[3817]: E1101 16:17:26.656174    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with RunContainerError: \"failed to create containerd task: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: \\\"kube-schedulerrrr\\\": executable file not found in $PATH: unknown\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:17:27 controlplane kubelet[3817]: E1101 16:17:27.026980    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:17:27 controlplane kubelet[3817]: E1101 16:17:27.027092    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with CrashLoopBackOff: \"back-off 1m20s restarting failed container=kube-scheduler pod=kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d)\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:17:28 controlplane kubelet[3817]: E1101 16:17:28.028540    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:17:28 controlplane kubelet[3817]: E1101 16:17:28.028645    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with CrashLoopBackOff: \"back-off 1m20s restarting failed container=kube-scheduler pod=kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d)\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:17:31 controlplane kubelet[3817]: E1101 16:17:31.320262    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:17:31 controlplane kubelet[3817]: E1101 16:17:31.320390    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with CrashLoopBackOff: \"back-off 1m20s restarting failed container=kube-scheduler pod=kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d)\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:17:35 controlplane kubelet[3817]: E1101 16:17:35.309717    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-apiserver-controlplane" containerName="kube-apiserver"
Nov 01 16:17:55 controlplane kubelet[3817]: E1101 16:17:55.310268    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/etcd-controlplane" containerName="etcd"
Nov 01 16:18:08 controlplane kubelet[3817]: E1101 16:18:08.310139    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-wkx8w" containerName="coredns"
Nov 01 16:18:24 controlplane kubelet[3817]: E1101 16:18:24.342727    3817 info.go:104] Failed to get disk map: could not parse device numbers from  for device md0
Nov 01 16:18:26 controlplane kubelet[3817]: E1101 16:18:26.310589    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-controller-manager-controlplane" containerName="kube-controller-manager"
Nov 01 16:18:33 controlplane kubelet[3817]: E1101 16:18:33.309963    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:18:33 controlplane kubelet[3817]: E1101 16:18:33.310098    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with CrashLoopBackOff: \"back-off 1m20s restarting failed container=kube-scheduler pod=kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d)\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:18:50 controlplane kubelet[3817]: E1101 16:18:50.309823    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-apiserver-controlplane" containerName="kube-apiserver"
Nov 01 16:18:50 controlplane kubelet[3817]: E1101 16:18:50.309967    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:18:50 controlplane kubelet[3817]: E1101 16:18:50.660703    3817 log.go:32] "StartContainer from runtime service failed" err="rpc error: code = Unknown desc = failed to create containerd task: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: \"kube-schedulerrrr\": executable file not found in $PATH: unknown" containerID="d346c5468a94722179bab53eabe41d34d334414a79d4edbe973ad032357896b6"
Nov 01 16:18:50 controlplane kubelet[3817]: E1101 16:18:50.660765    3817 kuberuntime_manager.go:1664] "Unhandled Error" err="container kube-scheduler start failed in pod kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d): RunContainerError: failed to create containerd task: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: \"kube-schedulerrrr\": executable file not found in $PATH: unknown" logger="UnhandledError"
Nov 01 16:18:50 controlplane kubelet[3817]: E1101 16:18:50.660832    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with RunContainerError: \"failed to create containerd task: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: \\\"kube-schedulerrrr\\\": executable file not found in $PATH: unknown\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:18:51 controlplane kubelet[3817]: E1101 16:18:51.137133    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:18:51 controlplane kubelet[3817]: E1101 16:18:51.137222    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with CrashLoopBackOff: \"back-off 2m40s restarting failed container=kube-scheduler pod=kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d)\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:18:52 controlplane kubelet[3817]: E1101 16:18:52.138497    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:18:52 controlplane kubelet[3817]: E1101 16:18:52.138609    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with CrashLoopBackOff: \"back-off 2m40s restarting failed container=kube-scheduler pod=kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d)\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:18:53 controlplane kubelet[3817]: E1101 16:18:53.310068    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-8zjhc" containerName="coredns"
Nov 01 16:18:57 controlplane kubelet[3817]: E1101 16:18:57.310431    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/etcd-controlplane" containerName="etcd"
Nov 01 16:19:17 controlplane kubelet[3817]: E1101 16:19:17.310309    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-wkx8w" containerName="coredns"
Nov 01 16:19:44 controlplane kubelet[3817]: E1101 16:19:44.309927    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-controller-manager-controlplane" containerName="kube-controller-manager"
Nov 01 16:19:54 controlplane kubelet[3817]: E1101 16:19:54.311680    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-apiserver-controlplane" containerName="kube-apiserver"
Nov 01 16:20:05 controlplane kubelet[3817]: E1101 16:20:05.310397    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/etcd-controlplane" containerName="etcd"
Nov 01 16:20:12 controlplane kubelet[3817]: E1101 16:20:12.310073    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-8zjhc" containerName="coredns"
Nov 01 16:20:20 controlplane kubelet[3817]: E1101 16:20:20.309981    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:20:20 controlplane kubelet[3817]: E1101 16:20:20.310124    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with CrashLoopBackOff: \"back-off 2m40s restarting failed container=kube-scheduler pod=kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d)\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:20:42 controlplane kubelet[3817]: E1101 16:20:42.310528    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-wkx8w" containerName="coredns"
Nov 01 16:21:07 controlplane kubelet[3817]: E1101 16:21:07.310209    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-apiserver-controlplane" containerName="kube-apiserver"
Nov 01 16:21:08 controlplane kubelet[3817]: E1101 16:21:08.310457    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-controller-manager-controlplane" containerName="kube-controller-manager"
Nov 01 16:21:28 controlplane kubelet[3817]: E1101 16:21:28.309957    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/etcd-controlplane" containerName="etcd"
Nov 01 16:21:41 controlplane kubelet[3817]: E1101 16:21:41.309617    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-8zjhc" containerName="coredns"
Nov 01 16:21:44 controlplane kubelet[3817]: E1101 16:21:44.310105    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:21:44 controlplane kubelet[3817]: E1101 16:21:44.611426    3817 log.go:32] "StartContainer from runtime service failed" err="rpc error: code = Unknown desc = failed to create containerd task: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: \"kube-schedulerrrr\": executable file not found in $PATH: unknown" containerID="03f778c0d714a369853047d5be570d589784c210d505b049306a037e6ada0660"
Nov 01 16:21:44 controlplane kubelet[3817]: E1101 16:21:44.611492    3817 kuberuntime_manager.go:1664] "Unhandled Error" err="container kube-scheduler start failed in pod kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d): RunContainerError: failed to create containerd task: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: \"kube-schedulerrrr\": executable file not found in $PATH: unknown" logger="UnhandledError"
Nov 01 16:21:44 controlplane kubelet[3817]: E1101 16:21:44.611514    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with RunContainerError: \"failed to create containerd task: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: \\\"kube-schedulerrrr\\\": executable file not found in $PATH: unknown\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:21:45 controlplane kubelet[3817]: E1101 16:21:45.358939    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:21:45 controlplane kubelet[3817]: E1101 16:21:45.359049    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with CrashLoopBackOff: \"back-off 5m0s restarting failed container=kube-scheduler pod=kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d)\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:21:46 controlplane kubelet[3817]: E1101 16:21:46.360293    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:21:46 controlplane kubelet[3817]: E1101 16:21:46.360448    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with CrashLoopBackOff: \"back-off 5m0s restarting failed container=kube-scheduler pod=kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d)\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:21:51 controlplane kubelet[3817]: E1101 16:21:51.319587    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:21:51 controlplane kubelet[3817]: E1101 16:21:51.319754    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with CrashLoopBackOff: \"back-off 5m0s restarting failed container=kube-scheduler pod=kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d)\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:21:51 controlplane kubelet[3817]: E1101 16:21:51.365689    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:21:51 controlplane kubelet[3817]: E1101 16:21:51.365875    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with CrashLoopBackOff: \"back-off 5m0s restarting failed container=kube-scheduler pod=kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d)\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:22:09 controlplane kubelet[3817]: E1101 16:22:09.310547    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-wkx8w" containerName="coredns"
Nov 01 16:22:14 controlplane kubelet[3817]: E1101 16:22:14.309961    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-apiserver-controlplane" containerName="kube-apiserver"
Nov 01 16:22:26 controlplane kubelet[3817]: E1101 16:22:26.309948    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-controller-manager-controlplane" containerName="kube-controller-manager"
Nov 01 16:22:31 controlplane kubelet[3817]: E1101 16:22:31.310086    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/etcd-controlplane" containerName="etcd"
Nov 01 16:22:51 controlplane kubelet[3817]: E1101 16:22:51.310433    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-8zjhc" containerName="coredns"
Nov 01 16:23:06 controlplane kubelet[3817]: E1101 16:23:06.310073    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:23:06 controlplane kubelet[3817]: E1101 16:23:06.310241    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with CrashLoopBackOff: \"back-off 5m0s restarting failed container=kube-scheduler pod=kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d)\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:23:20 controlplane kubelet[3817]: E1101 16:23:20.310335    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-apiserver-controlplane" containerName="kube-apiserver"
Nov 01 16:23:24 controlplane kubelet[3817]: E1101 16:23:24.343008    3817 info.go:104] Failed to get disk map: could not parse device numbers from  for device md0
Nov 01 16:23:26 controlplane kubelet[3817]: E1101 16:23:26.310178    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-wkx8w" containerName="coredns"
Nov 01 16:23:27 controlplane kubelet[3817]: E1101 16:23:27.310021    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-controller-manager-controlplane" containerName="kube-controller-manager"
Nov 01 16:23:33 controlplane kubelet[3817]: E1101 16:23:33.309641    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/etcd-controlplane" containerName="etcd"
Nov 01 16:23:52 controlplane kubelet[3817]: E1101 16:23:52.310446    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-8zjhc" containerName="coredns"
Nov 01 16:24:13 controlplane kubelet[3817]: E1101 16:24:13.309952    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:24:13 controlplane kubelet[3817]: E1101 16:24:13.310091    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with CrashLoopBackOff: \"back-off 5m0s restarting failed container=kube-scheduler pod=kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d)\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:24:38 controlplane kubelet[3817]: E1101 16:24:38.310031    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-apiserver-controlplane" containerName="kube-apiserver"
Nov 01 16:24:45 controlplane kubelet[3817]: E1101 16:24:45.310494    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/etcd-controlplane" containerName="etcd"
Nov 01 16:24:50 controlplane kubelet[3817]: E1101 16:24:50.310373    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-wkx8w" containerName="coredns"
Nov 01 16:24:50 controlplane kubelet[3817]: E1101 16:24:50.310461    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-controller-manager-controlplane" containerName="kube-controller-manager"
Nov 01 16:25:19 controlplane kubelet[3817]: E1101 16:25:19.310254    3817 prober_manager.go:209] "Readiness probe already exists for container" pod="kube-system/coredns-6f6c7df987-8zjhc" containerName="coredns"

controlplane ~ ➜  vi err-01.txt

controlplane ~ ➜  cat err-01.txt 
Nov 01 16:21:44 controlplane kubelet[3817]: E1101 16:21:44.611514    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with RunContainerError: \"failed to create containerd task: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: \\\"kube-schedulerrrr\\\": executable file not found in $PATH: unknown\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"
Nov 01 16:21:45 controlplane kubelet[3817]: E1101 16:21:45.358939    3817 prober_manager.go:197] "Startup probe already exists for container" pod="kube-system/kube-scheduler-controlplane" containerName="kube-scheduler"
Nov 01 16:21:45 controlplane kubelet[3817]: E1101 16:21:45.359049    3817 pod_workers.go:1324] "Error syncing pod, skipping" err="failed to \"StartContainer\" for \"kube-scheduler\" with CrashLoopBackOff: \"back-off 5m0s restarting failed container=kube-scheduler pod=kube-scheduler-controlplane_kube-system(186c52b5584d78a99894b5f06124bf5d)\"" pod="kube-system/kube-scheduler-controlplane" podUID="186c52b5584d78a99894b5f06124bf5d"

controlplane ~ ➜  grep -rn rrrr /etc/kubernetes/manifests/*
/etc/kubernetes/manifests/kube-scheduler.yaml:12:    - kube-schedulerrrr

controlplane ~ ➜  vi /etc/kubernetes/manifests/kube-scheduler.yaml

controlplane ~ ➜  

controlplane ~ ➜  

controlplane ~ ➜  k get deploy
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
app    1/1     1            1           11m

controlplane ~ ➜  




════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
══════════════════════════════════════════════ Q.2 ═════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════

Scale the deployment app to 2 pods.


controlplane ~ ✖ #================================ Q.2 =================================

controlplane ~ ✖ k get deploy app
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
app    1/1     1            1           13m

controlplane ~ ➜  k scale deployment app --replicas=2
deployment.apps/app scaled

controlplane ~ ➜  k get rs
NAME           DESIRED   CURRENT   READY   AGE
app-5497957c   1         1         1       14m

controlplane ~ ➜  k get pods
NAME                 READY   STATUS    RESTARTS   AGE
app-5497957c-sb55d   1/1     Running   0          14m

controlplane ~ ➜  k get pods -n kube-system 
NAME                                   READY   STATUS             RESTARTS      AGE
coredns-6f6c7df987-8zjhc               1/1     Running            0             22m
coredns-6f6c7df987-wkx8w               1/1     Running            0             22m
etcd-controlplane                      1/1     Running            0             22m
kube-apiserver-controlplane            1/1     Running            0             22m
kube-controller-manager-controlplane   0/1     CrashLoopBackOff   4 (37s ago)   113s
kube-proxy-q7kk7                       1/1     Running            0             22m
kube-scheduler-controlplane            1/1     Running            0             3m2s

controlplane ~ ➜  k logs kube-controller-manager-controlplane -n kube-system 
I1101 16:29:49.688575       1 serving.go:386] Generated self-signed cert in-memory
E1101 16:29:49.688641       1 run.go:72] "command failed" err="stat /etc/kubernetes/controller-manager-XXXX.conf: no such file or directory"

controlplane ~ ➜  grep XXXX -rn /etc/kubernetes/manifests/
/etc/kubernetes/manifests/kube-controller-manager.yaml:23:    - --kubeconfig=/etc/kubernetes/controller-manager-XXXX.conf

controlplane ~ ➜  vi /etc/kubernetes/manifests/kube-controller-manager.yaml 

controlplane ~ ➜  

controlplane ~ ➜  k get rs
NAME           DESIRED   CURRENT   READY   AGE
app-5497957c   1         1         1       15m

controlplane ~ ➜  k rollout restart deployment app 
deployment.apps/app restarted

controlplane ~ ➜  k get rs
NAME            DESIRED   CURRENT   READY   AGE
app-5497957c    1         1         1       16m
app-989658497   2         2         1       2s

controlplane ~ ➜  




════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
══════════════════════════════════════════════ Q.3 ═════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
Even though the deployment was scaled to 2, the number of PODs does not seem to increase. Investigate and fix the issue.


Inspect the component responsible for managing deployments and replicasets.

controlplane ~ ➜  k rollout restart deployment app 
deployment.apps/app restarted

controlplane ~ ➜  k get rs
NAME            DESIRED   CURRENT   READY   AGE
app-5497957c    1         1         1       16m
app-989658497   2         2         1       2s

controlplane ~ ➜  



════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
══════════════════════════════════════════════ Q.4 ═════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════

Something is wrong with scaling again. We just tried scaling the deployment to 3 replicas. But it's not happening.


Investigate and fix the issue.

controlplane ~ ➜  #================================ Q.4 =========================

controlplane ~ ➜  k get rs
NAME            DESIRED   CURRENT   READY   AGE
app-5497957c    0         0         0       18m
app-989658497   2         2         2       2m41s

controlplane ~ ➜  k get deploy
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
app    2/3     2            2           18m

controlplane ~ ➜  k get pods -n kube-system 
NAME                                   READY   STATUS             RESTARTS      AGE
coredns-6f6c7df987-8zjhc               1/1     Running            0             26m
coredns-6f6c7df987-wkx8w               1/1     Running            0             26m
etcd-controlplane                      1/1     Running            0             26m
kube-apiserver-controlplane            1/1     Running            0             26m
kube-controller-manager-controlplane   0/1     CrashLoopBackOff   3 (51s ago)   90s
kube-proxy-q7kk7                       1/1     Running            0             26m
kube-scheduler-controlplane            1/1     Running            0             7m40s

controlplane ~ ➜  k logs kube-controller-manager-controlplane -n kube-system 
I1101 16:35:08.664115       1 serving.go:386] Generated self-signed cert in-memory
E1101 16:35:08.666253       1 run.go:72] "command failed" err="unable to load client CA provider: open /etc/kubernetes/pki/ca.crt: no such file or directory"

controlplane ~ ➜  ls -l /etc/kubernetes/pki/ca.crt
-rw-r--r-- 1 root root 1107 Nov  1 16:08 /etc/kubernetes/pki/ca.crt

controlplane ~ ➜  

controlplane ~ ➜  ls -l /etc/kubernetes/manifests/
total 16
-rw------- 1 root root 2616 Nov  1 16:08 etcd.yaml
-rw------- 1 root root 3965 Nov  1 16:08 kube-apiserver.yaml
-rw------- 1 root root 3475 Nov  1 16:33 kube-controller-manager.yaml
-rw------- 1 root root 1726 Nov  1 16:27 kube-scheduler.yaml

controlplane ~ ➜  vi kube-controller-manager.yaml

controlplane ~ ➜  vi /etc/kubernetes/manifests/kube-controller-manager.yaml

controlplane ~ ➜  cat WRONG /etc/kubernetes/manifests/kube-controller-manager.yaml
cat: WRONG: No such file or directory
apiVersion: v1
kind: Pod
metadata:
  labels:
    component: kube-controller-manager
    tier: control-plane
  name: kube-controller-manager
  namespace: kube-system
spec:
  containers:
  - command:
    - kube-controller-manager
    - --allocate-node-cidrs=true
    - --authentication-kubeconfig=/etc/kubernetes/controller-manager.conf
    - --authorization-kubeconfig=/etc/kubernetes/controller-manager.conf
    - --bind-address=127.0.0.1
    - --client-ca-file=/etc/kubernetes/pki/ca.crt
    - --cluster-cidr=172.17.0.0/16
    - --cluster-name=kubernetes
    - --cluster-signing-cert-file=/etc/kubernetes/pki/ca.crt
    - --cluster-signing-key-file=/etc/kubernetes/pki/ca.key
    - --controllers=*,bootstrapsigner,tokencleaner
    - --kubeconfig=/etc/kubernetes/controller-manager.conf
    - --leader-elect=true
    - --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt
    - --root-ca-file=/etc/kubernetes/pki/ca.crt
    - --service-account-private-key-file=/etc/kubernetes/pki/sa.key
    - --service-cluster-ip-range=172.20.0.0/16
    - --use-service-account-credentials=true
    image: registry.k8s.io/kube-controller-manager:v1.35.0
    imagePullPolicy: IfNotPresent
    livenessProbe:
      failureThreshold: 8
      httpGet:
        host: 127.0.0.1
        path: /healthz
        port: probe-port
        scheme: HTTPS
      initialDelaySeconds: 10
      periodSeconds: 10
      timeoutSeconds: 15
    name: kube-controller-manager
    ports:
    - containerPort: 10257
      name: probe-port
      protocol: TCP
    resources:
      requests:
        cpu: 200m
    startupProbe:
      failureThreshold: 24
      httpGet:
        host: 127.0.0.1
        path: /healthz
        port: probe-port
        scheme: HTTPS
      initialDelaySeconds: 10
      periodSeconds: 10
      timeoutSeconds: 15
    volumeMounts:
    - mountPath: /etc/ssl/certs
      name: ca-certs
      readOnly: true
    - mountPath: /etc/ca-certificates
      name: etc-ca-certificates
      readOnly: true
    - mountPath: /usr/libexec/kubernetes/kubelet-plugins/volume/exec
      name: flexvolume-dir
    - mountPath: /etc/kubernetes/pki
      name: k8s-certs
      readOnly: true
    - mountPath: /etc/kubernetes/controller-manager.conf
      name: kubeconfig
      readOnly: true
    - mountPath: /usr/local/share/ca-certificates
      name: usr-local-share-ca-certificates
      readOnly: true
    - mountPath: /usr/share/ca-certificates
      name: usr-share-ca-certificates
      readOnly: true
  hostNetwork: true
  priority: 2000001000
  priorityClassName: system-node-critical
  securityContext:
    seccompProfile:
      type: RuntimeDefault
  volumes:
  - hostPath:
      path: /etc/ssl/certs
      type: DirectoryOrCreate
    name: ca-certs
  - hostPath:
      path: /etc/ca-certificates
      type: DirectoryOrCreate
    name: etc-ca-certificates
  - hostPath:
      path: /usr/libexec/kubernetes/kubelet-plugins/volume/exec
      type: DirectoryOrCreate
    name: flexvolume-dir
  - hostPath:
      path: /etc/kubernetes/WRONG-PKI-DIRECTORY
      type: DirectoryOrCreate
    name: k8s-certs
  - hostPath:
      path: /etc/kubernetes/controller-manager.conf
      type: FileOrCreate
    name: kubeconfig
  - hostPath:
      path: /usr/local/share/ca-certificates
      type: DirectoryOrCreate
    name: usr-local-share-ca-certificates
  - hostPath:
      path: /usr/share/ca-certificates
      type: DirectoryOrCreate
    name: usr-share-ca-certificates
status: {}

controlplane ~ ✖ grep WRONG /etc/kubernetes/manifests/kube-controller-manager.yaml
      path: /etc/kubernetes/WRONG-PKI-DIRECTORY

controlplane ~ ➜  

controlplane ~ ➜  vi /etc/kubernetes/manifests/kube-controller-manager.yaml

controlplane ~ ➜  

controlplane ~ ➜  k rollout restart deployment app
deployment.apps/app restarted

controlplane ~ ➜  k get rs
NAME            DESIRED   CURRENT   READY   AGE
app-5497957c    0         0         0       22m
app-989658497   2         2         2       6m54s

controlplane ~ ➜  k get rs
NAME             DESIRED   CURRENT   READY   AGE
app-5497957c     0         0         0       23m
app-7c5d444dfc   3         3         3       8s
app-989658497    0         0         0       7m9s

controlplane ~ ➜  


════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
═════════ Practice Test - Application Failure ═════════════════ 01.11.2026 ═════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════

════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
══════════════════════════════════════════════ Q.1 ═════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════

Troubleshooting Test 1: A simple 2 tier application is deployed in the alpha namespace. It must display a green web page on success. 
Click on the App tab at the top of your terminal to view your application. It is currently failed. Troubleshoot and fix the issue.


Stick to the given architecture. Use the same names and port numbers as given in the below architecture diagram. Feel free to edit, delete or recreate objects as necessary.







       Welcome to the KodeKloud Hands-On lab                                                                                                      
    __ ______  ____  ________ __ __    ____  __  ______ 
   / //_/ __ \/ __ \/ ____/ //_// /   / __ \/ / / / __ \
  / ,< / / / / / / / __/ / ,<  / /   / / / / / / / / / /
 / /| / /_/ / /_/ / /___/ /| |/ /___/ /_/ / /_/ / /_/ / 
/_/ |_\____/_____/_____/_/ |_/_____/\____/\____/_____/  
                                                        
               All rights reserved                                                                                                                  

controlplane ~ ➜  k get all
NAME                                READY   STATUS    RESTARTS   AGE
pod/mysql                           1/1     Running   0          9m45s
pod/webapp-mysql-6ddf84655d-2plq9   1/1     Running   0          9m44s

NAME                  TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/mysql         ClusterIP   10.43.141.35    <none>        3306/TCP         9m45s
service/web-service   NodePort    10.43.152.219   <none>        8080:30081/TCP   9m44s

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/webapp-mysql   1/1     1            1           9m44s

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/webapp-mysql-6ddf84655d   1         1         1       9m44s

controlplane ~ ➜  

controlplane ~ ➜  k expose pod/mysql --name=mysql-service --namespace=alpha --port=3306 --target-port=3306 -o yaml --dry-run
W1101 16:51:32.138746    9780 helpers.go:719] --dry-run is deprecated and can be replaced with --dry-run=client.
apiVersion: v1
kind: Service
metadata:
  labels:
    name: mysql
  name: mysql-service
  namespace: alpha
spec:
  ports:
  - port: 3306
    protocol: TCP
    targetPort: 3306
  selector:
    name: mysql
status:
  loadBalancer: {}

controlplane ~ ➜  k expose pod/mysql --name=mysql-service --namespace=alpha --port=3306 --target-port=3306 -o yaml --dry-run=client
apiVersion: v1
kind: Service
metadata:
  labels:
    name: mysql
  name: mysql-service
  namespace: alpha
spec:
  ports:
  - port: 3306
    protocol: TCP
    targetPort: 3306
  selector:
    name: mysql
status:
  loadBalancer: {}

controlplane ~ ➜  k expose pod/mysql --name=mysql-service --namespace=alpha --port=3306 --target-port=3306
service/mysql-service exposed

controlplane ~ ➜  

controlplane ~ ➜  k get all
NAME                                READY   STATUS    RESTARTS   AGE
pod/mysql                           1/1     Running   0          11m
pod/webapp-mysql-6ddf84655d-2plq9   1/1     Running   0          11m

NAME                    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/mysql           ClusterIP   10.43.141.35    <none>        3306/TCP         11m
service/mysql-service   ClusterIP   10.43.184.158   <none>        3306/TCP         5s
service/web-service     NodePort    10.43.152.219   <none>        8080:30081/TCP   11m

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/webapp-mysql   1/1     1            1           11m

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/webapp-mysql-6ddf84655d   1         1         1       11m

controlplane ~ ➜  k delete svc service/mysql
error: there is no need to specify a resource type as a separate argument when passing arguments in resource/name form (e.g. 'kubectl get resource/<resource_name>' instead of 'kubectl get resource resource/<resource_name>'

controlplane ~ ✖ k get all
NAME                                READY   STATUS    RESTARTS   AGE
pod/mysql                           1/1     Running   0          11m
pod/webapp-mysql-6ddf84655d-2plq9   1/1     Running   0          11m

NAME                    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/mysql           ClusterIP   10.43.141.35    <none>        3306/TCP         11m
service/mysql-service   ClusterIP   10.43.184.158   <none>        3306/TCP         35s
service/web-service     NodePort    10.43.152.219   <none>        8080:30081/TCP   11m

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/webapp-mysql   1/1     1            1           11m

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/webapp-mysql-6ddf84655d   1         1         1       11m

controlplane ~ ➜  k delete svc mysql
service "mysql" deleted from alpha namespace

controlplane ~ ➜  

controlplane ~ ➜  k get all
NAME                                READY   STATUS    RESTARTS   AGE
pod/mysql                           1/1     Running   0          11m
pod/webapp-mysql-6ddf84655d-2plq9   1/1     Running   0          11m

NAME                    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/mysql-service   ClusterIP   10.43.184.158   <none>        3306/TCP         47s
service/web-service     NodePort    10.43.152.219   <none>        8080:30081/TCP   11m

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/webapp-mysql   1/1     1            1           11m

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/webapp-mysql-6ddf84655d   1         1         1       11m

controlplane ~ ➜  k get nodes -o wide
NAME           STATUS   ROLES           AGE   VERSION        INTERNAL-IP     EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION     CONTAINER-RUNTIME
controlplane   Ready    control-plane   25m   v1.35.0+k3s3   10.244.154.47   <none>        Alpine Linux v3.16   6.8.0-90-generic   containerd://1.6.8

controlplane ~ ➜  curl -sS http://10.244.154.47:30081
<!doctype html>
<title>Hello from Flask</title>
<body style="background: #39b54b;"></body>
<div style="color: #e4e4e4;
    text-align:  center;
    height: 90px;
    vertical-align:  middle;">

  
    <!-- <h1> DATABASE CONNECTION SUCCESSFUL !</h1> -->
    <img src="/static/img/success.jpg">
  

  
    <h2> Environment Variables: DB_Host=mysql-service; DB_Database=Not Set; DB_User=root; DB_Password=paswrd;  </h2>
  

  
    <p> From webapp-mysql-6ddf84655d-2plq9!</p>
  

  

</div>
controlplane ~ ➜  



════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
══════════════════════════════════════════════ Q.2 ═════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
Troubleshooting Test 2: The same 2 tier application is deployed in the beta namespace. It must display a green web page on success. Click on the App tab at the top of your terminal to view your application. It is currently failed. Troubleshoot and fix the issue.


Stick to the given architecture. Use the same names and port numbers as given in the below architecture diagram. Feel free to edit, delete or recreate objects as necessary.

controlplane ~ ➜  #=========================== Q.2 ==================================

controlplane ~ ➜  k get all
No resources found in alpha namespace.

controlplane ~ ➜  k config set-context --current --namespace=beta
Context "default" modified.

controlplane ~ ➜  

controlplane ~ ➜  k get all
NAME                                READY   STATUS    RESTARTS   AGE
pod/mysql                           1/1     Running   0          82s
pod/webapp-mysql-6ddf84655d-67rjs   1/1     Running   0          81s

NAME                    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/mysql-service   ClusterIP   10.43.183.102   <none>        3306/TCP         82s
service/web-service     NodePort    10.43.91.46     <none>        8080:30081/TCP   81s

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/webapp-mysql   1/1     1            1           81s

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/webapp-mysql-6ddf84655d   1         1         1       81s

controlplane ~ ➜  

controlplane ~ ➜  curl -sS http://10.244.154.47:30081
<!doctype html>
<title>Hello from Flask</title>
<body style="background: #ff3f3f;"></body>
<div style="color: #e4e4e4;
    text-align:  center;
    height: 90px;
    vertical-align:  middle;">

  
    <img src="/static/img/failed.png">
    <!-- <h1> DATABASE CONNECTION FAILED !!</h1> -->
  

  
    <h2> Environment Variables: DB_Host=mysql-service; DB_Database=Not Set; DB_User=root; DB_Password=paswrd; 2003: Can&#39;t connect to MySQL server on &#39;mysql-service:3306&#39; (111 Connection refused) </h2>
  

  
    <p> From webapp-mysql-6ddf84655d-67rjs!</p>
  

  

</div>
controlplane ~ ➜  vi err-01.txt 

controlplane ~ ➜  

controlplane ~ ➜  k get all
NAME                                READY   STATUS    RESTARTS   AGE
pod/mysql                           1/1     Running   0          2m31s
pod/webapp-mysql-6ddf84655d-67rjs   1/1     Running   0          2m30s

NAME                    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/mysql-service   ClusterIP   10.43.183.102   <none>        3306/TCP         2m31s
service/web-service     NodePort    10.43.91.46     <none>        8080:30081/TCP   2m30s

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/webapp-mysql   1/1     1            1           2m30s

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/webapp-mysql-6ddf84655d   1         1         1       2m30s

controlplane ~ ➜  

controlplane ~ ➜  k edit service/web-service
service/web-service edited

controlplane ~ ➜  

controlplane ~ ➜  curl -sS http://10.244.154.47:30081
curl: (7) Failed to connect to 10.244.154.47 port 30081 after 0 ms: Couldn't connect to server

controlplane ~ ✖ k edit service/web-service
service/web-service edited

controlplane ~ ➜  

controlplane ~ ➜  

controlplane ~ ➜  k get all
NAME                                READY   STATUS    RESTARTS   AGE
pod/mysql                           1/1     Running   0          4m31s
pod/webapp-mysql-6ddf84655d-67rjs   1/1     Running   0          4m30s

NAME                    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/mysql-service   ClusterIP   10.43.183.102   <none>        3306/TCP         4m31s
service/web-service     NodePort    10.43.91.46     <none>        8080:30081/TCP   4m30s

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/webapp-mysql   1/1     1            1           4m30s

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/webapp-mysql-6ddf84655d   1         1         1       4m30s

controlplane ~ ➜  k edit pod/webapp-mysql-6ddf84655d-67rjs
Edit cancelled, no changes made.

controlplane ~ ➜  k edit service/mysql-service
service/mysql-service edited

controlplane ~ ➜  

controlplane ~ ➜  curl -sS http://10.244.154.47:30081
<!doctype html>
<title>Hello from Flask</title>
<body style="background: #39b54b;"></body>
<div style="color: #e4e4e4;
    text-align:  center;
    height: 90px;
    vertical-align:  middle;">

  
    <!-- <h1> DATABASE CONNECTION SUCCESSFUL !</h1> -->
    <img src="/static/img/success.jpg">
  

  
    <h2> Environment Variables: DB_Host=mysql-service; DB_Database=Not Set; DB_User=root; DB_Password=paswrd;  </h2>
  

  
    <p> From webapp-mysql-6ddf84655d-67rjs!</p>
  

  

</div>
controlplane ~ ➜  



════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
══════════════════════════════════════════════ Q.3 ═════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════

controlplane ~ ➜  #======================== Q.3 ===========================

controlplane ~ ➜  curl -sS http://10.244.154.47:30081
<!doctype html>
<title>Hello from Flask</title>
<body style="background: #ff3f3f;"></body>
<div style="color: #e4e4e4;
    text-align:  center;
    height: 90px;
    vertical-align:  middle;">

  
    <img src="/static/img/failed.png">
    <!-- <h1> DATABASE CONNECTION FAILED !!</h1> -->
  

  
    <h2> Environment Variables: DB_Host=mysql-service; DB_Database=Not Set; DB_User=root; DB_Password=paswrd; 2003: Can&#39;t connect to MySQL server on &#39;mysql-service:3306&#39; (111 Connection refused) </h2>
  

  
    <p> From webapp-mysql-6ddf84655d-7n5ns!</p>
  

  

</div>
controlplane ~ ➜  k get all
No resources found in beta namespace.

controlplane ~ ➜  k config set-context --current --namespace=gamma
Context "default" modified.

controlplane ~ ➜  

controlplane ~ ➜  curl -sS http://10.244.154.47:30081
<!doctype html>
<title>Hello from Flask</title>
<body style="background: #ff3f3f;"></body>
<div style="color: #e4e4e4;
    text-align:  center;
    height: 90px;
    vertical-align:  middle;">

  
    <img src="/static/img/failed.png">
    <!-- <h1> DATABASE CONNECTION FAILED !!</h1> -->
  

  
    <h2> Environment Variables: DB_Host=mysql-service; DB_Database=Not Set; DB_User=root; DB_Password=paswrd; 2003: Can&#39;t connect to MySQL server on &#39;mysql-service:3306&#39; (111 Connection refused) </h2>
  

  
    <p> From webapp-mysql-6ddf84655d-7n5ns!</p>
  

  

</div>
controlplane ~ ➜  curl -sS http://10.244.154.47:30081 >> err-01.txt 

controlplane ~ ➜  

controlplane ~ ➜  cat err-01.txt 

  
    <h2> Environment Variables: DB_Host=mysql-service; DB_Database=Not Set; DB_User=root; DB_Password=paswrd; 2003: Can&#39;t connect to MySQL server on &#39;mysql-service:3306&#39; (-2 Name does not resolve) </h2>
  

  
    <p> From webapp-mysql-6ddf84655d-2plq9!</p>
  -----------------------------------------------------------------


<h2> Environment Variables: DB_Host=mysql-service; DB_Database=Not Set; DB_User=root; DB_Password=paswrd; 2003: Can&#39;t connect to MySQL server on &#39;mysql-service:3306&#39; (111 Connection refused) </h2>
  

  
    <p> From webapp-mysql-6ddf84655d-67rjs!</p>
<!doctype html>
<title>Hello from Flask</title>
<body style="background: #ff3f3f;"></body>
<div style="color: #e4e4e4;
    text-align:  center;
    height: 90px;
    vertical-align:  middle;">

  
    <img src="/static/img/failed.png">
    <!-- <h1> DATABASE CONNECTION FAILED !!</h1> -->
  

  
    <h2> Environment Variables: DB_Host=mysql-service; DB_Database=Not Set; DB_User=root; DB_Password=paswrd; 2003: Can&#39;t connect to MySQL server on &#39;mysql-service:3306&#39; (111 Connection refused) </h2>
  

  
    <p> From webapp-mysql-6ddf84655d-7n5ns!</p>
  

  

</div>
controlplane ~ ➜  k get all
NAME                                READY   STATUS    RESTARTS   AGE
pod/mysql                           1/1     Running   0          92s
pod/webapp-mysql-6ddf84655d-7n5ns   1/1     Running   0          92s

NAME                    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/mysql-service   ClusterIP   10.43.237.109   <none>        3306/TCP         92s
service/web-service     NodePort    10.43.79.181    <none>        8080:30081/TCP   91s

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/webapp-mysql   1/1     1            1           92s

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/webapp-mysql-6ddf84655d   1         1         1       92s

controlplane ~ ➜  k edit pod/mysql
Edit cancelled, no changes made.

controlplane ~ ➜  k edit deployment.apps/webapp-mysql
Edit cancelled, no changes made.

controlplane ~ ➜  k edit pod/mysql
Edit cancelled, no changes made.

controlplane ~ ➜  

controlplane ~ ➜  k config set-context --current --namespace=gamma
Context "default" modified.

controlplane ~ ➜  k get all
NAME                                READY   STATUS    RESTARTS   AGE
pod/mysql                           1/1     Running   0          2m59s
pod/webapp-mysql-6ddf84655d-7n5ns   1/1     Running   0          2m59s

NAME                    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/mysql-service   ClusterIP   10.43.237.109   <none>        3306/TCP         2m59s
service/web-service     NodePort    10.43.79.181    <none>        8080:30081/TCP   2m58s

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/webapp-mysql   1/1     1            1           2m59s

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/webapp-mysql-6ddf84655d   1         1         1       2m59s

controlplane ~ ➜  k edit pod/mysql
Edit cancelled, no changes made.

controlplane ~ ➜  k edit deployment.apps/webapp-mysql
Edit cancelled, no changes made.

controlplane ~ ➜  curl -sS http://10.244.154.47:30081 >> err-01.txt 

controlplane ~ ➜  cat err-01.txt 

  
    <h2> Environment Variables: DB_Host=mysql-service; DB_Database=Not Set; DB_User=root; DB_Password=paswrd; 2003: Can&#39;t connect to MySQL server on &#39;mysql-service:3306&#39; (-2 Name does not resolve) </h2>
  

  
    <p> From webapp-mysql-6ddf84655d-2plq9!</p>
  -----------------------------------------------------------------


<h2> Environment Variables: DB_Host=mysql-service; DB_Database=Not Set; DB_User=root; DB_Password=paswrd; 2003: Can&#39;t connect to MySQL server on &#39;mysql-service:3306&#39; (111 Connection refused) </h2>
  

  
    <p> From webapp-mysql-6ddf84655d-67rjs!</p>
<!doctype html>
<title>Hello from Flask</title>
<body style="background: #ff3f3f;"></body>
<div style="color: #e4e4e4;
    text-align:  center;
    height: 90px;
    vertical-align:  middle;">

  
    <img src="/static/img/failed.png">
    <!-- <h1> DATABASE CONNECTION FAILED !!</h1> -->
  

  
    <h2> Environment Variables: DB_Host=mysql-service; DB_Database=Not Set; DB_User=root; DB_Password=paswrd; 2003: Can&#39;t connect to MySQL server on &#39;mysql-service:3306&#39; (111 Connection refused) </h2>
  

  
    <p> From webapp-mysql-6ddf84655d-7n5ns!</p>
  

  

</div><!doctype html>
<title>Hello from Flask</title>
<body style="background: #ff3f3f;"></body>
<div style="color: #e4e4e4;
    text-align:  center;
    height: 90px;
    vertical-align:  middle;">

  
    <img src="/static/img/failed.png">
    <!-- <h1> DATABASE CONNECTION FAILED !!</h1> -->
  

  
    <h2> Environment Variables: DB_Host=mysql-service; DB_Database=Not Set; DB_User=root; DB_Password=paswrd; 2003: Can&#39;t connect to MySQL server on &#39;mysql-service:3306&#39; (111 Connection refused) </h2>
  

  
    <p> From webapp-mysql-6ddf84655d-7n5ns!</p>
  

  

</div>
controlplane ~ ➜  k get all
NAME                                READY   STATUS    RESTARTS   AGE
pod/mysql                           1/1     Running   0          4m37s
pod/webapp-mysql-6ddf84655d-7n5ns   1/1     Running   0          4m37s

NAME                    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/mysql-service   ClusterIP   10.43.237.109   <none>        3306/TCP         4m37s
service/web-service     NodePort    10.43.79.181    <none>        8080:30081/TCP   4m36s

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/webapp-mysql   1/1     1            1           4m37s

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/webapp-mysql-6ddf84655d   1         1         1       4m37s

controlplane ~ ➜  k edit service/mysql-service
service/mysql-service edited

controlplane ~ ➜  

controlplane ~ ➜  

controlplane ~ ➜  curl -sS http://10.244.154.47:30081 >> err-01.txt 

controlplane ~ ➜  cat err-01.txt 

  
    <h2> Environment Variables: DB_Host=mysql-service; DB_Database=Not Set; DB_User=root; DB_Password=paswrd; 2003: Can&#39;t connect to MySQL server on &#39;mysql-service:3306&#39; (-2 Name does not resolve) </h2>
  

  
    <p> From webapp-mysql-6ddf84655d-2plq9!</p>
  -----------------------------------------------------------------


<h2> Environment Variables: DB_Host=mysql-service; DB_Database=Not Set; DB_User=root; DB_Password=paswrd; 2003: Can&#39;t connect to MySQL server on &#39;mysql-service:3306&#39; (111 Connection refused) </h2>
  

  
    <p> From webapp-mysql-6ddf84655d-67rjs!</p>
<!doctype html>
<title>Hello from Flask</title>
<body style="background: #ff3f3f;"></body>
<div style="color: #e4e4e4;
    text-align:  center;
    height: 90px;
    vertical-align:  middle;">

  
    <img src="/static/img/failed.png">
    <!-- <h1> DATABASE CONNECTION FAILED !!</h1> -->
  

  
    <h2> Environment Variables: DB_Host=mysql-service; DB_Database=Not Set; DB_User=root; DB_Password=paswrd; 2003: Can&#39;t connect to MySQL server on &#39;mysql-service:3306&#39; (111 Connection refused) </h2>
  

  
    <p> From webapp-mysql-6ddf84655d-7n5ns!</p>
  

  

</div><!doctype html>
<title>Hello from Flask</title>
<body style="background: #ff3f3f;"></body>
<div style="color: #e4e4e4;
    text-align:  center;
    height: 90px;
    vertical-align:  middle;">

  
    <img src="/static/img/failed.png">
    <!-- <h1> DATABASE CONNECTION FAILED !!</h1> -->
  

  
    <h2> Environment Variables: DB_Host=mysql-service; DB_Database=Not Set; DB_User=root; DB_Password=paswrd; 2003: Can&#39;t connect to MySQL server on &#39;mysql-service:3306&#39; (111 Connection refused) </h2>
  

  
    <p> From webapp-mysql-6ddf84655d-7n5ns!</p>
  

  

</div><!doctype html>
<title>Hello from Flask</title>
<body style="background: #39b54b;"></body>
<div style="color: #e4e4e4;
    text-align:  center;
    height: 90px;
    vertical-align:  middle;">

  
    <!-- <h1> DATABASE CONNECTION SUCCESSFUL !</h1> -->
    <img src="/static/img/success.jpg">
  

  
    <h2> Environment Variables: DB_Host=mysql-service; DB_Database=Not Set; DB_User=root; DB_Password=paswrd;  </h2>
  

  
    <p> From webapp-mysql-6ddf84655d-7n5ns!</p>
  

  

</div>
controlplane ~ ➜  k get service/mysql-service -o yaml | grep -i -A 5 selector
      {"apiVersion":"v1","kind":"Service","metadata":{"annotations":{},"name":"mysql-service","namespace":"gamma"},"spec":{"ports":[{"port":3306,"targetPort":3306}],"selector":{"name":"sql00001"}}}
  creationTimestamp: "2026-11-01T17:00:25Z"
  name: mysql-service
  namespace: gamma
  resourceVersion: "1650"
  uid: fb9a465e-7884-40a3-9080-16300ef06319
--
  selector:
    name: mysql
  sessionAffinity: None
  type: ClusterIP
status:
  loadBalancer: {}

controlplane ~ ➜  #




════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
══════════════════════════════════════════════ Q.4 ═════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════

Troubleshooting Test 4: The same 2 tier application is deployed in the delta namespace. It must display a green web 
page on success. Click on the App tab at the top of your terminal to view your application. 
It is currently failed. Troubleshoot and fix the issue.


Stick to the given architecture. Use the same names and port numbers as given in the below architecture diagram. 
Feel free to edit, delete or recreate objects as necessary.

controlplane ~ ➜  #============================ Q.4 ================================

controlplane ~ ➜  

controlplane ~ ➜  k config set-context --current --namespace=delta
Context "default" modified.

controlplane ~ ➜  

controlplane ~ ➜  k get all
NAME                                READY   STATUS    RESTARTS   AGE
pod/mysql                           1/1     Running   0          102s
pod/webapp-mysql-69867bff7d-t5brr   1/1     Running   0          101s

NAME                    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/mysql-service   ClusterIP   10.43.238.174   <none>        3306/TCP         102s
service/web-service     NodePort    10.43.57.171    <none>        8080:30081/TCP   101s

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/webapp-mysql   1/1     1            1           101s

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/webapp-mysql-69867bff7d   1         1         1       101s

controlplane ~ ➜  k edit deployment.apps/webapp-mysql
deployment.apps/webapp-mysql edited

controlplane ~ ➜  k rollout restart deployment.apps/webapp-mysql
deployment.apps/webapp-mysql restarted

controlplane ~ ➜  history | grep curl
   11  curl -sS http://10.244.154.47:30081
   16  curl -sS http://10.244.154.47:30081
   20  curl -sS http://10.244.154.47:30081
   25  curl -sS http://10.244.154.47:30081
   27  curl -sS http://10.244.154.47:30081
   30  curl -sS http://10.244.154.47:30081
   31  curl -sS http://10.244.154.47:30081 >> err-01.txt 
   41  curl -sS http://10.244.154.47:30081 >> err-01.txt 
   45  curl -sS http://10.244.154.47:30081 >> err-01.txt 
   53  history | grep curl

controlplane ~ ➜  curl -sS http://10.244.154.47:30081
<!doctype html>
<title>Hello from Flask</title>
<body style="background: #39b54b;"></body>
<div style="color: #e4e4e4;
    text-align:  center;
    height: 90px;
    vertical-align:  middle;">

  
    <!-- <h1> DATABASE CONNECTION SUCCESSFUL !</h1> -->
    <img src="/static/img/success.jpg">
  

  
    <h2> Environment Variables: DB_Host=mysql-service; DB_Database=Not Set; DB_User=root; DB_Password=paswrd;  </h2>
  

  
    <p> From webapp-mysql-7768746b6d-nfln4!</p>
  

  

</div>
controlplane ~ ➜  




════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
══════════════════════════════════════════════ Q.5 ═════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
Troubleshooting Test 5: The same 2 tier application is deployed in the epsilon namespace. It must display a green web page on success. Click on the App tab at the top of your terminal to view your application. It is currently failed. Troubleshoot and fix the issue.


Stick to the given architecture. Use the same names and port numbers as given in the below architecture diagram. Feel free to edit, delete or recreate objects as necessary



controlplane ~ ➜  #=============================== Q5 ========================

controlplane ~ ➜  

controlplane ~ ➜  

controlplane ~ ➜  k config set-context --current --namespace=epsilon 
Context "default" modified.

controlplane ~ ➜  

controlplane ~ ➜  

controlplane ~ ➜  k get all
NAME                                READY   STATUS    RESTARTS   AGE
pod/mysql                           1/1     Running   0          35s
pod/webapp-mysql-69867bff7d-fbt9t   1/1     Running   0          34s

NAME                    TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/mysql-service   ClusterIP   10.43.194.1    <none>        3306/TCP         34s
service/web-service     NodePort    10.43.100.91   <none>        8080:30081/TCP   34s

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/webapp-mysql   1/1     1            1           34s

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/webapp-mysql-69867bff7d   1         1         1       34s

controlplane ~ ➜  curl -sS http://10.244.154.47:30081
<!doctype html>
<title>Hello from Flask</title>
<body style="background: #ff3f3f;"></body>
<div style="color: #e4e4e4;
    text-align:  center;
    height: 90px;
    vertical-align:  middle;">

  
    <img src="/static/img/failed.png">
    <!-- <h1> DATABASE CONNECTION FAILED !!</h1> -->
  

  
    <h2> Environment Variables: DB_Host=mysql-service; DB_Database=Not Set; DB_User=sql-user; DB_Password=paswrd; 1045 (28000): Access denied for user &#39;sql-user&#39;@&#39;10.22.0.1&#39; (using password: YES) </h2>
  

  
    <p> From webapp-mysql-69867bff7d-fbt9t!</p>
  

  

</div>
controlplane ~ ➜  k get all
NAME                                READY   STATUS    RESTARTS   AGE
pod/mysql                           1/1     Running   0          54s
pod/webapp-mysql-69867bff7d-fbt9t   1/1     Running   0          53s

NAME                    TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/mysql-service   ClusterIP   10.43.194.1    <none>        3306/TCP         53s
service/web-service     NodePort    10.43.100.91   <none>        8080:30081/TCP   53s

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/webapp-mysql   1/1     1            1           53s

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/webapp-mysql-69867bff7d   1         1         1       53s

controlplane ~ ➜  k edit deployment.apps/webapp-mysql
deployment.apps/webapp-mysql edited

controlplane ~ ➜  

controlplane ~ ➜  k rollout restart deployment.apps/webapp-mysql
deployment.apps/webapp-mysql restarted

controlplane ~ ➜  curl -sS http://10.244.154.47:30081
<!doctype html>
<title>Hello from Flask</title>
<body style="background: #ff3f3f;"></body>
<div style="color: #e4e4e4;
    text-align:  center;
    height: 90px;
    vertical-align:  middle;">

  
    <img src="/static/img/failed.png">
    <!-- <h1> DATABASE CONNECTION FAILED !!</h1> -->
  

  
    <h2> Environment Variables: DB_Host=mysql-service; DB_Database=Not Set; DB_User=root; DB_Password=paswrd; 1045 (28000): Access denied for user &#39;root&#39;@&#39;10.22.0.1&#39; (using password: YES) </h2>
  

  
    <p> From webapp-mysql-85ff8cf554-rbcx4!</p>
  

  

</div>
controlplane ~ ➜  k get all
NAME                                READY   STATUS        RESTARTS   AGE
pod/mysql                           1/1     Running       0          115s
pod/webapp-mysql-6ddf84655d-qmpwb   1/1     Terminating   0          34s
pod/webapp-mysql-85ff8cf554-rbcx4   1/1     Running       0          20s

NAME                    TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/mysql-service   ClusterIP   10.43.194.1    <none>        3306/TCP         114s
service/web-service     NodePort    10.43.100.91   <none>        8080:30081/TCP   114s

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/webapp-mysql   1/1     1            1           114s

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/webapp-mysql-69867bff7d   0         0         0       114s
replicaset.apps/webapp-mysql-6ddf84655d   0         0         0       34s
replicaset.apps/webapp-mysql-85ff8cf554   1         1         1       20s

controlplane ~ ➜  k edit pod/mysql
error: pods "mysql" is invalid
A copy of your changes has been stored to "/tmp/kubectl-edit-424584545.yaml"
error: Edit cancelled, no valid changes were saved.

controlplane ~ ✖ k replace --force -f /tmp/kubectl-edit-424584545.yaml
pod "mysql" deleted from epsilon namespace
pod/mysql replaced

controlplane ~ ➜  curl -sS http://10.244.154.47:30081
<!doctype html>
<title>Hello from Flask</title>
<body style="background: #ff3f3f;"></body>
<div style="color: #e4e4e4;
    text-align:  center;
    height: 90px;
    vertical-align:  middle;">

  
    <img src="/static/img/failed.png">
    <!-- <h1> DATABASE CONNECTION FAILED !!</h1> -->
  

  
    <h2> Environment Variables: DB_Host=mysql-service; DB_Database=Not Set; DB_User=root; DB_Password=paswrd; 2003: Can&#39;t connect to MySQL server on &#39;mysql-service:3306&#39; (111 Connection refused) </h2>
  

  
    <p> From webapp-mysql-85ff8cf554-rbcx4!</p>
  

  

</div>
controlplane ~ ➜  curl -sS http://10.244.154.47:30081
<!doctype html>
<title>Hello from Flask</title>
<body style="background: #39b54b;"></body>
<div style="color: #e4e4e4;
    text-align:  center;
    height: 90px;
    vertical-align:  middle;">

  
    <!-- <h1> DATABASE CONNECTION SUCCESSFUL !</h1> -->
    <img src="/static/img/success.jpg">
  

  
    <h2> Environment Variables: DB_Host=mysql-service; DB_Database=Not Set; DB_User=root; DB_Password=paswrd;  </h2>
  

  
    <p> From webapp-mysql-85ff8cf554-rbcx4!</p>
  

  

</div>
controlplane ~ ➜  




════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
══════════════════════════════════════════════ Q.6 ═════════════════════════════════════════════════════════════════════
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
controlplane ~ ➜  #============================ Q.6 ============================

controlplane ~ ➜  k config set-context --current --namespace=zeta
Context "default" modified.

controlplane ~ ➜  

controlplane ~ ➜  k get all
NAME                                READY   STATUS    RESTARTS   AGE
pod/mysql                           1/1     Running   0          35s
pod/webapp-mysql-69867bff7d-cqprl   1/1     Running   0          34s

NAME                    TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/mysql-service   ClusterIP   10.43.121.30   <none>        3306/TCP         35s
service/web-service     NodePort    10.43.91.69    <none>        8080:30088/TCP   34s

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/webapp-mysql   1/1     1            1           34s

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/webapp-mysql-69867bff7d   1         1         1       34s

controlplane ~ ➜  curl -sS http://10.244.154.47:30081
curl: (7) Failed to connect to 10.244.154.47 port 30081 after 0 ms: Couldn't connect to server

controlplane ~ ✖ k edit service/web-service
service/web-service edited

controlplane ~ ➜  

controlplane ~ ➜  k get all
NAME                                READY   STATUS    RESTARTS   AGE
pod/mysql                           1/1     Running   0          99s
pod/webapp-mysql-69867bff7d-cqprl   1/1     Running   0          98s

NAME                    TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/mysql-service   ClusterIP   10.43.121.30   <none>        3306/TCP         99s
service/web-service     NodePort    10.43.91.69    <none>        8080:30081/TCP   98s

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/webapp-mysql   1/1     1            1           98s

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/webapp-mysql-69867bff7d   1         1         1       98s

controlplane ~ ➜  curl -sS http://10.244.154.47:30081
<!doctype html>
<title>Hello from Flask</title>
<body style="background: #ff3f3f;"></body>
<div style="color: #e4e4e4;
    text-align:  center;
    height: 90px;
    vertical-align:  middle;">

  
    <img src="/static/img/failed.png">
    <!-- <h1> DATABASE CONNECTION FAILED !!</h1> -->
  

  
    <h2> Environment Variables: DB_Host=mysql-service; DB_Database=Not Set; DB_User=sql-user; DB_Password=paswrd; 1045 (28000): Access denied for user &#39;sql-user&#39;@&#39;10.22.0.1&#39; (using password: YES) </h2>
  

  
    <p> From webapp-mysql-69867bff7d-cqprl!</p>
  

  

</div>
controlplane ~ ➜  k edit deployment.apps/webapp-mysql
deployment.apps/webapp-mysql edited

controlplane ~ ➜  

controlplane ~ ➜  k rollout restart deployment.apps/webapp-mysql
deployment.apps/webapp-mysql restarted

controlplane ~ ➜  curl -sS http://10.244.154.47:30081
<!doctype html>
<title>Hello from Flask</title>
<body style="background: #ff3f3f;"></body>
<div style="color: #e4e4e4;
    text-align:  center;
    height: 90px;
    vertical-align:  middle;">

  
    <img src="/static/img/failed.png">
    <!-- <h1> DATABASE CONNECTION FAILED !!</h1> -->
  

  
    <h2> Environment Variables: DB_Host=mysql-service; DB_Database=Not Set; DB_User=root; DB_Password=paswrd; 1045 (28000): Access denied for user &#39;root&#39;@&#39;10.22.0.1&#39; (using password: YES) </h2>
  

  
    <p> From webapp-mysql-648987d6f7-xv8ld!</p>
  

  

</div>
controlplane ~ ➜  k ge all
error: unknown command "ge" for "kubectl"

Did you mean this?
        set
        get
        cp

controlplane ~ ✖ k get all
NAME                                READY   STATUS        RESTARTS   AGE
pod/mysql                           1/1     Running       0          2m50s
pod/webapp-mysql-648987d6f7-xv8ld   1/1     Running       0          16s
pod/webapp-mysql-6ddf84655d-2pmfp   1/1     Terminating   0          33s

NAME                    TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/mysql-service   ClusterIP   10.43.121.30   <none>        3306/TCP         2m51s
service/web-service     NodePort    10.43.91.69    <none>        8080:30081/TCP   2m50s

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/webapp-mysql   1/1     1            1           2m50s

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/webapp-mysql-648987d6f7   1         1         1       17s
replicaset.apps/webapp-mysql-69867bff7d   0         0         0       2m50s
replicaset.apps/webapp-mysql-6ddf84655d   0         0         0       34s

controlplane ~ ➜  k edit pod/mysql
error: pods "mysql" is invalid
A copy of your changes has been stored to "/tmp/kubectl-edit-3822128857.yaml"
error: Edit cancelled, no valid changes were saved.

controlplane ~ ✖ k replace --force -f /tmp/kubectl-edit-3822128857.yaml
pod "mysql" deleted from zeta namespace
pod/mysql replaced

controlplane ~ ➜  k rollout restart deployment.apps/webapp-mysql
deployment.apps/webapp-mysql restarted

controlplane ~ ➜  curl -sS http://10.244.154.47:30081
<!doctype html>
<title>Hello from Flask</title>
<body style="background: #39b54b;"></body>
<div style="color: #e4e4e4;
    text-align:  center;
    height: 90px;
    vertical-align:  middle;">

  
    <!-- <h1> DATABASE CONNECTION SUCCESSFUL !</h1> -->
    <img src="/static/img/success.jpg">
  

  
    <h2> Environment Variables: DB_Host=mysql-service; DB_Database=Not Set; DB_User=root; DB_Password=paswrd;  </h2>
  

  
    <p> From webapp-mysql-7f4b8c97d4-dtr57!</p>
  

  

</div>
controlplane ~ ➜  
