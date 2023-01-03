$0当前Shell程序的文件名

dirname $0，获取当前Shell程序的路径

cd `dirname $0`，进入当前Shell程序的目录



$? 最后运行的命令的结束代码（返回值）

## cd

```shell
cd ~ # 跳到自己的 home 目录
cd - # 切换到上次所在目录
cd ..
```

## mv

用来为文件或目录改名、或将文件或目录移入其它位置

```shell
# 将/usr/runoob 下的所有文件和目录移到当前目录下
$ mv /usr/runoob/*  . 
# 将文件aaa 改名为 bbb 
mv aaa bbb
# 将info 目录放入 logs 目录中。注意，如果 logs 目录不存在，则该命令将 info 改名为 logs
mv info/ logs 
```

## chmod

控制用户对文件的权限

```shell
chmod [-cfvR] [--help] [--version] mode file...
```

mode : 权限设定字串，格式如下 :

```shell
[ugoa...][[+-=][rwxX]...][,...]
```

常用：

- u 表示该文件的拥有者，g 表示与该文件的拥有者属于同一个群体(group)者，o 表示其他以外的人，a 表示这三者皆是。
- \+ 表示增加权限、- 表示取消权限、= 表示唯一设定权限。
- r 表示可读取，w 表示可写入，x 表示可执行，X 表示只有当该文件是个子目录或者该文件已经被设定过为可执行。

```shell
# 将文件 file1.txt 设为所有人皆可读取 
chmod ugo+r file1.txt
chmod a+r file1.txt
```

## cat

连接文件并打印到标准输出设备上

-n 或 --number：由 1 开始对所有输出的行数编号。

-b 或 --number-nonblank：和 -n 相似，只不过对于空白行不编号。

-s 或 --squeeze-blank：当遇到有连续两行以上的空白行，就代换为一行的空白行。

-E 或 --show-ends : 在每行结束处显示 $。

-T 或 --show-tabs: 将 TAB 字符显示为 ^I。

```shell
# 把 textfile1 和 textfile2 的文档内容加上行号（空白行不加）之后将内容附加到 textfile3 文档里
cat -b textfile1 textfile2 >> textfile3
# 清空 /etc/test.txt 文档内容
cat /dev/null > /etc/test.txt
# 创建文件，并把标准输入输出到filename文件中，以ctrl+d作为输入结束
cat>filename
# 以EOF作为输入结束，和ctrl+d的作用一样
cat>filename<<EOF，以EOF作为输入结束，和ctrl+d的作用一样
```

## mv

用来为文件或目录改名、或将文件或目录移入其它位置

- **-b**: 当目标文件或目录存在时，在执行覆盖前，会为其创建一个备份。
- **-i**: 如果指定移动的源目录或文件与目标的目录或文件同名，则会先询问是否覆盖旧文件，输入 y 表示直接覆盖，输入 n 表示取消该操作。
- **-f**: 如果指定移动的源目录或文件与目标的目录或文件同名，不会询问，直接覆盖旧文件。
- **-n**: 不要覆盖任何已存在的文件或目录。
- **-u**：当源文件比目标文件新或者目标文件不存在时，才执行移动操作

```shell
# 将文件 aaa 改名为 bbb
mv aaa bbb
# 将 info 目录放入 logs 目录中。注意，如果 logs 目录不存在，则该命令将 info 改名为 logs
mv info/ logs 
# 将 /usr/runoob 下的所有文件和目录移到当前目录下
$ mv /usr/runoob/*  . 
```

## mkdir

用于创建目录，-p代表如果目录不存在，就创建一个

```
# mkdir [-p] dirName
# 在工作目录下的 runoob2 目录中，建立一个名为 test 的子目录。
# 若 runoob2 目录原本不存在，则建立一个。（注：本例若不加 -p 参数，且原本 runoob2 目录不存在，则产生错误。）
mkdir -p runoob2/test
```

## scp

secure copy, 是 linux 系统下基于 ssh 登陆进行安全的远程文件拷贝命令

```shell
scp [可选参数] file_source file_target 
```

```shell
# 从本地复制到远程，需要在本地的终端执行
# 第1,2个指定了用户名，命令执行后需要再输入密码，第1个仅指定了远程的目录，文件名字不变，第2个指定了文件名；
# 第3,4个没有指定用户名，命令执行后需要输入用户名和密码，第3个仅指定了远程的目录，文件名字不变，第4个指定了文件名
scp local_file remote_username@remote_ip:remote_folder 
scp local_file remote_username@remote_ip:remote_file 
scp local_file remote_ip:remote_folder 
scp local_file remote_ip:remote_file 
```

```shell
# 从远程复制到本地
scp root@www.runoob.com:/home/root/others/music /home/space/music/1.mp3 
scp -r www.runoob.com:/home/root/others/ /home/space/music/
```

## rm

```shell
rm [options] name...
```

- -i 删除前逐一询问确认。
- -f 即使原档案属性设为唯读，亦直接删除，无需逐一确认。
- -r 将目录及以下之档案亦逐一删除。

```shell
# 删除 一般文件
rm  test.txt 
# 删除 目录
rm  -r  homework  
# 删除当前目录下的所有文件及目录，并且是直接删除，无需逐一确认命令行为
rm  -rf  要删除的文件名或目录
```











