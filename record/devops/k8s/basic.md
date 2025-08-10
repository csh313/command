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

Kubernetes 是一个开源的容器编排平台，用于自动化部署、扩展和管理容器化应用。它提供了一个强大的框架，可以在集群中运行容器化应用，并提供高可用性、负载均衡、自动伸缩等功能。
Kubernetes 集群由控制平面和一组被称为节点的工作机器组成，这些工作机器运行容器化应用。每个集群至少需要一个工作节点才能运行 Pod。
控制平面组件 
控制平面的组件负责做出关于集群的全局决策（例如调度），以及检测和响应集群事件。控制平面组件可以在集群中的任何机器上运行。然而，为了简单起见，安装脚本通常会在同一台机器上启动所有控制平面组件，并且不会在这台机器上运行用户容器。
    API 服务器是 Kubernetes 控制平面的组件， 该组件负责公开了 Kubernetes API，负责处理接受请求的工作。 API 服务器是 Kubernetes 控制平面的前端。Kubernetes API 服务器的主要实现是 kube-apiserver。 kube-apiserver 设计上考虑了水平扩缩，也就是说，它可通过部署多个实例来进行扩缩。提供REST API接口，供客户端和其他组件调用。
    etcd 是 Kubernetes 用于存储集群状态的数据库。 etcd 是一个分布式键值存储，可用于保存 Kubernetes 集群的配置、状态和其他信息。
    kube-scheduler 负责监视新创建的、未指定运行节点（node）的 Pods， 并选择节点来让 Pod 在上面运行。  负责为新建的Pod分配合适的节点。
    kube-controller-manager 负责运行控制器进程。管理集群中的控制器，确保集群状态符合预期。
节点组件 
节点组件在每个工作机器上运行，并提供 Kubernetes 运行环境。节点组件包括：
    kubelet： 负责维护容器的生命周期，同时也负责 Pod 和容器的状态收集。kubelet 会在集群中每个节点（node）上运行。 它保证容器（containers）都运行在 Pod 中。kubelet 接收一组通过各类机制提供给它的 PodSpec，确保这些 PodSpec 中描述的容器处于运行状态且健康。  负责节点上的容器管理，接收并执行Master节点的指令。
    kube-proxy 是集群中每个节点（node）上所运行的网络代理，维护节点上的一些网络规则， 这些网络规则会允许从集群内部或外部的网络会话与 Pod 进行网络通信。  实现服务发现和负载均衡，确保容器间的通信顺畅。
    容器运行时 (Container Runtime): 负责运行容器，比如 Docker 或 rkt。 这个基础组件使 Kubernetes 能够有效运行容器。 它负责管理 Kubernetes 环境中容器的执行和生命周期。
节点 (Node): 节点是 Kubernetes 集群中运行容器化应用的机器。 Kubernetes 通过将容器放入在节点（Node）上运行的 Pod 中来执行你的工作负载。 节点可以是一个虚拟机或者物理机器，取决于所在的集群配置。 每个节点包含运行 Pod 所需的服务； 这些节点由控制面负责管理。

核心概念
pod 是最小的可部署和可管理的计算单元。它是 k8s 中应用的最小单位。pod 是一个逻辑主机，它由一个或多个容器组成，这些容器共享同一个网络命名空间、存储卷和其他依赖资源。这些容器通过共享相同的资源，可以更方便地进行通信、共享数据和协同工作。
Service：定义了一组Pod的访问策略，提供负载均衡和服务发现功能。
Deployment：提供声明式的更新机制，可以让你声明期望的状态，然后让 Kubernetes 自动完成部署。
Deployment：用于描述Pod的部署，支持滚动更新和回滚等功能。
Namespace：将集群内部的资源逻辑上隔离，便于管理和分配。

kubeadm：用来初始化集群的指令。
kubectl：用来与集群通信的命令行工具。
架构和组件：
Master节点：负责管理和控制整个集群，包括API Server、Scheduler、Controller Manager等组件。
    API Server：提供REST API接口，供客户端和其他组件调用。
    Scheduler：负责为新建的Pod分配合适的节点。
    Controller Manager：管理集群中的控制器，确保集群状态符合预期。
Node节点：实际运行容器的工作节点，包括kubelet、kube-proxy等组件。
    kubelet：负责节点上的容器管理，接收并执行Master节点的指令。在集群中的每个节点上用来启动 Pod 和容器等。
    kube-proxy：实现服务发现和负载均衡，确保容器间的通信顺畅。
