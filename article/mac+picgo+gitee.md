平时经常使用typora记笔记，唯一的痛点就是上传图片比较麻烦，typora写markdown文档，图片必须保存在某个地方。

有2种方法可以解决图片保存地址：保存在本地 或 保存在服务端。

保存在本地意味着本地占用存储空间越来越大， 并且如果一不小心删除图片，就尴尬了。所以本文记录使用picgo将图片自动上传到gitee服务端（当然也可以有很多其他的服务端）

### 阿里云方式

gitee加了防盗链，无法使用图床了

https://blog.csdn.net/qq_40449773/article/details/121570688

https://ccs.zone/post/5ef77217.html

#### typora图片重命名

#### gitee图片迁移



### gitee新建存储图片仓库

1、点击新建仓库，仓库必须选择开源，否则文章无法访问到仓库的图片

<img src="https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-05-image-20220105180433688-1ee7040fe257eb6b9515a3160862e3ee-5b5.png" alt="image-20220105180433688" style="zoom:50%;" />

2、获取token

在设置-私人令牌-点击生成新令牌。⚠️需要记住这个令牌，后面需要用到

### picgo安装及配置

> 需要有node、npm环境

1、安装

```shell
# 全局安装picgo
npm install picgo -g
# 安装gitee插件
picgo install gitee-uploader
# 以下为选择安装
# 安装重命名插件
picgo install rename-file
```

2、配置

方法一：直接修改配置文件`~/.picgo/config.json`

```json
{
  "picBed": {
    "uploader": "gitee",
    "current": "gitee",
    "gitee": {
      "repo": "XXXXX/images", // 用户名/仓库名
      "branch": "master", // 分支
      "token": "", // 前文保存的token
      "path": "images", // 仓库名
      "customPath": "default",
      "customUrl": ""
    },
    "transformer": "path"
  },
  "picgoPlugins": {
    "picgo-plugin-rename-file": true, // 启用重命名插件
    "picgo-plugin-gitee-uploader": true
  },
  "picgo-plugin-rename-file": {
    "format": "{y}-{m}-{d}-{origin}-{hash}-{rand:3}" //重命名格式
  },
}
```

方法二：命名行修改

```json
# 进入修改配置
picgo set uploader

# 按上下键找到gitee，回车    
# 2.repo：用户名/仓库名  https://gitee.com/zhouzhz/images也就是这个地址后面的zhouzhz/images
# 3.token：上面生成的token
# 4.path:上面repo的仓库名，也就是https://gitee.com/zhouzhz/images也就是这个地址后面的images
# 5.custompath:默认回车   
# 6.customURL:默认回车

# 使用配置好的文件
picgo use uploader
# 选择gitee
```

重命名插件配置：https://github.com/liuwave/picgo-plugin-rename-file

命名规则：

- {y} 年，4位
- {m} 月，2位
- {d} 日期，2位
- {h} 小时，2位
- {i} 分钟，2位
- {s} 秒，2位
- {ms} 毫秒，3位(**v1.0.4**)
- {timestamp} 时间戳(秒)，10位(**v1.0.4**)
- {hash}，文件的md5值，32位
- {origin}，文件原名（会去掉后缀）
- {rand:<count>}, 随机数，<count>表示个数，默认为6个，示例：{rand：32}、{rand}
- {localFolder:<count>}, <count>表示层级 ，默认为1，示例：{localFolder:6}、{localFolder}

### typora配置

![image-20220105164317992](https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-05-image-20220105164317992-f69714c1f8dea06bfedcbbba86fbeb88-237.png)

command处表示使用node操作picgo的upload命令

```shell
# 获取node和picgo的安装路径
which node  # /usr/local/bin/node
which picgo # /usr/local/bin/picgo
```

所以command为：`/usr/local/bin/node /usr/local/bin/picgo upload`

### 测试配置是否成功

点击Test Uploader，出现 validation succeed则表示配置成功。

### 将文章中已有图片批量上传

使用typora打开文档，点击format-image-upload all local images。则可以一次性将文章中已经插入的图片上传到gitee仓库

参考：

https://cdmana.com/2022/01/202201010706311946.html

