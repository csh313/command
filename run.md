打包docker容器成镜像
devops-backend
1.设置镜像名
	export gitlabMergeRequestLastCommit=csh1 
	echo $gitlabMergeRequestLastCommit
	bash scripts/build_image.sh Debug=1 EndOfArgs=EndOfArgs
2.构建容器 
	cd csh/projects/devops-backend/output/devops_compose  
	vim docker-compose.yml	bash run.sh -d
3.进入容器
	docker exec -it 16a /bin/bash
4.debug测试   
	bash runstart.sh Debug=1

远程主机上似乎禁用了 TCP 端口转发。确保 sshd_config 具有 AllowTcpForwarding yes。vim /etc/ssh/sshd_config 找到并修改 AllowTcpForwarding yes。重启 sshd 服务 systemctl restart sshd。
本地分支关联远程git branch --set-upstream-to=origin/csh csh 查看关联关系git branch -vv

tmux :tmux kill-session -t 会话名  关闭会话 
journalctl -u sshd -f查看ssh日志
vim ~/.bashrc打开bash shell的配置文件
alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'
alias dps="docker ps"
alias dex="docker exec -it"
alias dis="docker images"
alias drd="bash run.sh -d"
alias brd="bash runstart.sh Debug=1"
source ~/.bashrc使配置文件生效

将本机的ssh-copy-id传送给目的主机，实现免密

vim  u 撤销上步操作 
	A是光标到列末 I光标到列首  dd删除某行操作
	GG光标到行末  gg光标到行首	dG:删除光标下的数据
	:%d清空vim文件，u撤回上一步
	yy复制 p粘贴到光标下一行  
cat查看文件内容、创建文件、合并文件
	cat cs2.txt cs1.txt > cs.txt  # 合并多个文件
	cat > dameon <<EOF # 创建新文件
	cat >> dameon.json <<EOF # 追加内容
awk —— 按字段处理文本
重定向和管道：
重定向是将命令的输出或输入重定向到一个文件或另一个命令。比如：ls >> file.txt ls命令的输出到文件 
管道是将一个命令的输出作为另一个命令的输入。
xargs是一个使用标准数据流构建执行管道的命令

上传git修改 ：git add . [文件名] [文件名]
添加提交信息：git commit -m "Your commit message"
丢弃修改文件：git restore [文件名] [文件名]
推送修改：git push origin dev_csh
合并分支：git merge dev_csh
git fetch origin 拉取远程分支
git fetch [remote-name] [branch-name]抓取，将远程仓库的更新抓取到本地，不会合并，不指定则抓取全部
git pull [remote-name] [branch-name]拉取，将远程仓库的更新拉取到本地并合并，不指定则拉取全部 =git fetch + git merge
git pull hard origin/dev_csh 强制拉取远程分支
git branch -d dev_csh 删除本地分支
git push origin --delete dev_csh 删除远程分支
git remote add origin [link.git] 添加远程仓库
git remote -v 查看远程仓库
git clone [link.git] 克隆远程仓库
git push origin csh_dev:dev 将本地csh_dev分支推送到远程仓库的dev分支
git log --oneline 查看提交历史
git log --oneline --graph 查看提交历史树状图
git log --oneline --graph --decorate 查看提交历史树状图+分支信息
git  reflog 查看命令历史
git reset --hard [commit-id] 回退到指定commit
git reset --hard HEAD^ 回退到上一个版本
git reset file 撤销文件的add
git branch -D  local_branch_name  删除本地branch
git push origin --delete csh 删除远程分支origin/csh
git remote rm origin/dev_csh 删除远程分支  

git多个commit压缩一个commit：
git log --online查看下commit历史
git rebase -i 1cf62ba^  # 从1cf62ba的父提交开始变基
pick 表示 保留该commit 不动，作为主提交，后边的写成squash 压缩commit 
解决冲突后，git add .  git rebase --continue 继续变基
git rebase --abort 放弃变基
git push origin csh1:csh_dev --force  强制将本地csh1分支推送到远程csh_dev分支

使用rebase合并分支：
git config pull.rebase true配置下rebase
git pull origin dev  # 拉取远程dev分支并合并到当前分支
# 解决冲突后继续rebase
git add .
git rebase --continue
# 推送到远程个人分支
git push origin feature/my-feature  # 常规推送
git push -f origin feature/my-feature  # 如果rebase后需要强制推送
git pull --no-rebase origin dev  # 强制使用merge模式

暂存：
保存当前未提交的修改 git stash save "dev_csh分支的未完成工作"   
查看stash列表 git stash list
恢复最近一次stash ：git stash pop
恢复指定stash： git stash apply stash@{0} 
查看stash的修改内容 git stash show -p stash@{0}

commit：
git commit --amend --no-edit  # 合并修改到上次提交，不修改提交信息
git commit --amend #若需同时修改提交信息，去掉 --no-edit 参数
git push -f origin <分支名>  # 强制推送到远程分支



git fetch origin dev 拉取远程分支 然后再git merge 合并分支
git pull origin dev拉取最新的远程分支到本地dev分支，然后再切换到csh分支，git merge dev合并分支，这时csh分支就是合并了远程dev分支的最新代码。然后再push到远程csh分支中。
git tag [name]创建标签
git push origin --tags 推送标签
git tag -d [name]删除标签
git checkout -b [name] 创建分支
git push origin :refs/tags [branch][name]检出标签
git show [commit]显示提交信息
git update-index --assume-unchanged 文件名 本地忽略更新，重新追踪：git update-index --no-assume-unchanged 文件名
git stash push -m "Save config changes" Dockerfile* 临时忽略文件修改
git stash pop  恢复上次保存的stash内容，不过可能需要解决冲突
git stash list 查看stash列表


export gitlabMergeRequestLastCommit=VgrpHX
解压tar -xvf deploy_compose_77422334959b2f456612e591780de57897b198b3.tar
压缩tar -cvf cs.tar cs0 cs1 

which 文件名 查找文件地址
find / -name 文件名  查找文件路径(/可省略指的是地址)
		-user 用户名  查找指定用户的文件

df -T查看文件系统
清空当前目录下的文件  rm -rf ./*

ls -l /bin/ | grep sh  查找bin目录下所有sh文件 |管道输出 grep 筛选出带sh的文件
mkdir -p /data/logs/nginx/access/2021/08/17  创建目录
rmdir /data/logs/nginx/access/2021/08/17  删除目录（非空时）
rm -rf 删除文件和目录 rm -f 删除文件 rm -r 删除目录
touch 文件名 创建文件和修改时间戳
cp source dest 复制文件或目录 强制复制\cp
mv old new 移动文件或目录(或者重命名) 强制移动\mv
cat file1 file2 查看合并后的文件内容
cat file1 file2 > file3 合并文件内容并保存到新文件
cat file1 file2 >> file3 追加文件内容
cat file1 | grep "keyword"  查找文件内容
cat >> file <<EOF 追加内容到文件末尾
cat file1 | awk '{print $1}'  输出文件第一列内容
cat file1 | awk -F: '{print $1}'  输出文件第一列内容 -F指定分隔符
cat file1 | awk -F: '{print $1,$3}'  输出文件第一列和第三列内容
cat  (-n) file 查看文件内容 -n 显示行号
more 分页查看器
echo 输出内容到控制台 -t支持反斜杠的转义
ls 目录 > 覆盖 >>追加 
echo "内容" >>cs.txt 追加内容到文件末尾
echo "内容" >cs.txt 覆盖文件内容
echo "内容" | tee file 输出内容到控制台和文件
文件权限10位 rwx人可读可写可执行(或可进入目录) chmod 属主权限 属组权限 其他用户权限 
chmod 777 file 所有用户都有读写权限
ugoa chmod u+r file 给文件属主读权限

grep过滤查找 可以结合"|"管道符(表示将前一个命令的处理结果传递给后一个命令)
grep 选项 要查找的字符串 文件名（查找文件内容） -i忽略大小写 -n显示行号 -v反向查找
grep -v 选项 要查找的字符串 文件名（反向查找文件内容）
比如：ls | grep 要查找的字符串  查找当前目录下所有文件名包含要查找的字符串的文件
wc -l file 统计文件行数
ls -lh 显示文件大小
gzip 压缩文件 gzip -d 解压文件 gunzip 解压文件 gzip -r 压缩目录 -d 解压目录
zip不能压缩文件夹
tar打包 -z打包的同时压缩 -x解包 -c创建 -v显示过程 -f指定文件名 -C解压到指定目录
tar -zcvf file.tar.gz file1 file2 打包并压缩
tar -zxvf file.tar.gz -C /path/to/extract 解压到指定目录
sed -i 's#原文本#新文本#g' 文件名 替换文件内容
sed 's#原文本#新文本#g' 文件名 替换文件内容后输出到控制台，不修改原文件

sed -i '/要查找的字符串/d' 文件名 删除文件内容中包含指定字符串的行

grep查找 sed编辑 awk分析  
grep搜索、提取行
awk字段处理、query语言
sed替换、删除、追加文本
cat 合并文件内容、创建文件、追加内容
find查找文件、目录、用户、权限、类型
du显示磁盘使用情况、df显示磁盘分区信息
tee一边查看输出一边写入文件
du -sh 显示当前目录大小 du -h --max-depth=1 显示当前目录下所有子目录大小 du -h以kb/mb/gb显示当前目录大小
df 查看磁盘空间使用情况 -h 以可读方式显示
tmpfs临时的内存，基于内存的文件系统
free -h 查看内存使用情况
lsblk 显示块设备信息，查看磁盘、分区、挂载点等内容。列出系统上的所有的磁盘列表 -f显示文件系统信息 uuid等  比fdisk更直观
fdisk -l查看磁盘分区信息
mount 检查挂载文件系统信息


硬件没名字就找不到，挂载就是给硬件一个名字（目录名） 目录只是逻辑位置，真实的硬盘位置还得看挂载点
文件系统是操作系统用来组织、存储和管理数据的一种结构和方法。挂载（mount）就是把某个文件系统关联（接入）到当前系统的某个目录上，用户就能访问这个文件系统里的内容。
mount 显示已挂载文件系统
mount [-t 文件系统类型] [-o 挂载选项] 文件系统设备 挂载点
umount 卸载已挂载文件系统
我理解为如果不进行分区，那么磁盘都会在/根目录下。在boot下分区，挂载点就是boot，在boot里面的所有文件都会存储到磁盘分区1里，而不是根目录磁盘
uname -r 查看内核版本
arch显示机器的处理器架构


服务管理
service 服务名  start|stop|restart|status 启动|停止|重启|查看状态
systemctl start|stop|restart|status 服务名  启动|停止|重启|查看状态
proc进程管理
ps -ef 显示子父进程之间的关系
ps aux查看系统中所有进程

ifconfig查看当前的网络连接和ip信息
netstat -anp 显示网络连接信息 |grep 端口号 显示指定端口的连接信息 |netstat -nlp | grep 进程号 显示指定进程的连接信息
netstat显示网络状态和端口占用信息
ip地址+进程端口号 = 套接字（socket）
hostname查看主机名 /etc/hostname文件里
hostnamectl查看主机名、系统版本、内核版本、主机ID、时区、网络信息等 也可修改信息
cat /etc/hosts查看主机名和ip的对应关系
systemctl enable|disable 服务名 开机启动|关闭开机启动
systemctl list-unit-files|list-units 列出所有系统服务
systemctl is-enabled 服务名 显示服务是否开机启动
systemctl daemon-reload 重新加载系统服务
shutdown 关机 shutdown now 立即关机 shutdown 时间	
reboot 重启


/bin 二进制文件，存放着经常使用的命令
/boot 包含引导文件(如链接、镜像文件)，存放着启动Linux系统所需的各种文件
/dev 设备文件，存放着Linux系统使用的设备文件
/etc 系统配置文件，存放着各种系统配置文件
/home 用户目录，存放着用户的主目录，一般是以用户账号命名的
/lib 库文件，存放着系统运行所需的共享库文件
/media 媒体文件，存放着可移动介质，如软驱、光驱等
/mnt 挂载文件，存放着临时挂载的文件系统
/opt 第三方软件安装目录，存放着第三方软件
/proc 虚拟文件系统，存放着系统的内核信息，它主要用于获取系统和进程的信息，调试和管理系统。
/root 超级用户目录，超级用户的主目录
/run 运行时文件，存放着临时文件，如日志文件
/sbin 系统管理命令，存放着系统管理命令
/srv 服务文件，存放着服务器提供服务的文件
/sys 系统设备文件，存放着设备驱动程序和其他内核文件
/tmp 临时文件，存放着临时文件
/usr 多用户文件，存放着系统应用程序、库文件、命令等
/var 可变目录，存放着系统日志、登录文件、打印队列等
/usr/bin系统用户使用的应用程序
passwd 存放着系统所有用户的口令文件
/etc/passwd 存放着系统所有用户信息

 mmop paxos --lu获取 LU 信息
 Paxos 是一个分布式一致性算法,主要用于管理集群、授权和数据一致性


 脚本以#!/bin/bash开头，表示使用bash解释器执行脚本。
 变量名=变量值  # 定义变量 可用$(())、[]进行运算 	
 echo $变量名  # 输出变量值
 echo "Hello, $变量名!"  # 输出带变量的字符串
 sh文件里：$0表示脚本名，$1表示第一个参数，$2表示第二个参数，以此类推。
 $#表示所有输入参数的个数，常用于循环，$@表示所有参数列表,把每个参数区分对待。$*表示所有参数列表，以空格分隔，把所有参数看作一个整体。
 $?表示上一条命令的返回值，0表示执行成功，非0表示失败。
 脚本里的命令以反斜杠\结尾，表示接着上一行继续执行。
 脚本里的注释以#开头。
 脚本里的条件判断用if、then、fi表示，用test命令进行判断。
 判断 test 条件 或者 [ 条件 ]，条件支持正则表达式。[]条件前后要有空格
 -eq 等于 -ne 不等于 -gt 大于 -lt 小于 -ge 大于等于 -le 小于等于  如果用(())可以直接用符号><=
 -r 存在 -w 可写 -x 可执行 -f 存在且为文件 -d 存在且为目录 -e 存在   是否可读：[ -r hello.sh]
 多条件判断 && ||前一个执行成功才执行后边的 

 if [ $变量名 -eq 100 ]; then  # 判断变量是否等于100
 echo "变量等于100"
 fi

 if [ $变量名 -eq 100 ] && [ $变量名2 -eq 200 ]
 then  # 判断变量是否等于100且变量2是否等于200
 elif [ $变量名 -eq 300 ]; then  # 判断变量是否等于300
 else  # 如果都不满足，则执行else
 fi
 多个条件在一起,用-a和-o表示与或关系。以防变量为空，可在变量后加个值 ，比如 "$1" x

 case $变量名 in  # 多分支判断
 "值1")
 代码块 ;;  # ;;表示命令结束，相当于break
 "值2")
 代码块 ;;
 *)
 代码块 ;; # 如果都不是就执行此操作
 esac
 变量值1|变量值2|变量值3...|变量值n)  # 多分支判断，用|分隔多个值，最后一个值用|或)结尾
 变量值1|变量值2|变量值3...|变量值n) 代码块 ;;  # 多分支判断，用|分隔多个值，最后一个值用|或)结尾

for ((初始值;循环控制;变量变化))
do
 循环体
done

while []
do
 循环体
done

read读取控制台输入 -t 等待时间 -p 提示信息 变量名  # 读取控制台输入，并将输入的值赋值给变量名
read -t 10 -p "请输入用户名: " username  # 等待10秒，提示用户输入用户名,将输入的值赋值给变量username

basename显示当前路径的文件名，也可分割路径和文件名 basename /path/to/file.txt
basename /path/to/file.txt .txt  # 显示文件名，并去掉后缀
dirname显示当前路径的目录名 dirname /path/to/file.txt  # 显示路径



	//检查虚拟组节点是否有/etc/zion目录，如果没有则创建
	_, err = utils.SSHcmdout(client, "/usr/bin/test -d /etc/zion")
	if err != nil {
		log.Printf("虚拟组节点 %s 上不存在 /etc/zion 目录，正在创建...", nodeInfo.NodeName)
		_, err = utils.SSHcmdout(client, "/usr/bin/mkdir -p /etc/zion")
		if err != nil {
			log.Panicf("创建目录 /etc/zion 失败: %v", err)
		}
		log.Printf("虚拟组节点 %s 上创建目录 /etc/zion 成功", nodeInfo.NodeName)
	} else {
		log.Printf("虚拟组节点 %s 上已存在 /etc/zion 目录", nodeInfo.NodeName)
	}

正则表达式：
cat /etc/passwd | grep lo
^匹配字符串开头 以匹配字符串开头
    如：grep '^root' /etc/passwd 匹配以root开头的字符串
$匹配字符串结尾 、特殊字符 ^$匹配空行
.匹配任意字符 如 r..t
*匹配0个或多个字符 如 r.*t 匹配rant、rat、rot、ratt等 
+匹配1个或多个字符 如 r.+t  ro+t 表示匹配rot或4个字符
?匹配0个或1个字符 如 r.?t 
[]匹配括号中的字符 如 [a-z] ，相当于一个字符
[^]匹配不在括号中的字符 如 [^abc] ，匹配非abc的字符，相当于一个字符
\匹配特殊字符，如匹配$符：'\$' 
|或 如'index|start' 匹配index或start
{m,n}匹配m到n个字符 
{m,}匹配m个或更多字符 
{,n}匹配0到n个字符 
{m}匹配m个字符，如匹配两个a：a{2}
\w匹配字母、数字、下划线 
\W匹配非字母、数字、下划线 
\d匹配数字 
\D匹配非数字 
\s匹配空白字符 
\S匹配非空白字符 
\b匹配单词边界 
\B匹配非单词边界 
\n匹配换行符 
\t匹配制表符 

awk文本处理
cat /etc/passwd | awk -F ":" '/^root/ {print $6","$7}'获得第6、7列并用逗号分隔
cat /etc/passwd | awk -F ":" 'BEGIN{print "user"} /^root/ {print $6","$7} END{print "cs"}'  # 输出以root开头的用户信息，并最开头加上user，在最后输出cs
cat /etc/passwd | awk -v i=2 -F ":" '{print $3+i}'第三列加i
awk -F ":" '{print "文件名："FILENAME "行号:" NR "列数:" NF}' /etc/passwd  # 输出文件名、行号、列数


/usr/bin/getent passwd $username 查询用户
/usr/bin/getent group $groupname 查询组
/usr/sbin/groupadd -g $uid $username 创建组
/usr/sbin/useradd -M -u $uid -g $gid $username 创建用户
/usr/bin/passwd $username 修改密码
/bin/bash -c "echo 'fsroot:123' | /usr/sbin/chpasswd"
/usr/bin/userdel $username 删除用户
/usr/bin/groupdel $groupname 删除组
删除用户 /usr/sbin/userdel -r $username
删除组 /usr/sbin/groupdel $groupname

原本的，如果code=2直接报错返回
现在，是如果code=2说明用户不存在，进行下一步，但问题是是批量进行操作，有的已经存在，有的就不存在，已经存在再创建就报错  --》使用for循环来单点实现创建
当ExecRemoteCmd执行exector.ExecCmd有错时，先不Abort，需要进一步判断是什么错误，

Manual: 统一参数 → 适用于所有节点执行相同命令
Inside: 个性化参数 → 每个节点可以有不同的参数
Outside: 动态构建 → 通过函数动态生成参数

WithSkipError(true): 某个节点失败不影响其他节点
HasAnyOutSideNodeArgs检查节点列表中是否有任何一个节点的参数类型是 Outside