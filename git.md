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

## git操作历史

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
