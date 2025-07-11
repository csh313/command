命令：kubectl + [command] + [type] + [name]+ [flags]
command：指定要对一个或多个资源执行的操作，例如create、get、describe、delete等。(增删改查)
type：指定资源类型。资源类型不区分大小写，可以指定单数、复数或缩写形式。
name：指定资源的名称。名称区分大小写。如果省略名称，则显示所有资源的详细信息： kubectl get pods。
flags: 指定可选的参数。例如，可以使用-s或-server参数指定 Kubernetes API服务器的地址和端口。

kubectl get - 显示一个或者多个资源信息
kubectl describe - 显示一个资源的详细信息

kubectl get componentstatuses (简写cs)# 查看Master状态
kubectl get namespace # 查看所有命名空间
kubectl get pods -A  # 列出所有的pods
kubectl get pods -o wide  # 显示更多的pods列表信息
k logs -f pod-name # 查看pod的日志
kubectl exec -it pod-name -- /bin/bash # 进入pod内部
kubectl cp /path/to/local/file pod-name:/path/to/remote/file # 复制文件到pod
