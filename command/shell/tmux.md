# tmux
sudo yum install tmux -y 安装tmux
# 配置tmux
cat >> ~/.tmux.conf <<EOF
setw -g mode-keys vi
set-option -g allow-rename off # 关闭自动命名
set-option -g mouse on
bind -T copy-mode-vi v send-keys -X begin-selection
bind -T copy-mode-vi y send-keys -X copy-selection-and-cancel
set-option -g set-titles on  # 显示窗口标题（便于区分）

set -g terminal-overrides ",xterm*:smcup@:msgr@"  # 关键配置，兼容 xterm 系列终端
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

# 启动tmux
tmux 
tmux attach -t your_name 连接已有的会话
Ctrl + b 然后按 d（即 detach） 退出当前会话
exit 或 Ctrl + d 关闭当前会话
tmux kill-session -t <session-name> 关闭指定会话
Ctrl + b 然后按 c 创建新窗口
Ctrl + b 然后按窗口号（如 0）   切换到编号窗口（如0）	
Ctrl + b 然后按 ,        重命名当前窗口	
Ctrl + b c	创建新窗口（相当于一个新标签页）
Ctrl + b x	关闭当前面板