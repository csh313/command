### 安装buildKit（远程构建镜像）
1. 安装BuildKit二进制包 wget https://github.com/moby/buildkit/releases/download/v0.23.2/buildkit-v0.23.2.linux-amd64.tar.gz
2. 解压 tar -xvzf  buildkit-v0.23.2.linux-amd64.tar.gz
3. 移动到/usr/local/bin/ sudo mv bin/buildctl /usr/local/bin/
    sudo mv bin/buildkitd /usr/local/bin/
4. 检查安装：buildctl --version 

