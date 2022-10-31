参考：

http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html

[http://www.ruanyifeng.com/blog/2015/08/git-use-process.htm](http://www.ruanyifeng.com/blog/2015/08/git-use-process.html)l

 

**特别说明：**

1. 工程里直接使用提供的node_modules包（非通过npm线上安装）时，需在使用时先执行：npm rebuild（重新构建），保证所有依赖构建成功。（避免提交时不能触发eslint代码校验等问题）

 

一、git commit提交方式

1. 简要描述一行提交文本，例如：feat: 新增指令修改功能   fix: bug-ID10000-批量执行提示错误

git commit -m 'XXX: xxxx'

2. 需要提交多行文本情况，
   1. 进入交互式页面编辑，例如一个分支提交了多个bug


git commit   注：进入交互式页面后，按i编辑，完成后，按esc 然后输入 :wq 退出

参考提交描述：

fix: bug-list-20210222 【D1003176】指令执行：子层申赎-成交确认，交收录入窗口新加的字段需要添加输入校验

​     【ID1003174】交易开关接口配置：配置表格分页查询问题

二、git commit提交类型

feat：新功能（feature）

fix：修补bug

docs：文档（documentation）

style： 格式（不影响代码运行的变动）

refactor：重构（即不是新增功能，也不是修改bug的代码变动）

test：增加测试

chore：构建过程或辅助工具的变动

三、合并提交点，提交merge请求前，需保证只有一个commit

有3种方法

1. 使用软重置 注：XXX为需要合并的最早一个提交点前的提交点hash

git reset XXX

git add .

git commit

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

四、保证提交分支基于develop最新分支，避免merge时出现冲突情况

1. 新建分支前拉取develop最新代码，然后新建分支

```shell
 develop$ git pull
 develop$ git checkout -b new-feature
```

2. git commit 完成后，进入develop分支更新最新代码，然后进入业务分支，rebase到develop最新节点

```shell
develop$ git pull
develop$ git checkout new-feature
new-feature$ git rebase develop
\# 以下为rebase有冲突时执行，解决冲突后
new-feature$ git add .
new-feature$ git rebase --continue
```

五、push到远程后，在gitlab查看分支所有改动，确保没有错误的改动点，然后自己提交 **merge request** 给相应的同事，并告知帮忙review代码