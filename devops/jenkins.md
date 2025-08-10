## 通过docker拉取镜像
### 1.拉取镜像
docker pull jenkins/jenkins:lts 
长期支持版（LTS）
### 创建本地数据目录
```bash
mkdir -p /home/csh/devops/jenkins_data  # 替换为你的用户目录，如 /home/你的用户名/jenkins_data 创建目录（Linux/WSL 路径示例，Windows 可替换为 D:\jenkins 等）
chown -R 1000:1000 /home/csh/devops/jenkins_data # 赋予权限（Jenkins 容器内用户 UID 为 1000，需保证目录有权限）
```
### 启动容器
```bash
docker run -d \
  --name jenkins-lts \
  -p 8000:8080 \
  -p 50000:50000 \
  -v /home/csh/devops/jenkins_data:/var/jenkins_home \  # 你的数据挂载路径
  --dns 8.8.8.8 \  # 谷歌 DNS
  --dns 114.114.114.114 \  # 国内 DNS
  -e JENKINS_OPTS="--update-center https://mirrors.aliyun.com/jenkins/update-center.json" \  # 强制阿里云镜像源
  jenkins/jenkins:lts
docker run -d --name jenkins-lts -p 8000:8080 -p 50000:50000  -v jenkins_home:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock  --restart always   jenkins/jenkins:lts
```
### 初始化 Jenkins 界面
1. 获取初始密码
```bash
docker exec jenkins-lts cat /var/jenkins_home/secrets/initialAdminPassword
```
2. 打开浏览器，输入 http://localhost:8000 ，并输入密码，进入 Jenkins 界面。
Jenkins 在初始化时尝试连接官方插件更新服务器（https://updates.jenkins.io）,更新下国内镜像源
```bash
cd /home/csh/jenkins_data  # 替换为你的实际路径
vim hudson.model.UpdateCenter.xml
<?xml version='1.1' encoding='UTF-8'?>
<sites>
  <site>
    <id>default</id>
    <url>https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json</url>
  </site>
</sites>
```



docker run -d   --name jenkins-lts-jdk17   -p 8000:8080   -p 50000:50000   -v /home/csh/devops/jenkins_data:/var/jenkins_home   --dns 8.8.8.8   --dns 114.114.114.114   -e JENKINS_OPTS="--updateCenterUrl=https://mirrors.aliyun.com/jenkins/updates/update-center.json"   -e JAVA_OPTS="-Xms512m -Xmx2048m -Dfile.encoding=UTF-8"   -e JENKINS_JAVA_OPTIONS="-Djenkins.install.runSetupWizard=false"   jenkins/jenkins:2.426.3-lts-jdk17

