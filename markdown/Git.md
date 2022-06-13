#  Git本地管理及Github远程管理学习

## Git本地管理

### 1. Git本地管理

#### 1.1 创建版本库

初始化Git管理仓库

```shell
git init
```

添加文件到仓库

```shell
git add 
```

提交文件到仓库

```shell
git commit -m "description" # 添加说明，描述本次提交，便于后续管理
```

#### 1.2 时光机穿梭

仓库状态查看

```
git status
```

修改内容查看

```
git diff
```

##### 1.2.1 版本回退

历史记录查看

```
git log
```

版本回退

```
git reset --hard HEAD^ # 回退至上一个版本
git reset --hard HEAD^^ # 回退至上两个版本
git reset --hard HEAD～100 # 回退至上100个版本
git reset --hard commit_id # 回退至指定版本，如未来版本
```

HEAD指针指向版本号，对应每一次commit，通过查询git命令查询每一次commit的版本号。

```
git reflog
```

##### 1.2.2 工作区和暂存区

工作区指本地目录中可操作的文件区域；版本库为`.git`，其中包含暂存区`stage`（或index），还有不同的分支以及指向不同分支的指针，Git自动创建第一个分支`master`。

`git add`命令实际上是将要提交的修改放到暂存区stage，`git commit`命令将暂存区的所有修改一次性提交到分支。

##### 1.2.3 管理修改

Git管理的是修改，使用`git add`命令后，在工作区的第一次修改被放入暂存区，准备提交，`git commit`只负责将暂存区的修改提交，未放入暂存区的修改不会被提交。

##### 1.2.4 撤销修改

`git checkout -- <file>`或`git restore -- <file>`将file文件在工作区的修改全部撤销恢复，分为两种情况：

1. file修改后存放至暂存区，恢复至添加到暂存区之后的状态；
2. file修改后未存放至暂存区，恢复至版本库中的状态。

即恢复至最近一次`git add`或`git commit `时的状态。

`git reset HEAD <file>`将暂存区的修改撤销，重新放回工作区；此时暂存区无修改，工作区有修改，再使用`git restore -- <file>`丢弃工作区修改。

`git reset`既可以回退版本，也可以将暂存区的修改回退至工作区，HEAD表示最新的版本。

若已将修改提交至版本库，则使用版本回退功能回退版本。

##### 1.2.5 删除文件

在工作区删除了文件，`git status`会告知工作区与版本库的区别，显示哪些文件被删除。

```shell
rm <file>
git status
```

后续操作分为两种情况：

1. 需要从版本库中删除该文件

   ```shell
   git rm <file>
   git commit -m "remove <file>"
   ```

2. 文件为误删除，撤销删除操作

   ```shell
   git restore -- <file>
   ```

   `git restore`实际为使用版本库中的文件版本替换工作区文件，所以可用于文件修改与删除的撤销恢复。

   若文件从未保存至版本库，则无法使用该命令恢复；且只能恢复文件至最新版本，最近一次提交后修改的内容会丢失。

### 2. Github远程管理

#### 2.1 远程仓库

Git是分布式版本控制系统，同一个Git仓库，可以分布到不同的机器上，[GitHub](https://github.com/)网站提供Git仓库托管服务。

GitHub需要SSH Key，因为GitHub需要识别推送的提交者以防冒充，Git支持SSH协议，GitHub通过公钥确认推送者。

GitHub允许添加多个Key，在不同设备提交只需将每台设备的Key添加至GitHub。

1. 创建SSH Key。在用户主目录下，看是否有.ssh目录，是否有`id_rsa`和`id_rsa.pub`这两个文件，若已有，可直接跳到下一步；若没有，打开Shell创建SSH Key：

   ```shell
   ssh-keygen -t rsa -C "youremail@example.com"
   ```

   在用户主目录里找到`.ssh`目录，里面有`id_rsa`和`id_rsa.pub`两个文件，这两个就是SSH Key的秘钥对，`id_rsa`是私钥，不能泄露出去，`id_rsa.pub`是公钥，可以放心地告诉任何人。

2. 登陆GitHub，打开“Account settings”，“SSH Keys”页面，点“Add SSH Key”，填上任意Title，在Key文本框里粘贴`id_rsa.pub`文件的内容

##### 2.1.1 添加远程库

将本地仓库与Github仓库关联：

```shell
git remote add origin git@github.com:username/repository.git
```

远程库的名字为`origin`，Github默认名称。

将本地仓库内容推送至远程仓库，即将本地`master`分支推送至远程仓库：

```shell
git push -u origin master
```

远程仓库为空，使用`-u`参数将本地`master`与远程`master`分支关联，简化后续操作。

后续本地提交后推送至远程仓库：

```shell
git push origin master
```

删除远程库：

查看远程库信息：

```
git remote -v
```

根据名字删除，比如删除`origin`：

```
git remote rm origin
```

此处的删除实际为解除本地和远程的绑定关系，远程库本身没有任何改动。真正删除远程库，需要登录至GitHub进行删除。

##### 2.1.2 从远程库克隆



```
git clone git@github.com:username/repository.git
```

#### 2.2 分支管理

##### 2.2.1 创建与合并分支

##### 2.2.2 解决冲突

##### 2.2.3 分支管理策略

##### 2.2.4 Bug分支

##### 2.2.5 Feature分支

##### 2.2.6 多人协作

##### 2.2.7 Rebase



#### 2.3 标签管理

##### 2.3.1 创建标签

##### 2.3.2 操作标签
