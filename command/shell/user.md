## user
1. find
cat /etc/passwd # 查看系统用户信息
getent passwd  # 查看系统用户信息
who # 查看当前登录用户信息
2. add
useradd username # 添加用户
useradd -m username # 添加用户并创建主目录
useradd -g groupname username # 添加用户并指定用户组
useradd -M -u uid -g gid username # 添加用户并指定用户ID和用户组ID
3. delete
userdel username # 删除用户
userdel -r username # 删除用户及其主目录

## group
1. find
cat /etc/group # 查看系统用户组信息
getent group  # 查看系统用户组信息
2. add
groupadd groupname # 添加用户组
groupadd -g gid groupname # 添加用户组并指定组ID
3. delete
groupdel groupname # 删除用户组

## passwd
passwd username # 修改用户密码
/bin/bash -c "echo 'username:newpassword' | chpasswd" # 修改用户密码

