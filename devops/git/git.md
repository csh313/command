# Git 使用步骤
## 使用rebase合并分支
git config pull.rebase true 
git pull origin dev # 拉取远程dev分支并合并到当前分支
## 解决冲突后继续rebase
git add .
git rebase --continue
## 解决冲突后提交
git commit -m "commit message"
## 推送到远程仓库
git push origin dev
git push -f origin feature/my-feature  # 如果rebase后需要强制推送
git push origin csh1:csh_dev --force  强制将本地csh1分支推送到远程csh_dev分支
git rebase --abort 放弃变基
git push -f origin <分支名>  # 强制推送到远程分支


## 删除分支
git branch -d dev  # 删除本地dev分支
git push origin --delete dev  # 删除远程dev分支

## 丢弃、恢复修改
git checkout file 丢弃未暂存的修改
git clean -f 删除未跟踪的文件，不可逆
git restore file 将修改的文件恢复到暂存区的状态
git reset --hard HEAD^ 提交后，恢复到上一个版本
git reset --hard [commit] 提交后，恢复到指定版本

## 暂存 stash
git stash save "dev_csh分支的未完成工作" # 暂存当前工作区的修改
git stash list  # 查看暂存区列表
git stash pop  # 恢复暂存区的修改 (默认恢复最近一次暂存并删除)
git stash drop  # 删除暂存区的修改
git stash apply stash@{0}  # 恢复指定暂存区的修改

## commit
git commit --amend --no-edit  # 合并修改到上次提交，不修改提交信息
git commit --amend #若需同时修改提交信息，去掉 --no-edit 参数
git show [commit]显示提交信息
git update-index --assume-unchanged 文件名 本地忽略更新
git update-index --no-assume-unchanged 文件名 重新追踪

## 合并分支
一种情况是，你需要另一个分支的所有代码变动，那么就采用合并（git merge）；
另一种情况是，你只需要部分代码变动（某几个提交），这时可以采用 Cherry pick。
git log --oneline -3 查看最近三次提交记录
git cherry-pick [commit 哈希值] 合并指定提交到当前分支
git cherry-pick [commit1]..[commit2] 合并指定范围的提交到当前分支(不包含commit1，若包含就commit1^..)

##log
git log --oneline -3 查看最近三次提交记录
git log --oneline --graph 查看分支合并图
git log 查看所有提交记录
常见问题：
    git cherry-pick --abort取消上次操作。
    git cherry-pick --continue恢复上次操作。
    git cherry-pick --continue 解决冲突后继续rebase。


## git常用命令：
vim ~/.bashrc
alias g='git '
alias ga='git add '
alias gaa='git add *'
alias gap='git add -p '
alias gam='git am -s '
alias gcmam='git commit --amend'
alias gcp='git cherry-pick '
alias gb='git branch '
alias gba='git branch -a'
alias gbva='git branch -va'
alias gcl='git clone '
alias gcm='git commit -s '
alias gcmam='git commit --amend'
alias gco='git checkout '
alias gcol='git checkout -'
alias gcom='git checkout master'
alias gcotm='git checkout t-master'
alias gcon='git checkout next'
alias gcod='git checkout dev'
alias gd='git diff '
alias gds='git diff --stat '
alias gdst='git diff --staged '
alias gdsts='git diff --staged --stat '
alias gfea='git fetch --all'
alias gl='git log --decorate'
alias glgra='git log --decorate  --graph'
alias gll='git log --oneline --decorate'
alias gllwc='git log --oneline --decorate |wc'
alias gll5='git log -n 5 --oneline --decorate'
alias gll0='git log -n 0 --oneline --decorate'
alias gllgra='git log --oneline --decorate --graph'
alias gmv='git mv '
alias gpull='git pull '
alias gpom='git push origin master'
alias gr='git remote -v '
alias grbab='git rebase --abort'
alias grbcon='git rebase --continue'
alias grbh2='git rebase -i HEAD~2'
alias grbh3='git rebase -i HEAD~3'
alias grbh4='git rebase -i HEAD~4'
alias grbh5='git rebase -i HEAD~5'
alias grbh6='git rebase -i HEAD~6'
alias grbh7='git rebase -i HEAD~7'
alias grbh8='git rebase -i HEAD~8'
alias grbh9='git rebase -i HEAD~9'
alias grbh0='git rebase -i HEAD~10'
alias grbh11='git rebase -i HEAD~11'
alias grbh12='git rebase -i HEAD~12'
alias grbh13='git rebase -i HEAD~13'
alias grbh14='git rebase -i HEAD~14'
alias grbh15='git rebase -i HEAD~15'
alias grbh16='git rebase -i HEAD~16'
alias grbh17='git rebase -i HEAD~17'
alias grbh18='git rebase -i HEAD~18'
alias grbh19='git rebase -i HEAD~19'
alias grbh20='git rebase -i HEAD~20'
alias grbh21='git rebase -i HEAD~21'
alias grbh22='git rebase -i HEAD~22'
alias grbh23='git rebase -i HEAD~23'
alias grbh24='git rebase -i HEAD~24'
alias grbh25='git rebase -i HEAD~25'
alias grest='git restore '
alias gso='git show'
alias gsoh1='git show HEAD~1'
alias gsoh2='git show HEAD~2'
alias gsoh3='git show HEAD~3'
alias gsoh4='git show HEAD~4'
alias gsoh5='git show HEAD~5'
alias gsoh6='git show HEAD~6'
alias gsoh7='git show HEAD~7'
alias gsoh8='git show HEAD~8'
alias gsoh9='git show HEAD~9'
alias gsoh0='git show HEAD~10'
alias gs='git status '
alias gss='git status -s'
alias gt='git tag '

alias gsthl='git stash list '
alias gsths='git stash show '
alias gsthsp='git stash show -p'
alias gsthpu='git stash  push '
alias gsthpum='git stash  push -m'
alias gsthpo='git stash pop '


source ~/.bashrc
