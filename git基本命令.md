一丶 git init 初始化本地

二丶 git add <文件名、目录名>   提交到暂存区

三丶 git commit  -m '提交信息'  提交到git本地版本库

四丶 如果add 错了文件，此时想撤回暂存区可以使用：

​		git reset HEAD   撤销的操作

五丶git log -5 --pretty=oneline  打印提交日志

----------------------------------------------------------------------------------

**六丶git如何版本回退**

git reset --hard HEAD^  回退到上一个版本                                                                                                                                                                                                                                                                                                                                         

git reset --hard HEAD^^  回退到上两个版本

git reset --hard HEAD~50  回退50个版本

**七丶 如果回退完后又想回到之前最新的版本**

git reset --hard 6e175  后五位跟的是版本标识符

git reflog 显示所有的操作记录

**八丶如果进行了误操作，commit完后，不小心删除了文件，**

git checkout -- xx/xx  可以从git本地库拉回到工作区

**九丶如果不想要某个文件彻底删除**

git rm xx/xx

**十丶显示git本地库列表**

git ls-files

-----------------------------------------------------------------------------------------------------

**十一丶 生成ssh公钥密钥**

 ssh-keygen -t rsa -C '1157150179@qq.com'

**十二丶将文件推到远程仓库**

git remote add origin git@github.com:hyf0624/git02.git  与远程库建立链接

git add '文件名'

git commit -m '提交到git本地库'

git push -u origin master 推送到远程仓库

------------------------------------------------------------------------------------------

**十三丶git的分支操作**

git checkout 'branch' 切换到指定分支

git checkout -b 'new_branch' 新建分支并切换到新建分支

git branch -d 'branch' 删除指定分支

git branch 查看所有分支,并且*标记当前所有分支
git merge 'branch' 合并分支

git branch -m | -M oldbranch newbranch 重命名分支，如果newbranch名字分支已经存在，则需要使用-M强制重命名，否则，使用-m进行重命名



**十四丶分支的push与pull操作**

git branch -a 查看本地与远程分支

git push origin 'branch_name' 推送本地分支到远程

git push origin :remote_branch 删除远程分(本地分支还在保留)

git checkout -b local_branch origin/remote_branch 拉取远程指定分支并在本地创建分支

在直接在github上新建分支需要执行 git fetch后才能用 git branch -a 检查到



十五丶 由于github 现在默认分支之前的master分支改成了main分支, 所以现在提交代码流程可能改变了

```js
git init  开始
git config --global init.defaultBranch main 把默认分支改成main分支
git pull --rebase origin main  将README文件拉取下来
git add .
git commit -m '提交代码'
git push origin main
```

