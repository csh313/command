
cat >> /etc/docker/daemon.json <<EOF

{
    "insecure-registries": [
        "cr.io.plus:80",
        "registry.wh.io.plus:5000"
    ],
    "features": {
        "containerd-snapshotter": true
    },
    "registry-mirrors": [
        "http://cr.io.plus:80/docker-io"
    ]
}
EOF

使配置生效
systemctl daemon-reload
systemctl restart docker

export http_proxy=http://proxy.wh.io.plus:7890
export https_proxy=http://proxy.wh.io.plus:7890
export no_proxy="dev1,*.local,localhost,127.0.0.1,10.*.*.*,172.*.*.*,192.168.*.*,*.inner,a:*,.io.plus,.cn,cluster.svc"



unset http_proxy
unset https_proxy
unset no_proxy

docker login cr.io.plus:80 -u admin --password IOplus240311

command: ["tail", "-f", "/dev/null"]

proxyAddress="proxy.wh.io.plus"
file=$(systemctl cat docker |grep "#" |grep service | awk '{print $2}')
sed -i "/\[Service\]/a\
Environment=\"HTTP_PROXY=http://$proxyAddress:7890/\"\n\
Environment=\"HTTPS_PROXY=http://$proxyAddress:7890/\"\n\
Environment=\"NO_PROXY=localhost,127.0.0.1,cr.io.plus\"" $file

sudo systemctl daemon-reload
sudo systemctl restart docker



tmux:
cat >> ~/.tmux.conf <<EOF
setw -g mode-keys vi
set-option -g allow-rename off # 关闭自动命名
set-option -g mouse on
bind -T copy-mode-vi v send-keys -X begin-selection
bind -T copy-mode-vi y send-keys -X copy-selection-and-cancel

set-option -g history-limit 9999999

new-session -n tmp

new-window
new-window
new-window
new-window
new-window
new-window
new-window
new-window
new-window
select-window -t 1
EOF

在线查看sqlite文件内容
在web端查看数据，支持编辑
1. yum install php php-sqlite3
2. wget  https://github.com/FrancoisCapon/LoginToASqlite3DatabaseWithoutCredentialsWithAdminer/releases/download/5.3.0--2.0/adminer-5.3.0-sqlite-en.php -O adminer.php
3. php -S 10.9.98.23:8080
4. 浏览器访问 http://10.9.98.23:8080/adminer.php
5. 输入数据库文件路径 /etc/zion/db/devops.db  , 登录


