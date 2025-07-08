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


