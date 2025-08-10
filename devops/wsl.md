## 安装


### log
1. wsl: 检测到 localhost 代理配置，但未镜像到 WSL。NAT 模式下的 WSL 不支持 localhost 代理
解决方法：
```
1. 在用户目录下C:\Users\<your_username>，创建个文件 .wslconfig，内容如下：
[experimental]
autoMemoryReclaim=gradual
networkingMode=mirrored
dnsTunneling=true
firewall=true
autoProxy=true
2. 打开PowerShell，输入 wsl --shutdown
3. 重启wsl，输入 wsl
```