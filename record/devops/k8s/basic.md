# k8s集群

k8s的三种IP：
- Node IP：每个Node节点（即物理节点）的IP地址，可以通过`kubectl get nodes -o wide`命令查看。
- Pod IP：每个Pod（容器）的IP地址，可以通过`kubectl get pods -o wide`命令查看。
- Service IP：每个Service（集群）的IP地址，集群内部访问，可以通过`kubectl get services -o wide`命令查看。

进入k8s容器的方法：
kubectl get pods：查看所有Pod的名称和IP地址。
- `kubectl exec -it <pod-name> /bin/bash`：进入指定Pod的容器，并以bash方式进入。
- `k exec -it -n kube-system <pod-name> /bin/bash`：进入指定命名空间的Pod的容器，并以bash方式进入。
退出容器的方法： exit

持久化存储PV和PVC:
- PV（Persistent Volume）：集群外部的存储，比如NFS、Ceph、GlusterFS等。
- PVC（Persistent Volume Claim）：PVC 是Pod对存储资源的一个申请, 由用户对PV的申请，可以指定访问模式（ReadWriteOnce、ReadOnlyMany、ReadWriteMany）、存储大小、访问权限等。
