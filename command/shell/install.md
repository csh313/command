# 在linux里安装
yum install golang -y #安装golang
apt install -y golang #安装golang
apt update #升级安装包

## 迁移文件夹
scp -r root@10.9.98.39:/root/csh ./csh/  #将39服务器上的csh文件夹拷贝到本地


## 扩容磁盘
1. 关闭虚拟机，扩容
2. 开启虚拟机，下载sudo apt-get install gparted
### 错误：
root@csh:~# sudo apt-get install gparted
正在读取软件包列表... 完成
正在分析软件包的依赖关系树
正在读取状态信息... 完成
E: 无法定位软件包 gparted
root@csh:~#  
```bash
#### sudo apt-get update # 更新软件包
```
### 错误：
The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 由于没有公钥，无法验证下列签名
```bash
sudo apt-get install debian-archive-keyring #安装debian-archive-keyring包（包含官方密钥）
#若报错就手动导入密钥
curl -s https://ftp.debian.org/debian/pool/main/d/debian-archive-keyring/ | grep -oP 'debian-archive-keyring_\d+\.\d+_all\.deb' | sort -V | tail -1 # 列出可用的debian-archive-keyring版本
wget https://ftp.debian.org/debian/pool/main/d/debian-archive-keyring/debian-archive-keyring_2025.1_all.deb
sudo dpkg -i debian-archive-keyring_2025.1_all.deb #下载安装的包
sudo apt-get install -f      # 修复可能的依赖问题

sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0E98404D386FA1D9 6ED0E7B82643E131 F8D2585B8783D481 78DBA3BC47EF2265 54404762BBB6E853 BDE6D2B9216EC7A8 #再次导入密钥
sudo apt-get update #更新软件源
```
