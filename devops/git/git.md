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
