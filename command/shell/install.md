# 在linux里安装
yum install golang -y #安装golang
apt install -y golang #安装golang
apt update #升级安装包

## 迁移文件夹
scp -r root@10.9.98.39:/root/csh ./csh/  #将39服务器上的csh文件夹拷贝到本地
