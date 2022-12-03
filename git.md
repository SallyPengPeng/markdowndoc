## 换行符冲突

```shell
#提交时转换为LF，检出时转换为CRLF
git config --global core.autocrlf true   
#提交时转换为LF，检出时不转换
git config --global core.autocrlf input   
#提交检出均不转换
git config --global core.autocrlf false
```

```shell
#拒绝提交包含混合换行符的文件
git config --global core.safecrlf true   
#允许提交包含混合换行符的文件
git config --global core.safecrlf false   
#提交包含混合换行符的文件时给出警告
git config --global core.safecrlf warn
```

## branch

```shell
# 修改分支名
git branch -m <oldbranch> <newbranch>
# 查看分支
git branch
git branch -a
git branch -r
# 删除分支
git branch -d <branchname>
```

## rebase

```shell
# 合并节点
git rebase -i HEAD~3 
git rebase -i 36224db
```

```shell
git rebase --abort # 退出rebase，回到之前的状态
# rebase有冲突时，解决冲突后
new-feature$ git add .
new-feature$ git rebase --continue
```

## status

显示工作目录和暂存区的状态，有3类

```shell
# Untracked files:(没有tracked过的文件, 即从没有add过的文件)
# (use "git add <file>..." to include in what will be committed)
#hello.pyc
#
# On branch master
# Changes to be committed:  (已经在stage区, 等待添加到HEAD中的文件)
# (use "git reset HEAD <file>..." to unstage)
#modified: hello.py
#
# Changes not staged for commit: (有修改, 但是没有被添加到stage区的文件)
# (use "git add <file>..." to update what will be committed)
# (use "git checkout -- <file>..." to discard changes in working directory)
#modified: main.py
```

## stash

```shell
git stash # 暂存所有未提交的修改（包括暂存的和非暂存的）
git stash save "test-cmd-stash"

git stash pop [stash@{1}] # 恢复之前缓存的工作目录, 并将缓存中删除

git stsh apply # 将缓存堆栈中的stash应用到工作目录中，但并不删除stash拷贝
git stash list # 查看暂存区
git stash drop stash@{1} # 移除暂存区某一个stash

git stash show [stash@{$num}] # 显示暂存做了哪些改动
git stash clear # 删除所有暂存
```

## git操作历史

```shell
git reflog
```

```
e0ddf4f (HEAD -> feature/button-dynamic-func-phl, origin/feature/button-dynamic-func-phl) HEAD@{0}: reset: moving to e0ddf4f
3aebe74 HEAD@{1}: commit: 12
e0ddf4f (HEAD -> feature/button-dynamic-func-phl, origin/feature/button-dynamic-func-phl) HEAD@{2}: commit: feat: 按钮组件，'动态函数'属性改为textarea 并支持执行用户自定义的方法
2cfa32a (origin/develop, origin/HEAD, develop) HEAD@{3}: checkout: moving from develop to feature/button-dynamic-func-phl
2cfa32a (origin/develop, origin/HEAD, develop) HEAD@{4}: pull: Fast-forward
```

## alias

```shell
co = checkout
sw = switch
br = branch
recommit = commit --amend
commend = commit --amend --no-edit
clearbranch = remote prune origin
plg = log -10 --graph --pretty=format:'%C(yellow)%h%Creset -%Cred%d%Creset %s %Cgreen| %cr %C(bold blue)| %an%Creset' --abbrev-commit --date=relative
```

## git提交规范

http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html

[http://www.ruanyifeng.com/blog/2015/08/git-use-process.htm](http://www.ruanyifeng.com/blog/2015/08/git-use-process.html)l

## commit提交类型

feat：新功能（feature）

fix：修补bug

docs：文档（documentation）

style： 格式（不影响代码运行的变动）

refactor：重构（即不是新增功能，也不是修改bug的代码变动）

test：增加测试

chore：构建过程或辅助工具的变动

## 合并提交点

有3种方法

1. 使用软重置 注：XXX为需要合并的最早一个提交点前的提交点hash

git reset XXX

2. 使用 git rebase -i XXX

| **值**    | **说明**                                               |
| --------- | ------------------------------------------------------ |
| p, pick   | 使用该次提交                                           |
| r, reword | 使用该次提交，但重新辩题提交信息                       |
| e, edit   | 使用该次提交，但停止到该次提交                         |
| s, squash | 将该commit和前一个commit合并                           |
| f, fixup  | 将该commit和前一个commit合并，但不保留该提交的注释信息 |
| x, exec   | 执行shell命令                                          |
| d, drop   | 丢弃该commit                                           |

3. amend,仅适用于第2个提交点commit时

git commit --amend -m "提交msg" 注： 保留一个提交点，提交信息为最新的这个

git commit --amend --no-edit   注： 仅保留一个提交点，提交信息使用之前的
