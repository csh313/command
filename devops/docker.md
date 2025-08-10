## 打包
docker build -t cr.io.plus:80/library/ioagent-devcontainer:v1.3 -f .devcontainer/Dockerfile.base  . 打包项目
docker push  cr.io.plus:80/library/ioagent-devcontainer:v1.1 推送项目到镜像仓库
podman pull cr.io.plus/io-agent/io-agent:d521c3b7f071a6b7b6b0b74566782624312e712e

docker pull cr.io.plus/io-agent/io-agent:d521c3b7f071a6b7b6b0b74566782624312e712e

# 停止所有运行中的容器
docker stop $(docker ps -aq)

# 删除所有容器（包括已停止的）
docker rm $(docker ps -aq)

# 删除所有容器
docker stop $(docker ps -aq) && docker rm $(docker ps -aq)

# 打包镜像
docker build -f ./deployments/docker/Dockerfile.release .
# 启动容器
docker run 




